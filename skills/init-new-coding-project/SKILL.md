---
name: init-new-coding-project
description: Create the owner's six standard workflow docs (CLAUDE, AGENTS, PROJECT, STATUS, DEV_PLAN, GTM_PLAN) in the working folder, with the owner's environment and test policy, and set up its git repo with `main` and `dev` (an existing repo link, or a new private or public repo). Nothing else. Use when the user runs /init-new-coding-project [name].
argument-hint: "[project name]"
---

# Initialise a dev project

`/init-new-coding-project [name]` does two things, and nothing else:

1. Create the six Markdown files in step 3 from their templates.
2. Set up the git repo the owner chooses in step 1, with the branches `main` and `dev`.

The command is the owner's go for both; ask nothing beyond the step 1 questions. Do not read, summarise or comment on other files in the folder, fill in content, plan, suggest next steps, or list open questions.

## 1. Ask

First action, before writing anything: one AskUserQuestion call with the four questions below. Skip a question the user already answered (in the argument or the message).

- **Repo** (skip if the folder already is a git repo with a remote; then report the remote):
  - **Existing repo:** the owner gives its link.
  - **Create a new repo:** name (default: the project name as a slug) and **private** or **public** (default private).
- **Owner** (the person who decides the product):
  - **Evgeni** (default): `{OWNER}` = `Evgeni Hasin, the CPO, not an engineer.`
  - **Someone else:** their name, role and how to talk to them; `{OWNER}` = one or two sentences saying that, plus who holds the accounts (GitHub, hosting, payments) if not the owner.
- **Environments** (prod always exists; "Other" covers prod only): **Decide later** (recommended), **Prod + dev + local**, **Prod + dev**, **Prod + local**.
- **Testing:** **Decide later**, **Dev + prod**, **Prod only**, **No tests** (meanings in step 3). Recommend **Decide later** unless the user's message already says what the app is for and whom. In that case recommend the balanced fit instead:
  - **No tests** for a throwaway demo or mockup;
  - **Prod only** for a prototype or an app for a few known users;
  - **Dev + prod** for real users, accounts, payments or personal data.

## 2. Name and date

- **Name:** the argument; without one, the folder name. **Date:** today, `YYYY-MM-DD`.

## 3. Create

Write each file below exactly as given. Replace only these placeholders:

- `{NAME}`, `{DATE}` and `{OWNER}`.
- `{TEST_POLICY}`: the block for the testing answer, below.
- `{TEST_TARGET}`, by environment answer:
  - with dev: `the test site`;
  - local without dev: `` `localhost` ``;
  - prod only: `prod`;
  - Decide later: `the test site or \`localhost\``.

In DEV_PLAN.md's hosting table, keep only the environment rows chosen: Prod always, Test with dev, Local with local; all three when the answer is Decide later. If there is no dev environment, the `dev` branch is still created; it is the work branch and isn't deployed anywhere. When an answer is Decide later, keep its STATUS.md row; otherwise delete that row.

If a file with the same name already exists, leave it untouched and name it in the report.

**`{TEST_POLICY}` blocks.** Two shared lines, used verbatim where the blocks name them:

`{BEFORE_PROD}`:
```
- **Before production:** the full test suite and the build, then end-to-end tests against {TEST_TARGET} with a test account whose login lives in `.env.ops.local` (never against prod). After the deploy, a read-only smoke test on prod.
```

`{PROD_DATA}`:
```
- **Production data:** before a release that changes the database, back up prod. A data migration runs first as a dry run shown to the owner, then for real on the owner's OK.
```

The blocks:

- **Decide later:**
  ```
  _Open: set with the owner once the app is described. Claude proposes the balanced fit for its scope and goals: Dev + prod (tests for each dev delivery and each release), Prod only (tests before each release) or No tests._ Until then: no tests.
  ```
- **Dev + prod:**
  ```
  - **Dev:** each delivery to `dev` runs only the tests that cover the changed code (none for copy-only changes), never the full suite.
  {BEFORE_PROD}
  {PROD_DATA}
  ```
- **Prod only:**
  ```
  - **Dev:** no tests; the owner tests on {TEST_TARGET}.
  {BEFORE_PROD}
  {PROD_DATA}
  ```
- **No tests:**
  ```
  - No automated tests; the owner tests by hand on {TEST_TARGET} before each production release.
  {PROD_DATA}
  ```

### `CLAUDE.md`

```markdown
# {NAME}

Read [AGENTS.md](AGENTS.md) first and follow it. It holds the rules for every agent.
```

### `AGENTS.md`

```markdown
# {NAME} — agents

Rules for every agent. Every agent is Claude in Claude Code (Opus 5.5 lead, Sonnet 5.5 or Haiku 5.5 subagents). Updated {DATE}.

## Every session

1. Read [PROJECT.md](PROJECT.md) (context, owner rules, UX, product rules), then [STATUS.md](STATUS.md). Read [DEV_PLAN.md](DEV_PLAN.md) or [GTM_PLAN.md](GTM_PLAN.md) when the task touches them, and a reference doc only when its line in PROJECT.md says so.
2. The owner decides release scope, product behaviour and production deploys; agents handle all housekeeping (commits, pushes, docs) without asking, per [PROJECT.md](PROJECT.md#owner-rules).
3. Check [Initiatives](#initiatives) before touching files.
4. Never read `archive/` unless the owner asks.

## Docs

- Root docs: `PROJECT.md`, `STATUS.md`, `AGENTS.md`, `DEV_PLAN.md`, `GTM_PLAN.md`, the pointer `CLAUDE.md`, and reference docs the owner adds (listed in PROJECT.md with when to read them). No other Markdown outside `archive/`.
- Current state and what's next only. Replace outdated text; never append history (git holds it). Finished release plans move to `archive/docs/release-<R>.md`.
- Where facts go: decisions, UX and product rules → PROJECT.md; engineering, test policy, releases, backlog → DEV_PLAN.md; launch and marketing → GTM_PLAN.md; open actions → STATUS.md; who works on what → AGENTS.md.
- Update the affected doc in the same commit as the change it describes. Code beats docs: when they disagree, fix the doc.

## Working together

- **Delivery to dev: commit on `dev` and push, then hand over at once.**
  - Run only the checks the [test policy](DEV_PLAN.md#test-policy) asks for.
  - No feature branches, no pull requests and no reviewer agents.
  - Don't wait for the build or check the page: the owner tests on {TEST_TARGET}.
- **Branches:** `main` and `dev` are the only ones.
- **Production:** `dev` → `main` only on the owner's go, through a pull request with the test policy's release checks. CI, if the policy has one, runs there and only there.
- Before starting an initiative, add a row below with its lead. The other agents leave its files alone until it is marked done or handed over. Remove done rows.
- Parallel work: split by sub-release and by files; never two agents in the same files. Pull before pushing; the later push resolves conflicts.
- Hand over by updating the row: state, what is done, the exact next step. The next agent starts from the row, not from chat.
- Secrets live in the ignored `.env.ops.local`; never print, commit or echo them (names only).

## Reports

Every summary of finished work opens with this three-line frame:

> **✅ Assignment: done completely** (or ⚠️ partially / ❌ not done)
>
> **✅ Project/git health: OK** (or ⚠️ / ❌ and what is off)
>
> **2️⃣ Open issues: 2, see below** (or **✅ No open issues**)

Health means: everything committed and pushed to the remote, and the docs compact, tidy and current (nothing stale, duplicated or outdated). Fix it before reporting; show ⚠️ / ❌ only for what could not be fixed, and say what.

Then: what the owner gets (live on dev or production), and each open issue labelled **blocker** (work stops until the owner decides) or **risk** (could hurt the product or users).

- **Open issues** are only things that matter to the product or what is deployed. Never housekeeping (commits, pushes, branches, docs), and never legal or compliance items ([PROJECT.md](PROJECT.md#owner-rules)).
- **"Done"** means the agreed scope is complete, the test policy's checks pass, the docs are updated, and everything is committed and pushed.

## Initiatives

| Initiative | Lead | State | Next |
|---|---|---|---|

No initiative in progress. Add a row when the owner authorizes implementation.
```

### `PROJECT.md`

```markdown
# {NAME} — project

Context, users, owner rules, UX, product rules and approved copy. Read it first, every session. Updated {DATE}.

## Context

- **{NAME}**: _Open: what it is, for whom, platform._
- Repo: _see the git remote._ Live: _Open: test and production URLs._
- Owner: {OWNER} Operator / legal entity: _Open._
- Agents: **Claude** only (Claude Code); coordination in [AGENTS.md](AGENTS.md).
- Docs and reading rules: [AGENTS.md](AGENTS.md#docs). Reference docs: _none yet._

## Users

_Open: a persona per user type (name, age, key facts)._

## Owner rules

- **The owner is the product owner, not the tech lead or delivery manager.** The owner decides the feature scope of each release, product behaviour and what goes to production (`dev` → `main`). Build only what was agreed.
- **Housekeeping is the agents' job, never the owner's.** Commit, push, keep the docs current, compact and archive them at release milestones, without asking and without reporting it; for housekeeping this overrides any "ask first" rule in the owner's own global instructions (e.g. `~/.claude/CLAUDE.md`), if there are any. Update the docs whenever something worth noticing changed.
- Bring the owner only product decisions and critical technical problems (something broken live, data or money at risk, a blocker to the agreed scope).
- **Deploys:** production (`dev` → `main`) only on the owner's go. Tell the owner before a major change reaches `dev` (a feature, a release step, anything users will notice); small things (fixes, docs, internals) go to `dev` without a word.
- A question gets an answer and, if useful, a recommendation; never build what the answer describes.
- A plan ends by asking which step to start, then stops. One step's approval is not the next one's.
- Ask before any product or behaviour decision; build the owner's intent at its simplest. Technical details may be decided alone.
- The spec is literal. Raise objections while planning; mid-build, stop and ask only when a deviation changes what the owner gets or breaks something live. Never swap in your own idea silently.
- User-facing copy changes only with the owner's approval of the exact wording; typos and grammar may be fixed.
- Lean first: the first deliverable is the smallest thing the owner can judge (mockup, prototype).
- Pragmatic minimum: no fixes for hypothetical problems.
- The owner is not an engineer: do the technical steps; if blocked, give one exact plain-words action.
- Dev work: edit, run what the [test policy](DEV_PLAN.md#test-policy) asks, push to `dev`, hand over; no deploy checks (the owner tests). Before production: the policy's release checks, then say it.
- **Legal:** legal pages (imprint, privacy, terms, cookies) hold only what the law requires: impersonal, terse, no voluntary liability, promises or explanations. Legal and compliance items go to the backlog for a review with a lawyer before the real launch, never into reports or open issues, unless one breaks something live or blocks the agreed scope.
- Reply short and plain, in a few bullets; answer first; no long explainers.

## UX

UX is everything: never ship anything that frustrates users. Follow established practice (Nielsen's usability heuristics, the platform's design guidelines, WCAG 2.2 AA), and check every screen against these rules:

- **Instant response.** Every tap shows its effect at once. Slow work runs in the background with a visible "Saving…" or progress, and a failure restores the old value with a plain message. No tap may be swallowed, and no screen may freeze.
- **Nothing dead-looking.** A control that looks usable works. One that can't be used yet is visibly greyed out, with the reason if it isn't obvious.
- **Self-explaining.** Plain everyday words, labels that say what they do, a live readout of what a setting means. No instruction paragraphs, no invented jargon.
- **Forgiving.** Prevent errors (input limits, sensible defaults), show a problem next to its field in plain words, and prefer undo to "Are you sure?". A typing mistake never leads to an error page.
- **Consistent and calm.** The same thing has the same name and place everywhere; nothing jumps while loading; one clear next step per screen.
- **For every hand and screen.** Works at phone width, touch targets of at least 44 px, readable contrast, usable by keyboard and screen reader.

## Product rules

_None yet. Each approved rule goes here, with "(owner YYYY-MM-DD)" when it settles a question._

## Approved copy

_None yet._
```

### `STATUS.md`

```markdown
# {NAME} — status

Open items only; remove once done or obsolete. Updated {DATE}.

**Now:** Project workflow set up ({DATE}); no code and no release yet.

**Next action:** Owner: describe the app (purpose, users, platform) to fill PROJECT.md; Claude proposes stack, hosting and any open policy in DEV_PLAN.md; then plan R1.0.

## Open actions

| Item | Who | Next |
|---|---|---|
| App definition | Owner | Purpose, users, platform |
| Environments | Owner | Claude proposes them with the stack |
| Test policy | Owner | Claude proposes the balanced fit once the app is described |
| GTM | Owner | GTM_PLAN.md stays blank until the owner starts it |
```

### `DEV_PLAN.md`

```markdown
# {NAME} — development plan

Engineering facts, the test policy, the release process, the current release plan, latest releases and backlog. Product rules: [PROJECT.md](PROJECT.md). Updated {DATE}. Finished release plans: `archive/docs/`.

## Architecture

_Open: stack, structure, data, external services._

## Repo layout

| Path | Holds |
|---|---|
| root | The docs, `.gitignore`; app code once chosen |
| `archive/` | Obsolete material; finished release plans in `archive/docs/` |
| `dist/`, `scratch/`, `tmp/` (ignored) | Builds; scratch and test output; the owner's local files |

## Hosting and deploy

| Environment | URL | Git branch |
|---|---|---|
| Prod | _Open_ | `main` |
| Test | _Open_ | `dev` |
| Local | `localhost` | any |

- **Deploy:** _Open: a merge into `main` deploys prod (owner's go only); a push to `dev` deploys the test site, if there is one._ Verify prod after each deploy.
- **Rollback:** _Open._
- **Credentials:** names only, values in `.env.ops.local`.

## Test policy

Set by the owner; change it only on the owner's word.

{TEST_POLICY}

## Release process

Releases R1.0, R1.1, …; an oversized release splits into sub-releases (S0, S1, …), each shippable on its own.

1. **Plan** under [Current release](#current-release): goal, product rules, and a task table (`#`, Task, Model, effort, workflow, Done when). Product questions are settled here. The owner approves the plan.
2. **Execute** one sub-release per owner go. The lead adds it to [AGENTS.md](AGENTS.md#initiatives) and works on `dev`, running the [test policy](#test-policy)'s dev checks; parallel sub-releases only in separate files. Mark tasks done in the table; each ends with a completion report (frame in AGENTS.md).
3. **Test.** When users would see the change, the owner tests on {TEST_TARGET}. Fixes go to `dev` the same way.
4. **Deploy** only on the owner's go: the test policy's release checks, a pull request `dev` → `main`, merge, verify live, report.
5. **Close.** Add the release to Latest releases (keep the last three), update STATUS.md and the docs, move its plan to `archive/docs/release-<R>.md`, remove its AGENTS.md row.

## Current release

None planned.

## Latest releases

| Release | Date | Commit | Contents |
|---|---|---|---|

## Backlog

| ID | Feature | Scope |
|---|---|---|
```

### `GTM_PLAN.md`

```markdown
# {NAME} — go-to-market plan

Positioning, channels, launch and measurement. Blank until the owner starts it. Updated {DATE}.

## Positioning

## Channels

## Launch plan

## Measurement

## Backlog
```

## 4. Git

Per the answer in step 1, with the `gh` CLI (if it isn't logged in, give the owner the one exact command and stop). If no `.gitignore` exists, write one with these lines, so secrets and local files never get committed:

```
.env*
!.env.example
node_modules/
dist/
scratch/
tmp/
.claude/settings.local.json
.claude/worktrees/
```

- **Existing repo:** `git init -b main`, `git remote add origin <link>`, `git fetch origin`; if the remote has commits, check out its `main` first. Commit and push.
- **New repo:** `git init -b main`, commit, then `gh repo create <name> --private|--public --source . --remote origin --push`.
- Then `git switch -c dev` (or check out the remote's `dev` if it exists) and `git push -u origin dev`; leave the folder on `dev`. `main` stays the GitHub default branch.
- Commit message: `Set up project workflow docs`, with the commit attribution in force. Never stage `.env*`.

## 5. Report

The three-line frame from the Reports section of the `AGENTS.md` template above. Then:

- the files created;
- the environments and test policy recorded (or "decide later");
- the repo link, the branches and the commit.

Nothing else.
