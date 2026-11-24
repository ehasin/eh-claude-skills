# Scenario file format

One Markdown file per app or area, kept in the project, e.g. `tests/user-scenarios/auth.md`. Plain words; write steps as a user would do them.

## Structure

```markdown
# Scenarios: <app name>

base_url: http://localhost:3000

## Clean-state checks
- <what must be true before a run, checked by looking at the app>

## Cleanup
- <how to remove what the scenarios create: UI steps, or a shell command run from the project root>

## S1: <title>
Tags: <comma-separated>
Steps:
1. <action>
2. <action>
Expect:
- <observable result>
```

Rules:

- Ids are `S1`, `S2`, … and stay stable, so results can be compared across runs.
- **Expect** lines must be things you can see: a URL, a text on the page, an email arriving. Add `No console errors` to make console errors fail the scenario.
- Cleanup commands get addresses with a `+` in them. URL-encode them (`curl --data-urlencode`, not `-d`), or the `+` becomes a space and the delete silently misses.
- **Clean-state checks** and **Cleanup** are shared by all scenarios in the file. Without them, the skill can't prove the app was clean; the run's `cleanState` is then `unknown`.
- Google sign-in creates the same user every run, because it's one Google account. A scenario that needs a *new* Google user only works if **Cleanup** deletes that user.
- Write `Accept the consent screen` in a step when the app's Google consent screen should be accepted.

## Placeholders

| Placeholder | Becomes |
|---|---|
| `{{email}}` | A unique address for this run and scenario: `<tester>+<runId><scenarioId>@gmail.com` |
| `{{email:S1}}`, `{{password:S1}}` | The address or password scenario S1 used, to reuse its account |
| `{{password}}` | A generated strong password, stored only in `<out>/.secrets/<runId>.env` |
| `{{google_account}}` | The tester's Google account |
| `{{base_url}}` | `base_url` from the file |
| `{{run_id}}` | The run id |

## Example

```markdown
# Scenarios: Acme Notes

base_url: http://localhost:3000

## Clean-state checks
- Signing in with Google as {{google_account}} lands on /onboarding, not /notes.

## Cleanup
- App accounts: sign in as the account, Settings → Delete account → type DELETE → Confirm.
- Or run: npm run db:delete-user -- <email>

## S1: Sign up with email and verify
Tags: signup, email
Steps:
1. Open {{base_url}}/signup
2. Enter {{email}} and {{password}}, submit
3. Open the verification email and follow its link
Expect:
- After submit, the page says "Check your inbox"
- The email arrives within 3 minutes
- The link lands on /welcome showing "You're verified"
- No console errors

## S2: Sign in with Google (new user)
Tags: signin, google
Steps:
1. Open {{base_url}}/login
2. Click "Continue with Google", choose {{google_account}}
3. Accept the consent screen
Expect:
- Lands on /onboarding
- The header shows the tester's name

## S3: Wrong password
Tags: signin, negative
Steps:
1. Open {{base_url}}/login
2. Enter {{email:S1}} and the password "wrong-password-1"
Expect:
- An error says the email or password is wrong
- Still on /login
```
