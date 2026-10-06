---
name: career-ops-followup
description: Track follow-up cadence for Demo's active job applications — flag overdue follow-ups, draft tailored follow-up emails/LinkedIn messages in Demo's voice, and record sends. Use when Demo asks about follow-ups, nudging applications, or what's aging.
metadata:
  trigger: follow-up cadence, overdue applications, nudge drafts, aging applications
  source: career-ops modes/followup.md (cherry-picked sub-skill)
---

# Career-Ops Followup — Follow-up Cadence Tracker (Demo's install)

## Project root

All repo-relative paths resolve to the career-ops checkout:

```
PROJECT_ROOT=~/workspace/career-ops
```

Full mode reference: `modes/followup.md` in that checkout. User-layer context:
`config/profile.yml`, `modes/_profile.md`, `modes/_custom.md`.

## Voice

Follow-up drafts are conversational. Apply Demo's voice rules: plain, warm,
concise, active; no puffery, no invented claims; no em dashes, no filler
openers, no binary contrasts (see `~/workspace/skills/stop-slop/`). Proof
points come from verified facts only — the application-answers memory and
`~/workspace/user/files/Mike_Agent_File.md`. Never drop or soften a real metric.

## Prerequisites (every run)

1. Regenerate the tracker mirror:
   ```bash
   python3 ~/workspace/career-ops/sync-tracker.py
   ```
   If the script is missing or fails, stop and report it — don't work from
   a stale mirror or guess application state.
2. Run everything below from `PROJECT_ROOT`.

## Step 1 — Cadence check

```bash
cd ~/workspace/career-ops && node followup-cadence.mjs
```

If the command is missing, exits non-zero, or returns malformed JSON, stop
and report it — don't invent cadence data.

Parse the JSON: `metadata` (tracked, actionable, overdue/urgent/cold/waiting),
`entries` (per application: company, role, status, days since applied,
follow-up count, urgency, next date, contacts), `cadenceConfig`.

Defaults (also in `config/profile.yml`): Applied → first nudge at 7 days, then
every 7 days, max 2 (then cold); Responded → reply within 1 day (urgent), then
every 3; Interview → thank-you within 1 day, then every 3.

If nothing is actionable, say so plainly and stop.

**Rejection-latency cross-check** (only when interview rows exist):
`node rejection-latency.mjs` flags post-interview silence past the 30-day
courtesy threshold. Confirmation gate: never render a flag until the user has
confirmed the company and dates in the current conversation — unconfirmed
flags stay silent.

## Step 2 — Dashboard

Show applications sorted urgent > overdue > waiting > cold:

```
| # | Company | Role | Status | Days | Follow-ups | Next | Urgency | Contact |
```

- **URGENT** — company replied, respond within 24 hours.
- **OVERDUE** — follow-up past due; draft below.
- **waiting (X days)** — on track.
- **COLD** — 2+ follow-ups, no response; suggest closing, not another nudge.

## Step 3 — Drafts (overdue and urgent only)

Read the tracker's Notes for company context. Draft 3–4 sentences:

1. The specific role + when Demo applied (company and title named).
2. One concrete value-add from verified facts — quantified when possible.
3. Soft ask + a specific time window ("this week", "next Tuesday").
4. (Optional) one relevant recent project or achievement.

Rules: professional but warm, never desperate. NEVER "just checking in",
"just following up", "touching base", "circling back". Lead with value, not
the ask. Under 150 words. Include a subject line. Reference something specific
to THAT company.

- **No email contact found** → LinkedIn version: 3 sentences, 300 chars max.
- **Second follow-up** → 2–3 sentences, a new angle (insight, article, project
  update); don't repeat the first.
- **Cold (2+ sent)** → no draft. Suggest: mark Discarded, try a different
  contact, or deprioritize.
- **Agency-mediated** (Via = Staffing firm) → address the recruiter, not the
  company; if the end employer is unknown, ask for the company name as part
  of the nudge (it unlocks dedup).

## Step 4 — Present

For each draft: company + role + tracker #, recipient ("No contact found" if
none), subject, days since applied, follow-ups sent, channel, then the draft.

## Step 5 — Record (only on confirmation)

Record a follow-up in `data/follow-ups.md` ONLY after the user confirms they
actually sent it. Never record a draft as sent. Table columns:
`num | appNum | date | company | role | channel | contact | notes`.

## Step 6 — Summarize

Tracked / overdue (drafts above) / urgent (respond today) / waiting /
cold — then: "Tell me which ones you've sent and I'll record them."
