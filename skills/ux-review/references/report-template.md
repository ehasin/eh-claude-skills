# Report template

Write `<out>/<runId>/report.md` in this shape. Keep it scannable: tables and short lines, no essays. Leave out a section only if it would be empty, and say so in one line.

```markdown
# UX review: <product name>

<app url> · <env> · <date, from the shell> · run <runId>
Persona: <persona> (given | assumed)
Tasks: <task 1>; <task 2>; <task 3> (given | assumed)
Covered: <N> screens · desktop 1440×900 · mobile 390×844 · keyboard pass <yes/partial/no> · signed in <yes/no>
Build: <commit and branch, if repo given>

## Verdict: <Not ready | Fix first | Ship it> · <score>/100

**<Critical count> Critical · <High> High · <Medium> Medium · <Low> Low · <passed>/<tested> categories pass**

<2-4 sentences. The blunt truth about how this product feels to the persona right now, and the single biggest thing standing between it and great. No warm-up, no compliments.>

## Scorecard

| Category | Score | Result | C | H | M | L | In one line |
|---|---|---|---|---|---|---|---|
| FIRST · First impression | 6 | ❌ Fail | 0 | 2 | 1 | 0 | Headline says nothing; two competing CTAs |
| ... all 15 categories, in framework order; untested ones show "–" and "Not tested: <reason>" |

## Top 5 fixes

The five changes that would most change how the product feels. One line each, with the finding IDs.

1. **<Imperative fix>**: <why it matters in one clause> (FLOW-02, FEED-01)
2. ...

## Task walkthroughs

| Task | Result | Steps | Clicks | Pages | Waits > 1 s | Dead ends | Time |
|---|---|---|---|---|---|---|---|
| Sign up | Done with friction | 6 | 11 | 3 | 2 | 1 | 2 min 40 s |

<For each task, 2-5 lines: where the persona hesitated or got stuck, with finding IDs.>

## Findings

Grouped by category, in framework order. Within a category, most severe first.

### FIRST · First impression · 6/10 · ❌ Fail

#### FIRST-01 · High · The headline doesn't say what the product does
- **Where:** / (landing, above the fold, desktop and mobile)
- **Evidence:** "Elevate your workflow with intelligent automation" · shots/01-landing.png
- **Why it hurts:** a first-time visitor can't tell in 5 seconds whether this is for them; most leave.
- **Fix:** Say the job and the result: "Turn customer emails into tickets automatically."
- **Effort:** S

<Repeat for every finding. Tags after the severity where they apply: `inferred`, `source-only`, `verify on prod build`, `new`, `still open`, `regressed`.>

## Copy rewrites

Every copy problem found, in one table, so it can be handed straight to whoever owns the words.

| ID | Where | Current | Problem | Rewrite |
|---|---|---|---|---|
| COPY-01 | Sign-up button | "Submit" | Doesn't say what happens | "Create account" |

## Action items

Checklist by severity. Each line: fix, ID, effort.

### Critical: fix before anything else
- [ ] <fix> · <ID> · <S/M/L>

### High: this cycle
- [ ] ...

### Medium: soon
- [ ] ...

### Low: polish
- [ ] ...

## Measurements

| Screen | TTFB | LCP | CLS | Slowest interaction | Requests | Transfer |
|---|---|---|---|---|---|---|
| / | 210 ms | 1.9 s | 0.02 | 90 ms | 42 | 1.2 MB |

Accessibility probe summary: <contrast failures, unnamed controls, unlabeled fields, small targets, per screen, short>.
Console errors: <count and the worst, short>. Failed requests: <count and the worst, short>.

## What works

At most 3 bullets, only for things that are actually excellent. Leave the section out if nothing is.

## Since the last review

Only if a previous run was loaded: <fixed N · still open N · regressed N · new N>, then the regressed and still-open IDs in one line each.

## Coverage and limits

- Not tested: <areas and why: read-only env, behind sign-in, blocked by captcha, out of depth limit>
- No screen-reader pass was done (unless one was).
- Performance numbers are from one session on <env>; field data may differ.
- Left behind: <test data that couldn't be deleted, or "nothing">
```

## findings.json

Write `<out>/<runId>/findings.json` so a later run (or another agent) can compare and track. Keep it to these fields:

```json
{
  "runId": "uxr20261124T153000",
  "app": "https://staging.example.com",
  "env": "staging",
  "startedAt": "2026-11-24T15:30:00+01:00",
  "finishedAt": "2026-11-24T16:12:00+01:00",
  "commit": "a1b2c3d",
  "persona": "small-shop owner, first visit",
  "tasks": ["sign up", "add first product", "share store link"],
  "verdict": "Fix first",
  "score": 64,
  "categories": [
    { "id": "FIRST", "score": 6, "result": "fail", "counts": { "critical": 0, "high": 2, "medium": 1, "low": 0 } },
    { "id": "PERF", "score": null, "result": "not-tested", "reason": "read-only env" }
  ],
  "findings": [
    {
      "id": "FIRST-01",
      "category": "FIRST",
      "severity": "high",
      "title": "The headline doesn't say what the product does",
      "where": ["/"],
      "evidence": "\"Elevate your workflow with intelligent automation\"",
      "screenshots": ["shots/01-landing.png"],
      "fix": "Say the job and the result: \"Turn customer emails into tickets automatically.\"",
      "effort": "S",
      "tags": [],
      "status": "new"
    }
  ],
  "leftBehind": []
}
```

`severity`: `critical` | `high` | `medium` | `low`. `result`: `pass` | `fail` | `not-tested`. `status`: `new` | `still-open` | `regressed` (and, in the comparison list only, `fixed`). Match findings across runs by category, location and what the problem is, not by ID, because IDs are numbered fresh each run.
