# jobsdb skill

Free, unlimited manual job search against the public jobs_db Supabase
(DevelopIQ-ai/jobs_db on GitHub — a hiring.cafe scrape, ~178k US jobs).
Read-only via the published anon key; no signup, no credits.

## When to use

- One-off manual job searches where you'd otherwise spend finite JobsPipe credits.
- Broad title sweeps (e.g. all "partnerships" titles scraped recently).
- NEVER as a replacement for JobsPipe signals (instant alerts, ghost-score
  filtering, active-status) and never as a base for automation — the source
  repo is brand new (created 2026-10-01) and could vanish.

## CLI

`bin/jobsdb-search` — flags:

| Flag | Meaning |
|---|---|
| `--title "..."` | ilike match on job title (required for useful results) |
| `--company "..."` | ilike match on company name |
| `--since YYYY-MM-DD` | only rows scraped on/after date — **always use this**; the DB mixes fresh and stale (months-old) listings with no active/closed flag |
| `--limit N` | max rows (default 20) |
| `--remote` | filter workplace_type to remote |

Example:
```
bin/jobsdb-search --title "partnerships" --since 2026-09-01 --limit 20
bin/jobsdb-search --title "alliances" --remote --since 2026-09-15
```

## Verified quirks (2026-10-01)

- Simple `ilike` + `limit` queries work. Aggregate queries (`count=exact`,
  `order=`) hit statement timeouts — don't use them.
- `locations` is a JSON array; `workplace_type` is a string ("Remote", "Hybrid", ...).
- No dedup across re-scrapes beyond `collapse_key`; treat near-duplicates as expected.
- Always verify a listing is still live by opening its `apply_url` before acting —
  assume anything scraped >2 weeks ago may be dead.
