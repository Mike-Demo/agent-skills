---
name: "agent-chat-ux"
description: "Make your agent communicate like a great conversationalist: emoji language, status reactions, progress bars, and interactive cards/buttons for choices. Use this skill whenever building or tuning an agent's chat UX, when writing agent reply guidelines, or whenever an agent needs to ask the user a question, show progress on long work, acknowledge a task, or confirm a consequential action — even if nobody explicitly asked for 'UX'."
---

# Agent Chat UX

How to make an agent feel smarter through the way it communicates: emojis used
with intent, reactions as a lightweight feedback loop, progress indicators for
long work, and interactive cards/buttons so the user taps instead of types.

These are UX patterns, not decoration. Each one exists to reduce friction: less
typing for the user, fewer ambiguous answers, visible progress on work that
takes a while, and acknowledgment that costs the user nothing.

## The core rule

**When the answer is bounded, offer it as a card. When the question is open, ask it in plain text.**

If the user can only reasonably reply "yes", "no", "tomorrow", or "option B",
do not make them type that. Give them buttons. Reserve free typing for answers
you genuinely cannot predict. Tapping is faster than typing, eliminates typos,
and removes the "did I phrase that right?" anxiety — which is why cards make an
agent feel smart even when the underlying model is unchanged.

## Emojis: a language, not confetti

Emojis work because they are scannable: a glance carries meaning that would take
a sentence. They fail when they become noise. The rule is seasoning, not soup —
one or two per message, in predictable positions, with a stable vocabulary so
the user learns what each one means.

### A suggested vocabulary

Keep it small and consistent across every conversation:

- **Progress bars** — see the "Progress bars" section below for the full set.
- **Status reactions** (react to the user's message, not just reply):
  - 👍 — received, starting on it (the universal "got it")
  - ⏳ or 🔄 — work in progress, still running
  - ✅ — done, delivered
  - ❌ — failed or blocked (pair with a short explanation)
  - 👀 — looking into it, investigating
  - 💡 — suggestion or idea, not a result
- **Message landmarks** in longer replies: 📋 plan, 🔍 findings, ⚠️ warning,
  🎯 decision needed, 📎 attachment, 🔗 link. Landmarks let the user skim.

### Progress bars

A progress bar answers "is it stuck?" at a glance. Pick one design and use it
everywhere — consistency is what makes it scannable. Designs adapted from
[Ben Smith's Emoji Progress Bar collection](https://bensomething.notion.site/4aa383a1dc2b4170aaf04a04825bd302).

**Full bars** — always 10 segments: filled first, then empty, then the number.
Ten segments keeps the math trivial (each one is 10%).

- `🟦🟦🟦🟦🟦⬜️⬜️⬜️⬜️⬜️ 50%` — squares. Neutral and clean; the best default.
- `🌝🌝🌝🌝🌝🌚🌚🌚🌚🌚 50%` — moons. Playful; fits a casual product.
- `🌳🌳🌳🌳🌳🌱🌱🌱🌱🌱 50%` — growing trees. Nice for staged or growing work.
- `😄😄😄😄😄😡😡😡😡😡 50%` — faces. Use sparingly: the 😡 reads as failure,
  not "empty", which confuses the message.

**Simple bars** — filled units only, no empty track. Compact, good for tight
spaces:

- `⭐️⭐️⭐️⭐️⭐️ 50%`
- `▰▰▰▱▱ 3/5` — text blocks; these render everywhere, including plain-text
  clients where emoji may not.

**Done state:** when the work finishes, retire the bar and use a single ✅ —
`✅ Nightly sync complete — 12,408 records, 0 errors`. The bar means
in-progress; the checkmark means done.

Rules of thumb: update at milestones (extract → transform → load → verify),
not on a timer. If the platform lets you edit a message in place, update one
message instead of sending five. Never stack multiple reactions on one bar
update — one signal per moment.

### Restraint and accessibility

- Never put information *only* in an emoji. Screen readers announce emoji by
  name ("check mark"), and some clients render them as boxes. The text must
  stand alone; the emoji is the highlight, not the content.
- Match the user's energy. If they write terse plain text, stay terse. If they
  bring warmth and emoji, you can too.
- Never celebrate bad news. A ❤️ or 🎉 on a message about a failure, a loss,
  or a frustration reads as mocking. Default to a calm acknowledgment.
- One reaction per message. Stacking 👍✅🎉 on a single user message looks
  frantic.

## Reactions as a feedback loop

A reaction is a message that costs the user nothing to receive and nothing to
interpret. Use them at the seams of work:

1. **Task received** → 👍 on the user's message immediately, then start working.
   The user knows they were heard without reading a word.
2. **Long work starts** → if the platform supports it, a ⏳ or a short "On it —
   this'll take a few minutes." Then go quiet and work.
3. **Work completes** → ✅ plus the actual result in a message. The reaction is
   the headline; the message is the detail.

This pattern is especially powerful for background or scheduled work: the user
sees at a glance what was picked up, what's running, and what finished, without
opening a single message.

## Cards and buttons: tap, don't type

### When to use a card

- Bounded choices: yes/no, pick one of N, approve/edit/cancel.
- Confirmations before consequential actions (send, publish, delete, pay).
- Repeated decisions the user makes often (triage queues, daily reviews).
- Multi-step flows: show "Step 2 of 4" so the user knows where they are.

### How to design the options

- Each button sends its exact label as the user's reply. Write labels as the
  natural thing the user would say: "Yes, send it" not "CONFIRM_SEND".
- One question per card. Two unrelated questions in one card force the user to
  answer both at once or neither.
- Two to four options is the sweet spot. More than that, use a list or ask in
  text.
- Always include the graceful exit: "Not now", "Skip", "Cancel". A card with no
  way out is a trap.
- For approvals, state exactly what will happen in the card body: what action,
  on what item, with what consequence. The button is the signature; the body is
  the contract.

### Platform fallbacks

Not every surface supports rich cards. Adapt without losing the pattern:

- **Rich chat (widgets, Block Kit, inline keyboards):** full cards/buttons.
- **Reactions available, no cards:** use numbered options in text
  ("Reply 1, 2, or 3") and acknowledge with a reaction.
- **Plain text (SMS, email):** emojis still work; cards don't. Numbered lists
  plus a clear "reply with the number" instruction.

Never let the lack of buttons push you back to open-ended questions for bounded
answers — a numbered list still beats "so what would you like to do?".

## Anti-patterns

- Asking a question with three obvious answers and no options, making the user
  type what you could have offered as buttons.
- Decorating every line with an emoji until nothing stands out.
- Sending a "just checking in!" message with no new information — if nothing
  changed, stay silent or react.
- Using 🎉/❤️/😂 on failure states, error messages, or frustrated user input.
- Hiding the only copy of a link, code, or identifier inside an emoji-laden
  sentence where it's hard to copy.

## Examples

Read `references/examples.md` for annotated before/after transcripts: a
long-running task with progress bar and reactions, a question converted from
free text to an options card, and an approval card for a consequential action.
