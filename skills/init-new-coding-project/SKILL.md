---
name: init-new-coding-project
description: Set up persistent cross-session project documentation (CLAUDE.md plus docs/PLAN, STATUS, CHANGELOG, DECISIONS, RUNBOOKS) so future Claude Code sessions start cold and still work efficiently. Use when initialising a new software project, or when an existing project's docs need restructuring for short-session, low-token workflows.
---

# Initialise persistent project documentation

Establish a documentation structure that lets future Claude Code sessions work
without relying on conversation history. The user runs many short sessions —
throwaway task chats and per-section chats — so **the files are the memory**.
Every session starts cold and must be able to reconstruct project state quickly
and cheaply.

## 1. Inspect before writing

- Read the repository structure, existing `CLAUDE.md`, README, and any docs.
- Identify the stack, architecture, entry points, and the build/run/test/lint
  commands. Verify them by running them rather than assuming.
- Do not modify application code. This task is documentation only.

## 2. The structure

### `CLAUDE.md` (repo root)

The operating manual, loaded into **every message of every session** — so every
line is paid for repeatedly. Keep it tight; a hard cap of roughly 120 lines is a
useful discipline.

Contains: project purpose and high-level architecture; stack; conventions and
constraints; build/test/lint commands; repo-specific rules; the invariants that
must never be broken; the documentation rules from §3; one-line pointers to the
files below.

It is **not** a history. Detail lives in `docs/` and is read on demand.

### `docs/PLAN.md` — where we are going

Objective, scope, architecture, major components, roadmap and phases, planned
steps, assumptions, open questions, decisions still to make. Forward-looking
only: no status, no history.

### `docs/STATUS.md` — where we are now

Current objective, what is done, what is in progress, immediate next steps,
known issues and blockers, important technical state, current branch and latest
relevant commit. Optimised for a cold session that needs the picture in thirty
seconds. Overwrite freely — this is a snapshot, not a log.

### `docs/CHANGELOG.md` — what happened

Per meaningful change: date, what was delivered, commit hash when applicable,
why, whether it followed the plan or deviated, and any consequence or follow-up.

A few lines per milestone. Never a transcript: no per-command narration, no
conversational detail, nothing trivial.

### `docs/DECISIONS.md` — why it is the way it is

Per decision: the decision, rationale, alternatives considered where relevant,
and the consequences or trade-offs. Only decisions worth preserving — the ones a
future session might otherwise undo without realising what it was undoing.
Create the file only if such decisions exist.

### `docs/RUNBOOKS.md` — repeatable procedures

Step-by-step for operations that recur but are needed only occasionally:
deploying, seeding, adding data, creating a user, rotating a credential. Too
long for `CLAUDE.md`, wrong for `PLAN.md`. Include the traps each procedure has
already sprung.

## 3. Documentation rules to state in `CLAUDE.md`

Write these into `CLAUDE.md` so every future session follows them:

1. Read `CLAUDE.md`, then `docs/PLAN.md`, then `docs/STATUS.md`.
2. Read `docs/CHANGELOG.md` when historical context matters.
3. Read `docs/DECISIONS.md` before making or revisiting an architectural choice.
4. Read `docs/RUNBOOKS.md` before a deploy, a data change, or any repeatable op.
5. Inspect the relevant code before modifying it.
6. Update `STATUS.md` after meaningful work; overwrite rather than append.
7. Add a `CHANGELOG.md` entry after meaningful delivered changes.
8. Update `PLAN.md` when the agreed plan materially changes.
9. Record architectural decisions in `DECISIONS.md`.
10. Keep it concise. When something becomes obsolete, **change or delete it** —
    do not append around it.

State plainly: **the documentation is the persistent memory; conversation
history is not.**

## 4. Preserve what exists

Consolidate rather than overwrite. Move existing knowledge into whichever file
now owns it, and keep hard-won instructions — especially anything recording a
mistake already made. Leave exactly one source of truth per fact.

## 5. Populate honestly

Fill each file from what you actually established by inspection. Where something
is unknown, say so in `PLAN.md` under open questions rather than inventing it.
`CHANGELOG.md` starts with the baseline and anything delivered during this
initialisation.

## 6. Keep context cheap

Optimise for fast startup, low token cost, accurate reconstruction, and minimal
duplication. Maintain a clear separation: **plan** (future), **status** (now),
**changelog** (past), **decisions** (why), **runbooks** (how).

## 7. Git discipline

Do not commit or push automatically on every change, and do not leave
everything uncommitted either. **Recommend a commit and push at meaningful
milestones**, the way a senior engineer would: when a coherent unit of work is
finished and verified, say what you would commit/push and why. Pushing is the
default outcome — proceed unless the user objects, rather than waiting for
explicit go-ahead each time. Record the hash in `CHANGELOG.md`, and in
`STATUS.md` where it helps orient the next session.

## 8. Finish by reporting

- What you learned about the project.
- The structure you created.
- Assumptions, gaps, and decisions still needing the user's input.

Do not begin implementation work unless asked.
