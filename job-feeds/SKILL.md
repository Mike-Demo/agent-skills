---
name: job-feeds
description: "Unified job search sweep across three sources, merged and deduped for Demo's partnerships job search. Use when running the daily job sweep or a one-off multi-source search."
---

# job-feeds skill — the unified job search skill

Three sources wired into one sweep for Demo's partnerships job search.

## `bin/job-search` (the unified sweep)

Runs every enabled source, merges, dedupes by posting URL, and prints NEW
items as one JSON array: `source, title, company, url, published, location,
salary` (+ `ghost_score` for Jobspipe rows).

```
bin/job-search [--seen PATH] [--max-age-days N] [--jobspipe]
               [--jobspipe-limit N] [--rezi-roles "a,b"] [--no-rss] [--no-rezi]
```

- `--seen PATH` (default `<this-dir>/.job-search-seen.json`, created if
  missing): merged dedup across runs; rss-scan keeps a companion `<seen>.rss`.
- Sources: `rss:*` (Remotive + We Work Remotely public feeds, free, always on),
  `rezi` (Rezi job board via `search_jobs`, free reads, first page per role),
  `jobspipe` (manual API search — **opt-in only** with `--jobspipe`).
- Jobspipe costs 1 credit per new job and the quota never refills (635 left as
  of 2026-09-30). The primary Jobspipe channel is the live signals → Outlook
  alert emails, triaged by the Daily Job Sweep. Manual search here is
  fallback-only; the script prints a credit warning when used.
- Stderr carries per-source warnings; exit 1 only if every enabled source
  failed. One source failing never blocks the others.

## `bin/rss-scan`

The RSS leg on its own. Scans the public RSS feeds of Remotive and We Work
Remotely for partnerships leadership roles. Both publishers explicitly offer
these feeds for aggregation.

```
bin/rss-scan [--seen PATH] [--max-age-days N]
```

- Stdout: JSON array of NEW matches: `source, title, company, url, published`.
- `--seen PATH`: JSON list of posting URLs already handled (created if missing);
  only unseen matches are printed, and the file is updated.
- `--max-age-days N`: only items published within N days (default 3).
- Stderr carries per-feed warnings; exit 1 only if every feed failed.

Title filter: must contain a seniority token (head, director, vp, vice
president, svp, evp, chief) and a topic term. Each match is tiered:
- core: partnerships/alliances term (partnership, alliance, ...)
- adjacent: channel, ecosystem, or business development term
  (Demo is open to these related leadership functions).
Intern/junior/associate/assistant/coordinator titles are excluded. Company is guessed from common
title formats ("Role at Company", "Company: Role") and may be empty — the
worker triages with the URL.

Feeds covered: Remotive main + sales/marketing/product categories,
We Work Remotely full feed + sales-and-marketing + management-and-finance
categories. Category feeds exist because the main feeds only carry the most
recent N postings.

## The full pipeline (how the wired pieces fit)

1. **Search** — `bin/job-search` (or the Daily Job Sweep cron, which also reads
   the Jobspipe signal alert emails from Outlook and the rssapi.net webhooks).
2. **Triage** — filter to suitable roles ($130k+, remote US, full-time), exclude
   former employers (hosting.com, Codeable, InMotion/BoldGrid, MVP Marketing +
   Design, SPC, Sengistix, Binary Web Design), dedupe against
   `Master-Application-Tracker.xlsx` (OneDrive root, the canonical gate).
3. **Tailor** — Rezi: `rezi-mcp tools/call write_resume` from the master resume
   + the JD. Confirm with Demo before writing; never invent resume content.
   Rezi also auto-generates cover letters from company + JD.
4. **Apply** — standing rules: suitable → Sprout import (auto overnight);
   approved role rejected by Sprout → Cane applies direct without asking;
   borderline → digest for yay/nay. Stop for unanswerable required questions,
   unsaved logins (never create accounts unasked), or closed postings.
5. **Track** — log every application to the tracker with channel, timestamp,
   source, and résumé used; re-upload to OneDrive.

## Other sources (browser-based, see the daily cron body)

- Out In Tech job board (Qorporate) — Demo is a member; may need login.
- Axios Local job listings — enumerate every local edition's jobs page.
- Partnership Leaders community job board.
- LinkedIn Jobs — signed-in session, remote US, past-24h partnerships searches.

Scans the public RSS feeds of Remotive and We Work Remotely for partnerships
leadership roles. Both publishers explicitly offer these feeds for aggregation.

```
bin/rss-scan [--seen PATH] [--max-age-days N]
```

- Stdout: JSON array of NEW matches: `source, title, company, url, published`.
- `--seen PATH`: JSON list of posting URLs already handled (created if missing);
  only unseen matches are printed, and the file is updated.
- `--max-age-days N`: only items published within N days (default 3).
- Stderr carries per-feed warnings; exit 1 only if every feed failed.

Title filter: must contain a seniority token (head, director, vp, vice
president, svp, evp, chief) and a topic term. Each match is tiered:
- core: partnerships/alliances term (partnership, alliance, ...)
- adjacent: channel, ecosystem, or business development term
  (Demo is open to these related leadership functions).
Intern/junior/associate/assistant/coordinator titles are excluded. Company is guessed from common
title formats ("Role at Company", "Company: Role") and may be empty — the
worker triages with the URL.

Feeds covered: Remotive main + sales/marketing/product categories,
We Work Remotely full feed + sales-and-marketing + management-and-finance
categories. Category feeds exist because the main feeds only carry the most
recent N postings.

## Other sources (browser-based, see the daily cron body)

- Out In Tech job board (Qorporate) — Demo is a member; may need login.
- Axios Local job listings — enumerate every local edition's jobs page.
- Partnership Leaders community job board.
- LinkedIn Jobs — signed-in session, remote US, past-24h partnerships searches.
