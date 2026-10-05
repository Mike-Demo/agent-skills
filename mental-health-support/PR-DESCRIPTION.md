# PR description: Add `mental-health-support` skill

## Purpose

Job searching (and other repetitive digital workflows) can be draining:
triggering words repeated dozens of times ("rejected"), compulsive
checking for updates, and high volumes of impersonal notifications. This
skill is a supportive UX layer that reduces that avoidable strain — it is
explicitly **not** a diagnostic, therapeutic, or crisis-treatment tool.

## Design decisions

- **One skill with a job-search mode**, not two skills. Matches the repo's
  dominant pattern (`agent-chat-ux`, `partnerships-career`: one skill +
  `references/`). The safety guardrails, intervention levels, and config
  surface are identical for both uses; splitting them would duplicate the
  guardrails and let them drift.
- **Intervention levels** (`off` / `highlight` / `suggest` / `replace` /
  `summarize`, default `suggest`) so the user controls how much the skill
  touches what they see. Includes `plain_mode` for users who find euphemisms
  worse than the original.
- **Display ≠ source.** All transformations are display-only; source records
  are never edited by the skill. Every transformed view is labeled, and the
  original is always retrievable.
- **Fidelity over comfort.** Alternatives preserve exact outcome and
  severity; ambiguous employer messages stay ambiguous; silence is reported
  as "no response yet," never interpreted.
- **No config-file convention invented.** The repo has no config files, so
  preferences are documented markdown fields stated in chat (intervention,
  plain_mode, sensitive_terms, term_mappings, digest_cadence, quiet_hours,
  check_in_times), with graceful fallback to defaults on missing/malformed
  input.
- **Tests use the repo's native format**: `evals/evals.json`
  (`{skill_name, trigger_queries, evals: [{id, prompt, expected_output,
  files, expectations}]}`) — 12 evals covering all 10 required scenarios
  plus crisis response and digest neutrality. No pytest: the repo has zero
  test files and inventing a framework would break convention.
- **Changelog**: the repo has none, so `CHANGELOG.md` is a *proposed* entry
  format — adopt, adapt, or drop.

## Testing

- `evals/evals.json` validates as JSON and matches the committed
  `partnerships-career/evals/evals.json` schema (12 evals, each with
  id/prompt/expected_output/files/expectations).
- Coverage: display-vs-source fidelity, meaning preservation, plain-mode
  opt-out, term add/remove, transform labeling, original retrieval,
  non-diagnosis, no private data in logs, ambiguity handling, malformed
  config, crisis response, digest neutrality.

## Privacy protections

- Opt-in only; "turn it off" disables immediately.
- Stores only user preferences (term lists, levels, cadence). Never logs
  message content, application details, or health information.
- Nothing is sent to external services; all transformation is local to the
  conversation.
- Never diagnoses, never infers mental state, never stereotypes.

## Threat / privacy analysis

| Threat | Mitigation |
|---|---|
| Skill used to hide a real outcome from the user | Transformations are labeled; originals retrievable; alternatives must preserve severity |
| Skill fabricates kinder employer intent | Fidelity rules: ambiguous stays ambiguous; no reinterpretation |
| Sensitive job-search/health data leaks via logs or telemetry | Preferences-only storage; no content logging; no external calls |
| Skill presents as therapy | Explicit non-clinical scope in SKILL.md; crisis rule defers to real resources |
| Repo-context: live OAuth tokens committed in `clera/` and `indeed/` | Out of scope for this PR, but flagged: the skill's "no secrets" stance is documented partly because the repo currently violates it elsewhere |

## Unresolved questions

1. Should `CHANGELOG.md` become a repo convention, or should the entry live
   only in the commit message? (Repo currently has no changelog.)
2. Crisis resources are US/UK-IE-centric — acceptable default, or should the
   skill ask the user's country on first opt-in?
3. `metadata:` frontmatter (`trigger`) is used by only 3 of 28 skills and may
   not be consumed by tooling — kept for discoverability; drop if unwanted.
