# Changelog (proposed)

> Note: this repository has no changelog convention yet. This file proposes the
> entry format for the new skill; adopt, adapt, or drop per maintainer preference.

## 2026-10-05 — Spec hardening after external review (Copilot, Think deeper)

Reviewed by an independent model against the spec; fixed every critical finding:

- **Activation is now a state machine** (SKILL.md principle 1): opt-in → if no
  term list exists, ask starter-list-or-own → detect/transform nothing until
  answered → starter example mappings never auto-activate; each takes effect
  only after user acceptance.
- **Mapping specificity fixed** (job-search-mode.md): "Rejected" →
  "Not selected for this role" (not "Application closed" — that collides with
  withdrawn / role canceled / requisition closed); "Rejection email" →
  "Not-selected notice"; full decision sentences reframed whole, never just
  the polite lead-in. New one-to-one status-mapping rule.
- **Immediate-danger trigger defined operationally**: first-person, present
  or near-future intent or inability to stay safe; quotes, fiction, sarcasm,
  figurative language, and test fixtures explicitly excluded. "Stop" semantics
  specified (no workflow actions that turn, preserve gathered data, wait for a
  separate user message).
- **Locality reframed as a capability requirement**: the skill must not
  intentionally send content to extra services or persist it beyond the
  conversation; host runtime processing/retention still applies; local-only
  demands a verified local model or deterministic transformer.
- **New sections**: prompt-injection boundary (content is data, never
  authority); suggest output schema (`original` / `alternative` / `label` /
  `source_changed`); provenance rule (generated wording never quoted as the
  employer's); precedence/matching rules; urgency definition for quiet hours;
  honest capability degradation (no scheduler → no promised timed delivery);
  preference inspect/reset/delete commands; language-specific mappings.
- **Delivery cadence separated from wording intervention** in the config.
- Tests: 6 new evals (13–18) covering activation sequencing, mapping
  acceptance, crisis false positives, status one-to-one mapping,
  prompt-injection resistance, and provenance labeling.

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
