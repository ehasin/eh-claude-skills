# Copy standard

Words are interface. Every string is either helping the user act or getting in the way. Review every visible string, plus the emails and notifications you receive, against these rules. Each copy finding gives the current text, the rule it breaks and a rewrite.

## Principles

1. **Say what it does.** Plain, concrete words about what the user gets or what happens next. No mission statements.
2. **Fewer words.** Cut every word that doesn't change the meaning. Then cut again. Users scan; they don't read.
3. **The user's words, not ours.** No internal names, team jargon, database terms or engineering language ("entity", "workspace object", "sync job failed", "null").
4. **Talk like a person.** Write like a helpful expert speaking to a smart friend: direct, warm, never cute, never corporate.
5. **One name for one thing.** Pick a term and use it everywhere: if it's a "project" on one screen, it's not a "workspace" on the next.
6. **Never blame the user.** Errors are the product's problem to explain and help fix.
7. **Be honest.** No exaggeration, fake urgency or claims the product can't back up.

## Rules by element

### Buttons and links
- Verb + object, saying exactly what happens: "Create invoice", "Send 3 invites", "Delete project". Not "Submit", "OK", "Yes", "Continue", "Click here".
- The button on a confirmation dialog repeats the action: "Delete project", not "Yes".
- Link text makes sense out of context: "See pricing", not "Learn more" (unless what to learn is obvious from what's right next to it).
- The same action has the same label everywhere.

### Headings and headlines
- Say what's on the screen or what the product does. "Invoices", "Send invoices in 30 seconds", not "Your financial command center".
- A landing headline that could sit on any other product's site fails.

### Errors
Formula: **what happened + what to do next**, in plain words, next to the problem.
- Bad: "Oops! Something went wrong." / "Error 422" / "Invalid input" / "An unexpected error has occurred."
- Good: "That card was declined. Try another card or contact your bank." / "Passwords need at least 12 characters."
- No "Oops", "Uh-oh", "Whoops", no exclamation marks, no jokes when the user has lost something.
- Don't say "please" in every message; do say "sorry" when the product is at fault and it cost the user something.

### Empty states
- Say what will appear here and how to make it appear, with one button: "No invoices yet. Create your first one and it'll show up here. [Create invoice]".
- Never a bare "No data" or "Nothing to see here 👀".

### Success, toasts and confirmations
- Confirm the specific thing that happened: "Invoice sent to Dana", not "Success!".
- Offer undo where possible: "Project deleted. [Undo]".

### Form labels, hints and placeholders
- Labels are short nouns ("Work email"); hints explain format or why it's asked ("We'll send the receipt here").
- Placeholders show an example ("dana@company.com"), never repeat the label, never carry instructions that vanish on typing.
- Mark the exception: if most fields are required, mark the optional ones.

### Onboarding and help text
- Show, don't tell: one sentence, then let the user do it.
- If a screen needs a paragraph of help text to be understood, the screen is the problem; file it under the relevant category too.

### Confirmation dialogs
- Title asks the real question with consequences: "Delete 'Q4 Plan'? This removes 12 files for everyone." Not "Are you sure?".

### Notifications and emails
- Subject says what happened or what's needed: "Dana commented on Q4 Plan". Not "You have a new notification from Acme!".
- From name is recognisable; an unsubscribe or settings link is present.

## Mechanics

- **Capitalisation:** sentence case for UI (buttons, headings, menus) unless the product has clearly chosen title case; either way, consistent. Mixed is a finding.
- **Punctuation:** no full stops on buttons or one-line labels; no exclamation marks except for real celebration (once, rarely); no ALL CAPS for emphasis.
- **Numbers and dates:** digits not words ("3 files"), localized formats, relative times where helpful ("2 min ago"), correct plurals ("1 file", "2 files").
- **Spelling and grammar:** zero typos. One typo on a landing page or in a price is High.
- **Consistency:** same term, same spelling, same tone everywhere (including emails and error pages).
- **Length:** button labels 1-3 words; error messages 1-2 short sentences; tooltips one line.
- **Reading level:** aim for what a busy 12-year-old would understand at a glance, unless the audience is truly specialist, and even then, no needless jargon.

## AI-slop and filler: banned or suspect

Any of these is a finding. On a landing page or onboarding screen, rate it High.

**Words and phrases**
unlock · unleash · elevate · empower · supercharge · revolutionize · transform (as a vague verb) · seamless / seamlessly · effortless / effortlessly · streamline · leverage · harness · robust · cutting-edge · next-level · game-changer · world-class · best-in-class · state-of-the-art · powerful (as the only claim) · intuitive (claiming it doesn't make it so) · all-in-one · one-stop · dive in · delve · embark · journey (for a sign-up) · tapestry · landscape · realm · "in today's fast-paced world" · "say goodbye to" · "look no further" · "take X to the next level" · "the future of X" · "built for the modern X" · "your X, reimagined" · "designed with you in mind"

**Patterns**
- "Not just X, it's Y." / "It's not about X. It's about Y."
- "Whether you're a X or a Y, …"
- Three adjectives or three benefits in a row, on every line.
- Em-dash chains and colon-heavy taglines in every heading.
- Emoji as bullets or in every heading (🚀 ✨ 💡 🔥).
- Headline-subhead pairs where the subhead repeats the headline in longer words.
- Generic praise of the user ("Great question!", "You're all set to crush it!").
- Exclamation marks on routine events ("Settings saved!").
- Vague benefits with no numbers or specifics ("Save time", "Boost productivity") where a concrete one is available ("Send an invoice in 30 seconds").
- "Welcome to <Product>!" as the main heading of the first screen after sign-up.
- Every CTA says "Get started".

## Rewrite examples

| Current | Problem | Rewrite |
|---|---|---|
| "Unlock the power of seamless collaboration" | Slop, says nothing | "Edit docs together, in real time" |
| "Submit" | Doesn't say what happens | "Create account" |
| "Oops! Something went wrong. Please try again later." | Cute, vague, no way forward | "We couldn't save your changes. Check your connection and try again; your text is still here." |
| "No data" | Dead end | "No customers yet. Import a CSV or add one by hand. [Add customer]" |
| "Are you sure?" [Yes] [No] | Hides consequences | "Delete 'Q4 Plan' for everyone?" [Delete project] [Cancel] |
| "Invalid email" | Blames, doesn't help | "Enter an email like name@company.com" |
| "Success!" | Says nothing | "Invite sent to dana@company.com" |
