# Changelog (proposed)

> Note: this repository has no changelog convention yet. This file proposes the
> entry format for the new skill; adopt, adapt, or drop per maintainer preference.

## 2026-10-05 — Add `mental-health-support` skill

- New skill: opt-in UX layer that softens harsh wording in repetitive digital
  workflows (highlight / suggest / replace / summarize intervention levels,
  user-defined sensitive terms, notification batching, quiet hours,
  intentional check-in schedules).
- Job-search mode (`references/job-search-mode.md`): neutral status-label
  reframing (display-only, source records untouched), factual digest
  summaries, fidelity rules for ambiguous employer messages.
- Safety: non-clinical by design (no diagnosis, no mental-state inference);
  immediate-danger rule stops the task and shares crisis resources
  (US 988, UK/IE Samaritans 116 123).
- Privacy: stores only user preferences; no message content logged; nothing
  sent to external services.
- Tests: 12 evals in `evals/evals.json` covering display-vs-source fidelity,
  meaning preservation, plain-mode opt-out, term add/remove, transform
  labeling, original retrieval, non-diagnosis, no private data in logs,
  ambiguity handling, malformed config, crisis response, and digest neutrality.
