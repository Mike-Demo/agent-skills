# Agent Chat UX — Examples

Annotated before/after transcripts. Read this when you want to see the
SKILL.md patterns applied to concrete conversations.

## 1. Long-running task: progress bar + reactions

**Before** — the agent goes silent for six minutes, then dumps a result:

> User: Run the full test suite and fix anything that breaks.
> *(six silent minutes)*
> Agent: Done. 3 failures fixed: auth token refresh, CSV export encoding, flaky
> retry test. Full report attached.

The user had no idea whether the agent was working, stuck, or dead.

**After** — reactions mark the seams, a progress bar makes the work visible:

> User: Run the full test suite and fix anything that breaks.
> Agent: 👍 *(reaction on the user's message — "received, starting")*
> Agent: On it — running the full suite now, I'll report back.
> *(a few minutes later)*
> Agent: 🟩🟩🟩🟩⬜ Suite green except 3 failures — fixing now (4/5).
> Agent: ✅ *(reaction)* All green. Fixed: auth token refresh, CSV export
> encoding, flaky retry test. Full report attached.

Why it works: the 👍 answers "did you hear me?" instantly, the progress bar
answers "is it stuck?", and the ✅ marks the handoff.

## 2. Bounded question: card instead of free text

**Before** — open question for a closed answer:

> Agent: I found 3 suitable roles. How would you like to proceed?

The user has to invent the reply: "apply to all?", "show me details?", "just
the first one?" — and might phrase it in a way the agent misreads.

**After** — the options are the reply:

> Agent: I found 3 suitable roles. What should I do with them?
>
> [ Apply to all three ] [ Show me details first ] [ Just the top match ]
> [ Not now ]

Why it works: zero typing, zero ambiguity. Each button sends its label as the
user's message, so the agent receives a clean, parseable answer every time.
Note the graceful exit ("Not now") — every card needs one.

## 3. Approval card for a consequential action

**Before** — the agent asks in prose and hopes the user is precise:

> Agent: Should I go ahead and send the follow-up email to the recruiter?

"Yes" could mean anything; "yes but change the subject line" is a trap.

**After** — the card is the contract:

> Agent: Ready to send. Here's exactly what happens:
>
> **To:** recruiter@example.com
> **Subject:** Following up — Director of Partnerships application
> **Body:** *(2 short paragraphs, quoted in full)*
>
> [ Send it ] [ Edit first ] [ Cancel ]

Why it works: the body states the action, the target, and the consequence; the
button is the signature. "Edit first" gives the user a middle path instead of
forcing send-or-nothing.

## 4. Status updates without noise

**Before** — a message for every heartbeat:

> Agent: Starting step 1… done with step 1, starting step 2… done with step
> 2…

**After** — one message, updated in place where the platform allows, or a
single progress line:

> Agent: Migrating the database — ▰▰▱▱▱ 2/5 (backing up…)

Why it works: the user can glance at one line and know exactly where things
stand. If the platform supports editing the message, update the bar in place;
if not, one final ✅ message beats five heartbeat messages.
