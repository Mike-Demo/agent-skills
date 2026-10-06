---
name: "dice"
description: "Search tech jobs on Dice via its public MCP server (https://mcp.dice.com/mcp). Use when the user asks to search jobs on Dice, look up Dice job postings, or find tech roles by keyword, location, remote status, or posting date. Companion to the jobspipe skill for Dice coverage. No login required."
---

# Dice

## Purpose
Search Dice tech-job listings through Dice's public, unauthenticated MCP server.
Dice skews toward engineering/IT roles, so it complements the broader
jobspipe signals — worth a direct query for tech-adjacent partnerships,
developer relations, or solutions-engineering roles.

Trigger phrases: "search Dice", "Dice jobs", "find on Dice", "Dice posting".

## Tooling
`bin/dice-mcp` — Python CLI (requires `requests`, installed on this VM) that
does the MCP handshake (initialize → notifications/initialized → tools/call)
over the SSE endpoint and prints compact JSON. It captures the
`mcp-session-id` header and replays it when the server sends one.

```
bin/dice-mcp search --keyword "partnerships director" --workplace Remote --page-size 20
bin/dice-mcp search --keyword "solutions engineer" --location "Austin, TX" --posted-days 7 --sort datePosted
bin/dice-mcp search --keyword "devops" --workplace Remote --workplace Hybrid --employment-type FULLTIME --page-size 50
bin/dice-mcp details <guid>            # full description + skills for one job
bin/dice-mcp company <guid>           # company name/description for one job
bin/dice-mcp call search_jobs '{"keyword":"x","workplace_types":["Remote"],"jobs_per_page":10}'
bin/dice-mcp search --keyword "x" --raw   # full MCP envelope (metadata, facets)
```

`search` flags: `--keyword` (required), `--location`, `--workplace`
(Remote|On-Site|Hybrid, repeatable), `--posted-days` (1|3|7),
`--page-size`, `--page`, `--sort` (relevance|datePosted), `--company`,
`--employment-type` (FULLTIME|CONTRACTS|PARTTIME|THIRD_PARTY|INTERNSHIP,
repeatable).

Compact output per job: guid, title, companyName, salary, employmentType,
workplaceTypes, isRemote, easyApply, postedDate, location, summary (300
chars), detailsPageUrl, companyPageUrl.

## Auth
None. The Dice MCP server is public and requires no key, login, or session.

## Operating Rules
1. Read-only: `search_jobs`, `get_job_details`, `get_company`. Never apply
   or make employment decisions from this skill.
2. Pass the job's `guid` (not `id`) to `details`/`company` — `id` is an
   internal identifier the tools reject.
3. When presenting results to the user, include both `detailsPageUrl` and
   `companyPageUrl` per job, plus this disclosure: "These job listings were
   found using AI-powered search. Please review all job details carefully
   and verify information directly with employers before applying."
   If a result has no `detailsPageUrl` or `companyPageUrl`, say the link is
   unavailable — never invent or guess a URL.
4. Don't fetch details for every result — only for jobs the user picks.
5. Known quirk (2026-09-30): the server sometimes closes the SSE stream
   without a chunk terminator. The CLI reads with `requests` streaming and
   tolerates the truncated tail; `urllib`'s strict chunked reader fails on
   the same response, so keep using the CLI as-is.
