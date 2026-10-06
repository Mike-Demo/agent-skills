---
name: jobsdb
description: "Free, unlimited manual job search against the public jobs_db Supabase (a hiring.cafe scrape of US jobs; row counts change over time). Use for one-off manual searches where you would otherwise spend finite JobsPipe credits; never as a replacement for JobsPipe signals."
---

# jobsdb skill

Free, unlimited manual job search against the public jobs_db Supabase
(DevelopIQ-ai/jobs_db on GitHub — a hiring.cafe scrape of US jobs; the row
count changes over time, so don't rely on any specific number).
Read-only via the published anon key; no signup, no credits.

## When to use

- One-off manual job searches where you'd otherwise spend finite JobsPipe credits.
- Broad title sweeps (e.g. all "partnerships" titles scraped recently).
- NEVER as a replacement for JobsPipe signals (instant alerts, ghost-score
  filtering, active-status) and never as a base for automation — the source
  repo is new and could vanish.

## CLI

`bin/jobsdb-search` — flags:

| Flag | Meaning |
|---|---|
| `--title "..."` | ilike match on job title (required for useful results) |
| `--company "..."` | ilike match on company name |
| `--since YYYY-MM-DD` | only rows scraped on/after date — **always use this**; the DB mixes fresh and stale (months-old) listings with no active/closed flag |
| `--limit N` | max rows (default 20) |
| `--remote` | filter workplace_type to remote |

Example (`--since` takes a date computed from the requested freshness window —
e.g. 7 days back for a weekly sweep):
```
bin/jobsdb-search --title "partnerships" --since <YYYY-MM-DD> --limit 20
bin/jobsdb-search --title "alliances" --remote --since <YYYY-MM-DD>
```

## Known quirks

- Simple `ilike` + `limit` queries work. Aggregate queries (`count=exact`,
  `order=`) hit statement timeouts — don't use them.
- `locations` is a JSON array; `workplace_type` is a string ("Remote", "Hybrid", ...).
- No dedup across re-scrapes beyond `collapse_key`; treat near-duplicates as expected.
- Always verify a listing is still live by opening its `apply_url` before
  acting — older scrapes are less reliable, so prefer fresh `--since` windows.
