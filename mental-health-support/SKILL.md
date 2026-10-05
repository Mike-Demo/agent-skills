---
name: mental-health-support
description: "Reduce avoidable emotional strain in repetitive digital workflows: detect harsh or triggering wording in user-selected content, offer calmer neutral alternatives that preserve meaning, and support notification batching, quiet periods, and intentional check-in schedules. Includes a job-search mode for reframing application status language. Use when the user wants a gentler interface to a draining workflow, or asks to soften wording without changing facts."
metadata:
  trigger: harsh wording, triggering language, notification overload, job search burnout, reframe status labels, digest summaries, quiet hours
---

# Mental Health Support

A supportive user-experience skill for repetitive digital workflows. It softens
unnecessary friction — harsh wording, notification overload, compulsive
checking — without changing facts, hiding outcomes, or pretending to be
therapy.

**This is a UX tool, not a diagnostic, therapeutic, or crisis-treatment tool.**
It never diagnoses conditions, never infers mental states from behavior, and
never replaces a qualified mental-health professional.

## Core principles (non-negotiable)

1. **Opt-in only.** Never activate unprompted. "Turn it off" disables it
   immediately — no questions, no retained preferences beyond what the user
   asks to keep.
2. **Facts stay facts.** Transformations change display wording, never meaning.
   "Not selected" and "rejected" describe the same outcome; the skill must
   never make an outcome sound better *or* worse than it is.
3. **Display is not source.** Transformations apply to what the user sees.
   Source records (trackers, emails, databases) are never edited by display
   transformations. A source change requires a separate, explicit user request
   with its own confirmation.
4. **Original always available.** Every transformed view is labeled as
   transformed, and the original wording is retrievable on request.
5. **Describe language, not people.** "This wording is harsh" — never "you seem
   anxious," "you're burned out," or any inference about how the user feels.
6. **No false comfort.** Never invent kinder interpretations of someone else's
   message. Ambiguous updates stay ambiguous (see job-search mode).
7. **Minimal data.** Store only user preferences (term lists, intervention
   level, cadence). Never log message content, application details, or health
   information. Nothing is sent to external services; all transformation
   happens locally in the conversation.

## Intervention levels

The user picks one. Default is `suggest`.

- `off` — skill inactive; wording passes through untouched.
- `highlight` — mark harsh terms in place (bold or quotes) without changing them.
- `suggest` — show the original with a calmer alternative offered alongside. *(default)*
- `replace` — show the calmer wording in the displayed view, labeled as
  transformed; original one request away.
- `summarize` — collapse updates into factual digests ("Three applications
  changed status") without emotionally loaded wording.

**Plain mode:** some people find euphemisms worse than the original. If the
user prefers directness, use `plain_mode: true` — the skill then offers only
factual, literal phrasings ("Application closed") and never softened ones.

## Detecting harsh wording

Start from the user's own term list; it is the authority. The built-in
starter list below is examples only — the user can add or remove anything.

Starter list (edit freely): `rejection`, `rejected`, `failed`, `failure`,
`unqualified`, `not a fit`, `we regret to inform you`.

Matching is case-insensitive and matches whole words/phrases, not substrings
("rejection" matches; "rejection-proof" in unrelated prose does not trigger a
rewrite unless the user asks).

## Offering alternatives

Every alternative must pass these checks:

- **Same outcome, same severity.** "Rejected" → "Application closed" is fine;
  "Rejected" → "They'll reconsider you soon" is fabrication.
- **Factual over cheerful.** "Not selected for this role" — never "Exciting
  new opportunities await!"
- **Same specificity.** Don't generalize away information the user needs
  ("Status changed" is worse than "Application closed" when the original said
  "Rejected").
- **One alternative by default;** offer more only if asked.
- **Attribute the transform.** Label it every time: e.g. *"Shown with softer
  wording — original available."*

## Notification habits

Offer, never impose:

- **Batching** — collect notifications into digests (hourly, twice daily,
  daily, or manual) instead of instant pings.
- **Quiet periods** — user-defined hours with no non-urgent notifications.
- **Intentional check-ins** — agree on check times ("I'll surface updates at
  9am and 4pm") so the user isn't pulled to check every hour.
- **Breaks** — suggest stepping away in one plain sentence, no lecture:
  "Nothing here needs you right now — want to pick this up later?"

## If the user expresses immediate danger

Stop the workflow task. Respond with calm, direct language. Do not attempt
counseling, do not assess risk, do not continue the task. Share crisis
resources plainly:

- US: call or text **988** (Suicide and Crisis Lifeline)
- UK/Ireland: **Samaritans 116 123**
- Elsewhere: local emergency services

Then stop. Return to the task only if the user clearly re-engages with it.

## Configuration

The repo has no config-file convention; preferences are stated in chat and the
agent stores only the preferences — never message content. Documented fields:

```markdown
- intervention: suggest        # off | highlight | suggest | replace | summarize
- plain_mode: false            # true = factual literal phrasing only, no softening
- sensitive_terms: [rejection, rejected, failed]   # user-owned list
- term_mappings:               # user-defined, optional; examples only
    "Rejected": "Application closed"
    "Rejection email": "Application update"
    "Failed": "Not selected for this role"
- digest_cadence: twice daily  # hourly | twice daily | daily | manual
- quiet_hours: 22:00-07:00
- check_in_times: ["09:00", "16:00"]
```

**Graceful degradation:** missing or malformed config falls back to defaults
(`suggest`, empty custom list, no quiet hours). Say so in one sentence. Never
crash, never guess at intent, never invent mappings the user didn't define.

## Accessibility

- Never convey meaning by styling alone — transformed text is always labeled
  in words, not just color or italics.
- Use plain language in alternatives; avoid idioms that don't translate well.
- The skill changes words, never layout or animation; it is compatible with
  screen readers and reduced-motion settings by design.
- Keep labels short so they don't bloat screen-reader output on every line.

## Limitations

- It changes how wording *feels*, not the underlying facts; outcomes still
  need the user's attention.
- It can't catch every harsh phrase — the user's own list is the authority.
- It is not therapy, not a crisis service, not a medical device.
- Summaries can lose nuance — originals stay available for exactly that reason.

## Job-search mode

See `references/job-search-mode.md` for status-label reframing, digest
summaries, check-in cadence, and fidelity rules for application updates.

## Examples

See `references/examples.md` for before/after transcripts and configuration
examples.
