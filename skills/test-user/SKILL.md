---
name: test-user
description: Act as a real end user and test a web app in a dedicated Chrome profile, following written scenarios - sign up, sign in, email verification through the profile's Gmail inbox, and "Sign in with Google" - then write timestamped JSON results. Use when the user or an agent runs /test-user, asks for user tests, end-to-end tests by a test user, a sign-up or sign-in check, or wants an app clicked through like a real user. Requires Claude in Chrome connected to the tester's Chrome profile.
---

# test-user

You are a test user. You open the app in the tester's Chrome profile, follow each scenario like a real person, record exactly what happened, clean up after yourself, and write the results.

This skill is generic and public. Everything specific to a person or a project comes in as input. Never write unmasked account addresses, passwords, tokens or private URLs into this skill, into chat, or into the JSON results.

## Inputs

The caller (a person typing `/test-user`, or another agent) passes:

| Input | Required | Meaning |
|---|---|---|
| `scenarios` | yes | Path to a scenario file (see [references/scenario-format.md](references/scenario-format.md)), or scenarios inline in the same format |
| `hosts` | yes | Allowed hosts, comma-separated, e.g. `localhost:3000,staging.example.com`. Nothing outside this list is touched, except Google's sign-in pages and Gmail |
| `config` | yes | Path to a local env file with the tester identity (see [references/config.example.env](references/config.example.env)) |
| `out` | no | Results folder. Default `test-results/` in the project root |
| `only` | no | Scenario ids or tags to run, e.g. `S1,S3` or `tag:signup` |
| `label` | no | Short name for this run |
| `trigger` | no | `manual` (default) or `agent`. With `agent`, never ask questions: anything that would need a person makes the scenario or run `blocked` |

If a required input is missing: with `trigger=manual`, ask for it once; with `trigger=agent`, stop and write a result with `status: "error"` naming the missing input.

Example:

```
/test-user scenarios=tests/user-scenarios/auth.md hosts=localhost:3000 config=C:/Users/me/.claude/test-user/config.env
```

## Hard rules

1. **Allowed hosts only.** Before opening any URL, check its host against `hosts`. Google sign-in (`accounts.google.com`) and Gmail (`mail.google.com`) are allowed only for the tester account. A link in an email or on a page that points elsewhere is recorded, not opened. Production is tested only if the caller listed it.
2. **Never type the Google password.** The Chrome profile is already signed in. "Sign in with Google" means picking the tester account in Google's chooser and, if the scenario says so, accepting the app's own consent screen. A password prompt, captcha, "verify it's you" or 2-step prompt means the scenario is `blocked`: stop it and move on.
3. **No real money.** Payments only in a provider's test mode with its published test cards, and only when a scenario asks for it.
4. **Secrets stay local.** Generated app passwords go only into `<out>/.secrets/<runId>.env`. The results show the tester address masked (`ab***yz@gmail.com`).
5. **No browser dialogs.** Don't trigger `alert`/`confirm`/`prompt`. If a step needs one (e.g. a delete confirmation), warn in the results, and if the browser stops responding, mark the scenario `blocked`.
6. **Report what happened, not what should have happened.** If you didn't see it, it isn't `passed`.

## Run

### 1. Preflight

1. **Timestamp.** Get the start time from the shell in ISO 8601 with offset (PowerShell `Get-Date -Format o`, POSIX `date -Iseconds`). Never guess the time. `runId` = `tu` + start time as `yyyyMMddTHHmmss`, e.g. `tu20261124T153000`.
2. **Config.** Read the `config` file. Don't echo its values.
3. **Project.** Record the git commit, branch and whether the tree is dirty (`git rev-parse HEAD`, `git status --porcelain`). If `<out>` is not gitignored, say so in `issues` (don't edit `.gitignore` yourself).
4. **Hosts.** Check every URL in the scenarios against `hosts`. Any scenario using a host not on the list is `skipped` with the reason.
5. **Browser.** Load the Claude in Chrome tools (see the `chrome-browser` skill if available). Call `tabs_context_mcp`. If several browsers are connected and `TEST_USER_CHROME_DEVICE_ID` is set, `select_browser` with it; otherwise follow the tool's instructions to let the person pick. Open a new tab.
6. **Identity check.** Open `https://mail.google.com/mail/u/0/#inbox`. The page title must contain `TEST_USER_GMAIL`. If it doesn't, or Gmail asks to sign in, stop the whole run with `status: "blocked"`: wrong browser or profile.
7. **Clean-state check.** Look through every `*.json` in `<out>`. For every artifact whose `cleanup` is not `cleaned` or `not-needed`, take its real identifiers from that run's secrets file and run the scenario file's **Cleanup** steps for it now; once it's verified gone, set its `cleanup` to `cleaned` in that old file, with a `note` naming this run. Then run the scenario file's **Clean-state checks**. Record the outcome in `preflight.cleanState`:
   - `clean`: nothing was left over and the checks pass.
   - `cleaned`: leftovers were found and removed; checks pass.
   - `dirty`: leftovers or failing checks you couldn't fix. **Stop the run** with `status: "blocked"`. Never test on a dirty state.
   - `unknown`: no earlier results and the scenario file has no checks. Continue, and note it.

### 2. Scenarios

Run each scenario in file order (filtered by `only`). For each:

1. Record `startedAt` from the shell. Call `read_console_messages` and `read_network_requests` once with `clear: true` on the scenario's tab: tracking only starts with the first call, so anything before it is lost.
2. Fill placeholders: `{{email}}` = `<local>+<runId><scenarioId>@<domain>` from `TEST_USER_GMAIL` (all lowercase, e.g. `tester+tu20261124t153000s1@gmail.com`; if the app rejects `+`, note it and fail the step); `{{password}}` = 16 random characters with upper, lower, digit and symbol, saved to the secrets file; `{{email:Sn}}` / `{{password:Sn}}` = what scenario Sn used (if Sn didn't run, this scenario is `skipped`); `{{google_account}}` = the tester account; `{{run_id}}`; `{{base_url}}`.
3. Do the steps like a user: look at the page (`read_page`, `find`, screenshots), click and type what a person would. Prefer `browser_batch` for predictable sequences. Use `form_input` for fields.
4. After a click or submit that should change the page, wait until the URL or content actually changes (check every 1–2 s, up to 10 s) before judging; reading too early sees the old page. Then compare what you see with the step's expectation. Record `actual` in plain words. A screenshot (`save_to_disk: true`) goes in `evidence` for every failed or blocked step, and for the final state of each scenario. Copy each saved screenshot to `<out>/<runId>/<scenarioId>-<step>.jpg` (the tool saves to a temp folder) and record that path.
5. **Email verification.** Open Gmail in a separate tab and search `to:{{email}} newer_than:1d`. Poll: wait 10 s, refresh, up to 3 minutes. Open the newest match, read the link or code, and use it only if its host is allowed. No email within 3 minutes = step `failed` with "no email received".
6. **Google sign-in.** Click the app's Google button. In Google's chooser click the tester account. Accept the consent screen only if the scenario says so. Anything asking for a password or verification = `blocked` (rule 2).
7. Collect `read_console_messages` (`onlyErrors: true`, pattern `.`) and `read_network_requests` for the allowed hosts; keep errors and responses with status ≥ 400.
8. Any artifact you create (an app account, a record, an email) goes into `artifacts` right away, with `cleanup: "pending"` and a masked description. Its real identifiers (address, password, record id) go into the secrets file, so a later run can clean it up.
9. Status: `passed` if every expectation was seen; `failed` if any expectation was contradicted; `blocked` if something outside the app stopped you (Google verification, missing email permission, dialog, browser lost); `skipped` if not run. A console error fails the scenario only if the scenario's **Expect** says "no console errors"; otherwise it's recorded.
10. If a step fails, stop that scenario and go to the next. Don't retry a failing action more than twice.

### 3. Cleanup (always)

Run this even when scenarios failed or the run was stopped:

1. For each `pending` artifact, do the scenario file's **Cleanup** steps (UI steps, or shell commands written in that file; run commands from the project root, nothing else). A command's exit code is not proof: check in the app that the artifact is gone. Set `cleanup` to `cleaned`, `failed` (with why) or `manual` (needs a person).
2. In Gmail, move this run's emails to Trash: search for every `{{email}}` used in the run (`to:a OR to:b …`), select all, Trash. Never empty the Trash.
3. Close every tab you opened.
4. Run the **Clean-state checks** again and record them in `postCleanup`.

### 4. Results

1. Write `<out>/<runId>.json` following [references/results.schema.json](references/results.schema.json). Times are ISO 8601 with offset, from the shell.
2. Write `<out>/<runId>.md`: a short summary for people. One line per scenario (status, title, the reason if not passed), then leftovers that need a person.
3. Overall `status`: `error` (inputs or setup broken), else `blocked` (run stopped in preflight), else `failed` if any scenario failed, else `blocked` if any blocked, else `passed`.
4. Return to the caller: the overall status, the counts, each non-passed scenario in one line, any `manual` cleanup, and the two file paths. Nothing else.
