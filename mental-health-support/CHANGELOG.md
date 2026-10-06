# Changelog (proposed)

> Note: this repository has no changelog convention yet. This file proposes the
> entry format for the new skill; adopt, adapt, or drop per maintainer preference.

## 2026-10-05 — Round 2: fix example-vs-rule contradictions (revalidation 4/6 → fixes)

Second external review passed activation, specificity, crisis, and locality,
but failed mode boundaries and provenance — the *examples* contradicted the
rules. Fixed:

- Digest example 4 relabeled as what it is (`summarize`); added example 4b,
  a true suggest-level digest that shows originals with alternatives offered
  and never silently collapses.
- All agent-generated alternatives in examples unquoted; fixed label
  "Agent-generated alternative:" used throughout; quotation marks reserved
  for exact source text.
- Mapping approval is now per-item or persistent ("Use this once" /
  "Save this mapping" / "Accept all proposed mappings"); separate
  `accepted_term` vs `accepted_mapping` states; detectable-but-unmapped
  terms get a flagged original plus an explicitly "Proposed — not approved"
  draft, never a silent reuse.
- Named cadences resolve to user-selected times (defaults: twice daily →
  09:00/16:00, daily → 09:00, hourly → top of hour); agent never invents times.
- Large-batch rule: ask before collapsing ("47 updates — show all or
  counts-only summary?").
- Replace boundary defined: "short phrase" ≈ ≤10 words, not a complete
  standalone message. Selecting `highlight` counts as its required consent.
- Already-neutral statuses (Withdrawn, Role canceled, Hiring paused) shown
  unchanged — no alternative proposed, keeping output deterministic.
- Danger example tightened to the minimal protocol; third-party-report
  example adds emergency-services escalation.
- Tests: evals 19–21 (suggest digest fidelity, provenance labeling,
  one-time mapping approval). 21 evals total.

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
