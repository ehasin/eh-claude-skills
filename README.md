# eh-claude-skills

**Evgeni Hasin's top Claude Code skills** — a curated collection of custom [Claude Code](https://claude.com/claude-code) skills for real development workflows.

## Featured: `init-new-coding-project`

### The problem it solves

Every Claude Code chat has a context window, and every long-running project eventually outgrows it. The default coping mechanism — cramming more history into one giant chat, or re-explaining the project from scratch in a new one — doesn't scale. Two failure modes show up constantly:

- **Context inflation.** As a chat grows, more of the window is spent re-deriving things Claude already figured out three hours ago, leaving less room for the actual task.
- **Manual scope management.** Splitting work across chats usually means the developer becomes the state-sync layer — manually re-pasting status, decisions, and next steps into every new session.

This skill removes that overhead by moving persistent project state **out of the chat and into the repo.**

### How

Running `init-new-coding-project` sets up a small, deliberately-scoped documentation structure:

| File | Answers |
|---|---|
| `CLAUDE.md` | What are the rules and stack? (loaded every message — kept tight, ~120 lines) |
| `docs/PLAN.md` | Where are we going? |
| `docs/STATUS.md` | Where are we *now*? (overwritten, not appended — a snapshot, not a log) |
| `docs/CHANGELOG.md` | What happened, and why? |
| `docs/DECISIONS.md` | Why is it built this way, and what shouldn't get silently undone? |
| `docs/RUNBOOKS.md` | How do I do the thing we do occasionally but always forget the gotchas of? |

Because `STATUS.md` and `CHANGELOG.md` persist in the repo rather than in any one chat's context, a brand-new session — a throwaway task chat, a different feature branch, a different day — can read the current state in seconds instead of the conversation needing to carry it. That's the core trick: **task routing (which chat handles what) stops requiring manual context hand-off**, because the state was never only in the chat to begin with.

Git discipline is opinionated on purpose: the skill never auto-commits silently, but it also doesn't sit on a pile of uncommitted work waiting for permission. At a meaningful milestone it recommends a commit and push the way a senior engineer would — and push is the default outcome unless you object, not something you have to explicitly bless every time.

### Who it's for

- **Software developers** managing a real project across many short sessions, multiple branches, or a team — who want a lightweight, greppable source of truth instead of tribal chat history.
- **Vibe coders** who move fast across sessions and devices and don't want to re-explain the project every time — the docs carry the context so you don't have to.

### Install

Copy `skills/init-new-coding-project/` into your `~/.claude/skills/` (user-level, all projects) or a project's `.claude/skills/` (project-scoped), then invoke it in Claude Code with `/init-new-coding-project`.

## License

MIT — use it, fork it, adapt it.
