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
8. **The design speaks first.** Text that explains how to use a screen is a sign the screen failed.

## Text load: words standing in for design

The best interface needs almost no words. Apply the **delete test** to every screen and dialog: imagine every instruction, hint, tooltip and help paragraph gone. Can the persona still do the task?

- **No:** the screen depends on its text. File one COPY finding for the screen, quote the text it leans on, and make the fix a design change (show the state, make the control say what it does, use defaults, teach by doing), not a rewrite. High on a core screen or in onboarding; Medium elsewhere.
- **Yes, but the text is still there:** it's noise that every user pays for on every visit. Cut it. Medium if it's a block of more than one sentence, Low for a single line.
- Rules, tutorials and "how it works" text that the product only works with are the same failure, wherever they live.

Probe P7 counts the words per screen. On a task screen (not a content, legal or marketing page), more than about 40 words of explanation, or any block over 2 sentences, is a reason to run the delete test hard. A count is a pointer, never a finding by itself.

## Punch

UI copy is scanned, not read. Write so the first two words carry the meaning.

- **Front-load.** The word the user is scanning for comes first: "Delete project?" not "Are you sure you want to delete this project?". "Saved 2 min ago" not "Your changes were successfully saved 2 minutes ago".
- **One idea per sentence.** UI sentences stay under 15 words, and a block holds at most 2 sentences. Over 20 words is a finding.
- **Active and concrete.** A real subject and a real verb: "We couldn't save your changes", not "Saving could not be completed". Verbs, not nouns: "Configure alerts", not "Alert configuration".
- **Say it once.** If the heading, the body and the button say the same thing, keep the one that does the job and cut the rest.
- **No throat-clearing.** No "Here you can…", "This page shows…", "Welcome to…", "In this section…". Start with the content.
- **No hedges or filler.** Cut: simply · just · easily · quickly (as filler) · basically · actually · very · really · please note · note that · in order to · at this time · currently (when obvious) · feel free to · make sure to · it's important to · allows you to · enables you to · lets you (when the verb alone works) · utilize · functionality · kindly · stacked "please".

Flabby copy is Medium per root cause, and High when it runs through the landing page, onboarding or a core flow. Every punch finding comes with the tightened rewrite.

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
- If a screen needs a paragraph of help text to be understood, the screen is the problem: it fails the delete test (see Text load).

### Confirmation dialogs
- Title asks the real question with consequences: "Delete 'Q4 Plan'? This removes 12 files for everyone." Not "Are you sure?".

### Notifications and emails
- Subject says what happened or what's needed: "Dana commented on Q4 Plan". Not "You have a new notification from Acme!".
- From name is recognisable; an unsubscribe or settings link is present.

### Legal pages (imprint, privacy policy, terms, cookie notice)
- Still copy: same language as the UI, headings, short paragraphs, a date of the last update.
- A plain-language summary at the top of the privacy policy: what's collected, why, who else gets it, how to delete it. A wall of legalese with no summary is Medium.
- What they say must match what the product does. See TRUST in the framework for what to check.

## Consistency and contradictions

Judge these across the whole app, not screen by screen. Keep the string inventory (probe P7, one section per screen) and read it end to end before writing findings.

- **Glossary.** List each core concept (the things the user creates, the currency, the main actions, the plan names) and every name used for it, with where. More than one name for a core concept is High; for a minor one, Medium. The report carries the glossary with the one name to keep.
- **Contradictions.** Two places that state different facts: limits, prices, counts, rules, dates, what's free. Help or rules text that doesn't match what the product actually does. Promises the product breaks ("No ads" with ads, "Saved automatically" when it isn't, "Free" behind a paywall). High in a core flow; Critical when it's about price, data or consent; Medium elsewhere.
- **Voice.** One form of address (in German, `du` or `Sie`, never both), one tone, one capitalisation style.
- **Language.** The UI is in one language, the one `<html lang>` declares. Mixed languages on a screen, or a `lang` that doesn't match the text, is a finding.

## Mechanics

- **Capitalisation:** sentence case for UI (buttons, headings, menus) unless the product has clearly chosen title case; either way, consistent. Mixed is a finding.
- **Punctuation:** no full stops on buttons or one-line labels; no exclamation marks except for real celebration (once, rarely); no ALL CAPS for emphasis.
- **Numbers and dates:** digits not words ("3 files"), localized formats, relative times where helpful ("2 min ago"), correct plurals ("1 file", "2 files").
- **Spelling and grammar:** zero typos, judged in the page's own language and its conventions (German nouns capitalised, „quotes“, decimal comma). One typo on a landing page or in a price is High. Proofread the whole inventory once, slowly, at the end. If a language is one you can't proofread reliably, say so under "Coverage and limits" instead of guessing.
- **Consistency:** same term, same spelling, same tone everywhere (including emails, error pages and accessible names that only screen readers hear).
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
