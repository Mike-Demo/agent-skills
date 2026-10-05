# Job-search mode

A specialization of the mental-health-support skill for job trackers, status
updates, and employer messages. All core principles from `SKILL.md` apply —
especially: facts stay facts, display is not source, no false comfort.

## Status-label reframing

Application trackers repeat the same loaded words dozens of times. The skill
can reframe them in the *displayed view* using neutral, factual language. The
mappings below are **examples, not mandatory replacements** — the user defines
their own, and `plain_mode` users may prefer the literal originals.

| Original (example) | Neutral alternative (example) |
|---|---|
| Rejected | Application closed |
| Rejection email | Application update |
| Failed | Not selected for this role |
| We regret to inform you | Update on your application |
| Not a fit | Not moving forward |

Rules:

- **User customization wins.** Never assume "rejection" affects everyone the
  same way. Ask which words bother *this* user before applying any mapping.
- **The mapping is display-only.** The tracker's underlying status value is
  untouched. If the user later exports or shares the tracker, the original
  values are what leave the system.
- **Every reframed label is marked** as transformed, with the original
  retrievable ("shown with softer wording — original: 'Rejected'").

## Digest summaries

Instead of a stream of individual pings, offer batched factual summaries at
the user's chosen cadence:

- "Three applications changed status."
- "Two employers sent updates."
- "Four applications remain active."

Then list the items neutrally on request. Digests report *counts and facts* —
never interpretations ("good news" / "bad news"), never streaks, never
rankings.

## Check-in cadence

Let the user choose how often they review updates: hourly, twice daily,
daily, or manual. Outside those windows, hold non-urgent updates silently.
Pair with quiet hours from the core config. The point is to break the
compulsive-check loop, not to hide information — everything is still there at
the next check-in.

## Fidelity rules for employer messages

- **Never reinterpret.** "We decided to move forward with other candidates"
  means exactly that — not "they might reconsider," not "it's about fit."
- **Ambiguous updates stay ambiguous.** "We'll be in touch soon" → report as
  "Employer said they will be in touch; no timeline given." Do not convert
  vagueness into reassurance *or* dread.
- **Silence is not data.** No reply after N days is "No response yet" — never
  "They're not interested."
- **Separate the decision from the person.** Alternatives describe the
  *application's* state, never the candidate's worth. No language that equates
  outcomes with personal value.

## What this mode never does

- No gamification (streaks, scores, leaderboards for applications).
- No productivity pressure ("You should apply to 5 more today").
- No shame framing ("You missed 3 follow-ups").
- No auto-rewriting of the user's own notes or journal entries — only
  user-selected content.
- No changing email subjects or tracker values in the source system.
