---
name: career-ops-patterns
description: Analyze Demo's job applications for rejection patterns and targeting insights — funnel stats, channel yield (Sprout vs Jobbie vs Manual), ATS-vendor yield, blockers, and recommendations. Use when Demo asks about rejection patterns, what's converting, or how to improve targeting.
metadata:
  trigger: rejection patterns, conversion analysis, targeting review, channel performance
  source: career-ops modes/patterns.md (cherry-picked sub-skill)
---

# Career-Ops Patterns — Rejection Pattern Detector (Demo's install)

## Project root

All repo-relative paths resolve to the career-ops checkout:

```
PROJECT_ROOT=~/workspace/career-ops
```

Full mode reference: `modes/patterns.md` in that checkout. User-layer context:
`config/profile.yml`, `modes/_profile.md`, `modes/_custom.md`.

## Prerequisites (every run)

1. Regenerate the tracker mirror (the xlsx in `~/workspace/master-tracker/` is
   canonical; the markdown mirror is derived and must never be hand-edited):
   ```bash
   python3 ~/workspace/career-ops/sync-tracker.py
   ```
2. Run everything below from `PROJECT_ROOT`.

## Step 1 — Run the analysis

```bash
cd ~/workspace/career-ops && node analyze-patterns.mjs --summary
```

Minimum threshold: at least 5 sent rows (Applied/Responded/Interview/Offer/
Rejected). Below that, say so plainly and stop.

Key output fields: `metadata` (totals, outcomeRates), `funnel`, `scoreComparison`,
`archetypeBreakdown`, `blockerAnalysis`, `remotePolicy`, `companySizeBreakdown`,
`vendorAnalysis`, `viaChannelAnalysis`, `scoreThreshold`, `techStackGaps`,
`discardReasonStats`, `recommendations`.

### Demo-specific mappings

- **Score column is `—` for every row** (Demo doesn't track JD scores). Skip
  `scoreComparison` and any score-threshold recommendation unless scores exist.
- **`viaChannelAnalysis` is the channel split-test scoreboard.** `Via` is
  normalized to `Sprout | Jobbie | Manual | Staffing firm | Tracker`. Report
  per-channel advance rate — this is the Sprout-vs-Jobbie-vs-Manual comparison.
- **`vendorAnalysis`** works from the tracker's `URL` column (only a small
  share of rows carry one — measure and report the actual coverage each run).
  Greenhouse/Lever/Ashby/Workday are URL-detectable; everything
  else falls in `unknown`. Always state coverage.
- **Causal humility is mandatory.** Report channel yield, never discrimination
  or bias claims. "X% of your applications go through {vendor/channel} and it
  advances far less than your other channels — route those companies through
  referral or direct contact instead." Respect `sufficientSample`: below the
  floor, observations only, never recommendations.

### Hygiene first

Lead with aged-Applied rows that look silent — present that list before any
card. A stale tracker row produces the same signal as genuine silence.

## Step 2 — Write the report

Write to `reports/pattern-analysis-{YYYY-MM-DD}.md` in the checkout, plus copy
to `~/workspace/goals/land-a-head-or-director-of-partnerships-role/files/`
so it shows up with the other job-search documents.

Structure: title + applications analyzed + date range + outcome line, then
Conversion Funnel table, Archetype Performance (`decidedRate` leads;
`conversionRate` never divides by unevaluated rows), Top Blockers (share of
gap-bearing entries), Remote Policy Patterns (never call a segment bad on
conversionRate alone — silence is not rejection), Tech Stack Gaps, then
numbered Recommendations each with [IMPACT] and reasoning.

## Step 3 — Present

Condensed summary in chat: one-line stats (X sent, Y decided, Z% of decided
advanced — never a share of the total), top 3 findings, link to the full report.

## Step 4 — Offer actions

Ask which recommendations to apply. Demo's equivalents of the stock actions:

- Filter/portal changes → update the Daily Job-Search Workflow v2 plan and the
  discovery filters (JobsPipe signals, RSS subscriptions), not `portals.yml`.
- Archetype/targeting changes → edit `modes/_profile.md` (never `_shared.md`).
- Score threshold → only when `scoreThreshold.sufficientSample` is true; store
  under a `patterns` key in `config/profile.yml`.

## Outcome classification

Interview/Offer/Responded/Hired = **positive** · Applied = **awaiting** ·
Rejected = **negative** · Discarded = **discarded** (not an employer decision) ·
SKIP = **self-filtered** · Evaluated = **pending** (never sent).
