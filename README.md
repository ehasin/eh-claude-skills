# eh-claude-skills

**Evgeni Hasin's top Claude Code skills**: a curated collection of custom [Claude Code](https://claude.com/claude-code) skills for real development workflows.

| Skill | What it does |
|---|---|
| [`init-new-coding-project`](#init-new-coding-project) | Sets up a project so its state lives in the repo, not in chat history |
| [`test-user`](#test-user) | Tests your app as a real user in Chrome: sign-up, sign-in, email verification, Google sign-in, with JSON results |

## `init-new-coding-project`

### The problem it solves

Every Claude Code chat has a context window, and every long-running project eventually outgrows it. Cramming more history into one giant chat, or re-explaining the project from scratch in a new one, doesn't scale:

- **Context inflation.** As a chat grows, more of the window goes to re-deriving what Claude already figured out hours ago.
- **Manual scope management.** Splitting work across chats turns the developer into the state-sync layer, re-pasting status, decisions and next steps into every new session.

This skill moves persistent project state **out of the chat and into the repo**, and writes down how the owner and the agents work together.

### What it does

`/init-new-coding-project [name]` does two things and nothing else:

1. **Asks four questions** in one prompt: the repo (an existing link, or a new private or public one), the owner, the environments (prod, dev, local, or decide later) and the test policy (dev + prod, prod only, no tests, or decide later).
2. **Writes six docs and sets up git**, with `main` and `dev` branches and a `.gitignore` that keeps secrets out.

| File | Holds |
|---|---|
| `CLAUDE.md` | A one-line pointer to `AGENTS.md` |
| `AGENTS.md` | Rules for every agent: session start, where facts go, how deliveries and releases work, the report frame, and an initiatives table so parallel agents don't collide |
| `PROJECT.md` | Context, users, owner rules, UX rules, product rules and approved copy |
| `STATUS.md` | Open items only: a snapshot, not a log |
| `DEV_PLAN.md` | Architecture, hosting, test policy, release process, current release, latest releases and backlog |
| `GTM_PLAN.md` | Positioning, channels, launch and measurement (blank until you start it) |

A brand-new session can read the current state from these files in seconds. Which chat handles which task stops mattering, because the state was never only in the chat.

### How work flows

- **Docs stay current, not historical.** Outdated text is replaced, never appended; git holds the history. Each doc is updated in the same commit as the change it describes.
- **The owner decides the product; agents do the housekeeping.** Commits, pushes and doc upkeep happen without asking. Scope, product behaviour and production deploys wait for the owner.
- **Two branches only.** Deliveries are committed and pushed straight to `dev`. `dev` → `main` goes through a pull request with the release checks, only on the owner's go.
- **Tests match the stakes.** The chosen test policy decides what runs on each delivery and before each release.
- **Every report opens with the same three lines**: assignment done, project/git health, open issues.

### Who it's for

- **Developers** running a real project across many short sessions or several agents, who want a greppable source of truth instead of chat history.
- **Product owners and vibe coders** who direct the work without being engineers, and want Claude to handle the technical steps and bring them only product decisions.

### Install

Copy `skills/init-new-coding-project/` into `~/.claude/skills/` (all projects) or a project's `.claude/skills/` (one project), then run `/init-new-coding-project` in an empty project folder.

The default owner is the author. Pick **Someone else** at the owner question, or edit the default line in `SKILL.md` to make yourself the default.

## `test-user`

### The problem it solves

Unit tests and scripted browser tests don't cover the flows users hit first: signing up, confirming an email, "Sign in with Google". Those flows cross into real inboxes and Google's sign-in, which are slow and brittle to script. So they get clicked through by hand before each release, or not at all.

### What it does

`/test-user` turns Claude into a test user. It works in a dedicated Chrome profile that is signed in to a test Gmail account, and:

1. **Checks the app is clean** before starting: it finds anything earlier runs left behind, removes it, and refuses to test on a dirty state.
2. **Runs your scenarios** like a person would, written in plain Markdown: sign up with a unique `tester+<run>@gmail.com` address, open the verification email in Gmail, follow the link, sign in with Google, try a wrong password.
3. **Records evidence**: what it expected, what it saw, screenshots, console errors and failed network requests.
4. **Cleans up** every account, record and email it created, and checks that cleanup worked.
5. **Writes results** as timestamped JSON (`test-results/<runId>.json`, with a published schema) and a short Markdown summary.

Each scenario ends as `passed`, `failed`, `blocked` (something outside the app stopped it, like a Google "verify it's you" prompt) or `skipped`.

### Built-in limits

- Opens only the hosts you pass in. Production only if you list it.
- Never types the Google password. The profile is already signed in, and any verification prompt stops the scenario.
- No real payments. Generated passwords stay in a local, gitignored secrets file. Addresses are masked in the results.
- One Google account means one Google identity. To test a *new* Google user, your scenario's cleanup has to delete that user.
- It complements scripted end-to-end tests; it doesn't replace them. An AI clicking through can vary between runs.

### Setup

1. Create a Gmail account just for testing, and a Chrome profile signed in to it.
2. Install [Claude in Chrome](https://claude.ai/chrome) in that profile only.
3. Copy `references/config.example.env` to a private place outside your repos and fill it in.
4. Write scenarios in your project, e.g. `tests/user-scenarios/auth.md` ([format](skills/test-user/references/scenario-format.md)), and add `test-results/` to `.gitignore`.

### Run

Manually, in any project:

```
/test-user scenarios=tests/user-scenarios/auth.md hosts=localhost:3000 config=<path to your config>
```

From another agent or a script, add `trigger=agent` so it never stops to ask:

```
claude --chrome -p "/test-user scenarios=... hosts=... config=... trigger=agent"
```

It needs your Chrome running with the extension connected, so it runs on your machine, not on a CI server.

## Install

Copy a skill's folder from `skills/` into `~/.claude/skills/` (all projects) or a project's `.claude/skills/` (one project).

## License

MIT: use it, fork it, adapt it.
