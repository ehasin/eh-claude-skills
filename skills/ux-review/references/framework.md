# UX review framework

15 categories. Each one has the question it answers, what to check, and examples that are Critical or High on their own. The bracketed `[N#]` tags map to Nielsen's 10 usability heuristics; WCAG references are to WCAG 2.2 AA.

## Severity

| Level | Meaning | Examples | Fix by |
|---|---|---|---|
| **Critical** | Blocks a core task, loses or exposes data, misleads the user into a wrong or harmful action, or locks out a group of users | Sign-up fails; Save silently drops changes; keyboard trap; destructive action with no confirm and no undo; price shown differs from price charged | Before shipping anything else |
| **High** | Real friction or confusion for many users, a frequent workaround, or something that makes the product look broken or untrustworthy | Primary action hard to find; error message with no way forward; 4 s wait with no feedback; layout breaks on mobile; copy that reads like AI wrote it on the landing page | This cycle |
| **Medium** | Noticeable annoyance, inconsistency or slowness that users tolerate but feel | Two different words for the same thing; spacing inconsistent across screens; no empty state, just a blank table | Soon |
| **Low** | Polish. A sharp user notices, most don't | Misaligned icon by 2 px; missing favicon; title case in one menu item | When touching that area |

Raise a level when an issue is everywhere, on the first screen a new user sees, or in the money path (sign-up, checkout, upgrade). Lower a level only if the affected screen is rarely seen **and** the cost is small.

**Owner scope.** When the owner has declared a platform or input method out of scope (the `context` input), findings that only affect it are tagged `owner-scope`, capped at Medium and don't drive the verdict. They are still reported with the standard they miss. Everything else in this framework applies as written.

## Scoring a category (0-10)

| Score | Means |
|---|---|
| 10 | Nothing to fix. Holds up next to the best product in its class. Rare. |
| 9 | Only Low findings. Great. |
| 8 | A few Mediums, no High. Solid, but not yet great. |
| 6-7 | One or two Highs, or many Mediums. Users feel it. |
| 4-5 | Several Highs, or one Critical. Clearly rough. |
| 2-3 | Pervasive problems or multiple Criticals. |
| 0-1 | Broken or missing entirely. |

**Pass rule:** a category passes only with a score of **8 or more, zero Critical and zero High**. Pass means ship-grade by this bar, not "acceptable".

**Overall score (0-100):** the weighted average of the tested categories' scores × 10, rounded. Weights: FLOW 3; FIRST, COPY, VIS, FEED, ERR, PERF, A11Y 2; all others 1.

**Verdict** (`owner-scope` findings don't count here):
- **Not ready**: any Critical anywhere.
- **Fix first**: no Critical, but at least one category fails.
- **Ship it**: every tested category passes.

---

## 1. FIRST: First impression and clarity

*Within 5 seconds, does a new user know what this is, who it's for and what to do next?*

- The headline says what the product **does**, in plain words, not what it "empowers". [N2]
- One primary call to action stands out; it says what happens when clicked.
- The page title, favicon and social preview make sense.
- Above the fold isn't wasted on decoration, cookie banners or a carousel.
- What you guessed during the cold open matches what the product really is.

Critical / High on their own: you couldn't tell what the product does (High, Critical if it's the landing page of a paid product); the primary action is missing or buried (High).

## 2. ONBOARD: Onboarding and empty states

*How fast does a new user get to the first moment of real value?*

- Steps from arrival to first value, counted. Every step before value has to earn its place.
- Sign-up asks only for what's needed now. No credit card, phone or company size unless truly necessary.
- No forced tour of tooltips. Learning happens by doing.
- Empty states explain what will appear here and offer the one action that fills them, ideally with an example or sample data.
- The user lands somewhere useful after sign-up, not on an empty dashboard.

Critical / High: sign-up or first sign-in fails (Critical); empty dashboard with no next step (High); more than one blocking screen before value with no skip (High).

## 3. NAV: Navigation and information architecture

*Can users find things and always know where they are?*

- The current location is always clear (active nav state, page title, breadcrumbs where deep). [N1]
- Labels use the user's words, not internal team names. [N2]
- Things that belong together live together; nothing important is more than 2 levels deep.
- Back, refresh and deep links work; URLs reflect the state worth sharing. [N3]
- There's a way to search when content exceeds what fits in a menu.
- 404 and dead-end pages lead somewhere useful.

Critical / High: browser Back breaks the app or loses work (High, Critical if data is lost); a core feature only reachable by guessing (High).

## 4. FLOW: Core task flows

*Can the persona do their top tasks quickly, without thinking about the interface?*

- Steps, clicks and fields per task are as few as possible; you've counted them.
- Sensible defaults, remembered choices, smart prefill. Never ask twice for something the app already knows. [N7]
- Recognition over recall: options visible, no need to remember things from a previous screen. [N6]
- Primary action placed where the eye ends up, and consistent across screens. [N4]
- Shortcuts for frequent users (keyboard shortcuts, bulk actions) that don't get in a newcomer's way. [N7]
- No dead ends; after finishing, the next likely step is offered.

Critical / High: a top task can't be completed (Critical); a top task takes twice the steps it should, or needs a workaround (High).

## 5. COPY: Copy and microcopy

*Does every word earn its place, sound human and help the user act?*

Apply [copy-standard.md](copy-standard.md) in full. Every copy finding includes the exact current text and a rewrite.

Critical / High: copy that misleads about price, data, consequences or consent (Critical); error messages that don't say what happened and how to fix it (High); AI-slop or marketing filler on the landing page or onboarding (High); jargon or internal terms in core flows (High).

## 6. VIS: Visual design and hierarchy

*Does the eye go where it should, and does it look designed rather than assembled?*

- One clear focal point per screen; hierarchy through size, weight and space, not more colors and boxes.
- A real type scale (few sizes, consistent), readable body size (16 px on web is the floor for body text), line length of about 50-80 characters.
- A spacing system: consistent gaps, alignment to a grid, related things close together, unrelated things apart.
- Restrained color with meaning: one accent for actions, semantic colors for status, never color alone to carry meaning.
- Icons from one family, one style, one weight, with consistent sizes.
- Visual density matches the job (a data tool can be dense; an onboarding screen can't).

Critical / High: the primary action looks less important than secondary ones (High); text overlapping or cut off (High); several competing focal points on a key screen (High).

## 7. FEED: Feedback and system status

*Does the user always know what's happening, and that their action worked?* [N1]

- Every action gives a visible response within 100 ms (pressed state, spinner, optimistic update).
- Anything over 1 s shows progress; over 10 s shows what's happening and lets the user leave and come back.
- Success is confirmed where the user is looking, not in a toast in the far corner only.
- Skeletons or placeholders match the final layout (no jump when content arrives).
- Unsaved changes are clear; leaving with unsaved changes is caught.
- Real-time state (sync, online/offline, collaborators) is shown when it matters.

Critical / High: user can't tell if Save worked (High; Critical if it silently fails); long waits with a frozen screen (High); double submission possible because nothing changed after the first click (High).

## 8. ERR: Errors, edge cases and recovery

*When something goes wrong, does the product prevent it, explain it and help the user recover?* [N5] [N9]

- Prevention first: constraints, defaults and confirmation of destructive actions; better still, undo instead of "Are you sure?". [N3] [N5]
- Error messages say what happened, why (if useful) and what to do next, in plain words, near where the problem is. [N9]
- Errors never lose the user's input.
- Network failures, timeouts and server errors are handled with a clear message and a retry, not a blank screen or raw stack trace.
- Edge content is handled: long names, emoji, empty lists, huge lists, missing images, zero and negative numbers, other languages.
- Permission and limit states (plan limits, no access) explain the situation and the way forward.

Critical / High: a destructive action with no confirm and no undo (Critical); input lost on an error (High, Critical for long forms); raw technical errors shown to users (High); blank screen on failure (High).

## 9. FORM: Forms and input

*Is entering information as effortless as it can be?*

- Only fields that are needed; optional ones marked as optional (or removed).
- Visible labels above fields; placeholders are examples, never the only label.
- Correct input types and attributes (`email`, `tel`, `numeric`, `autocomplete`) so mobile keyboards and autofill help.
- Validation inline, after the user leaves the field, with messages that say how to fix it; never only on submit, never while the user is still typing the first character.
- Password rules shown up front; show-password toggle; paste allowed.
- Sensible tab order, Enter submits, focus moves to the first error.
- Long forms broken into steps with visible progress, or one-page with grouping.

Critical / High: paste blocked into password or code fields (High); a form can't be submitted and the reason isn't visible (Critical); placeholder-only labels (High); validation that clears fields (High).

## 10. PERF: Speed and perceived performance

*Does it feel instant?*

Measure with probe P2. Thresholds (Core Web Vitals, rated at the 75th percentile in the field; you are measuring one session, so treat numbers as indicative):

| Metric | Good | Needs work | Poor |
|---|---|---|---|
| LCP (Largest Contentful Paint) | ≤ 2.5 s | ≤ 4 s | > 4 s |
| INP (Interaction to Next Paint) | ≤ 200 ms | ≤ 500 ms | > 500 ms |
| CLS (Cumulative Layout Shift) | ≤ 0.1 | ≤ 0.25 | > 0.25 |
| TTFB (Time to First Byte) | ≤ 0.8 s | ≤ 1.8 s | > 1.8 s |

- Navigation between screens feels instant (prefetching, client transitions, cached data).
- No layout jumps as fonts, images or ads load.
- No spinner for something that could have been shown immediately.
- Page weight and request count proportionate to what's on screen.

On `local` and `dev`, unoptimised builds are expected to be slow: cap PERF findings at Medium unless the problem is clearly architectural (e.g. 300 requests, a 10 MB bundle, a waterfall of sequential API calls), and add "verify on a production build" to each.

Critical / High: a core screen takes > 4 s to become usable on a normal connection (High); layout shift moves a button as the user clicks it (High).

## 11. RESP: Responsive and mobile

*Is the mobile experience designed, or just squeezed?*

- No horizontal scrolling; nothing cut off at 390 px wide.
- Tap targets at least 24×24 px (WCAG 2.5.8), ideally 44×44 px, with space between them. Inline text links are exempt from the minimum.
- Primary actions within thumb reach; sticky bars don't cover content or inputs.
- Text readable without zooming (16 px body); zoom not disabled.
- The on-screen keyboard doesn't hide the field being typed in or the submit button.
- Tables, charts and wide content adapt (stack, scroll inside a container, or summarise).

Critical / High: a core task can't be done on mobile (Critical); horizontal page scroll or overlapping UI (High); zoom disabled (High).

## 12. A11Y: Accessibility (WCAG 2.2 AA)

*Can people using a keyboard, screen reader, zoom or with low vision use it?*

Use probes P3, P4 and P5. Check at least:

- Text contrast ≥ 4.5:1 (≥ 3:1 for large text: 24 px, or 18.66 px bold); UI component and focus indicator contrast ≥ 3:1. (1.4.3, 1.4.11)
- Everything works with the keyboard alone, in a logical order, with no traps. (2.1.1, 2.1.2, 2.4.3)
- Focus is always visible and never hidden behind sticky headers. (2.4.7, 2.4.11)
- Images have alt text; icon-only buttons have accessible names. (1.1.1, 4.1.2)
- Form fields have labels; errors are announced and described in text. (1.3.1, 3.3.1, 3.3.2)
- Page has a `lang`, a meaningful title and a heading structure; landmarks used. (3.1.1, 2.4.2, 1.3.1)
- Content reflows at 320 px width / 400% zoom without loss. (1.4.10)
- Motion respects `prefers-reduced-motion`; nothing flashes.
- Target size minimum 24×24 px. (2.5.8)
- No drag-only interactions without an alternative. (2.5.7)
- Sign-in doesn't require solving a puzzle or remembering a code without paste or a password manager. (3.3.8)

Automated probes find maybe a third of real issues. Report what the probes found and what you checked by hand, and say clearly that a screen-reader pass wasn't done unless it was.

Critical / High: keyboard trap or core task impossible by keyboard (Critical); unlabeled form fields or icon buttons in core flows (High); body text below 4.5:1 contrast (High); focus invisible (High).

## 13. CRAFT: Consistency and craft

*Does it feel like one product made by people who sweat the details?* [N4]

- The same thing has the same name, icon, color and position everywhere.
- Components behave the same everywhere (all modals close the same way, all tables sort the same way).
- All interactive states are designed: hover, focus, active, disabled, loading, selected.
- Details: favicon, page titles per screen, sensible 404, no placeholder or test content, dates and numbers formatted for the user's locale, plurals right ("1 items" is a finding), no broken images, no double scrollbars, no flash of unstyled content.
- Transitions are short (150-300 ms), purposeful and consistent; nothing animates just to animate.
- Native browser dialogs (`alert`, `confirm`) are not used.
- Dark mode, if offered, is designed rather than inverted.

Critical / High: placeholder, lorem ipsum or test data visible to users (High); inconsistent terminology for core concepts (High).

## 14. TRUST: Trust, respect and honesty

*Does the product treat the user as an adult and earn their trust?*

- No dark patterns: no confirm-shaming ("No thanks, I hate saving money"), pre-ticked opt-ins, hidden costs, roach-motel cancellation, fake urgency or fake scarcity, nagging.
- Prices, trials and renewals are stated plainly before the user commits.
- Permissions (notifications, location, camera) asked only when needed, with the reason.
- Cookie consent is honest: reject is as easy as accept.
- Privacy and data use explained in plain words where data is collected.
- Notifications and emails are useful, rare and easy to turn off.
- Signs of a real company: who's behind it, how to get help, working contact.

Critical / High: hidden costs or deceptive consent (Critical); cancel or delete account harder than sign-up (High); permission prompt on first load (High).

## 15. SLOP: Taste and originality

*Does it look and read like someone with taste made it, or like a template an AI filled in?*

Signs of slop. Each one is at least a Medium finding, High when it's on the landing page or onboarding, or when there are several of them:

**Visual**
- The default look of a component library or starter template, unmodified (stock shadcn/Tailwind UI/Bootstrap look, default shadows, default radius on everything).
- Purple-to-blue gradients, gradient headline text, glowing blobs, glassmorphism for no reason.
- Three identical feature cards with an icon, a two-word title and a vague sentence.
- Emoji used as icons or in headings and bullets.
- Sparkle icons and "✨ AI-powered" badges.
- Stock 3D illustrations, generic hero mockups, placeholder avatars, fake logos or fake testimonials.
- Everything centred, everything in a rounded card, everything the same weight.
- Dashboards full of decorative charts and made-up metrics that don't help a decision.
- Random icon styles mixed; icons that don't mean anything.

**Copy** (see the slop list in [copy-standard.md](copy-standard.md))
- Filler verbs: unlock, elevate, empower, supercharge, seamlessly, effortlessly.
- "Not just X, it's Y", "Whether you're X or Y", triplets everywhere, em-dash chains.
- Headlines that could be on any product's site.

**Product**
- Features that exist because they were easy to generate, not because a user needs them (a chat box bolted on, settings nobody needs).
- AI output shown without the user being able to check, edit or undo it.

Ask: could you swap the logo for a competitor's and nobody would notice? If yes, that's a High finding.
