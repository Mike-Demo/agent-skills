---
name: "jobspipe"
description: "Use Jobspipe when the user asks for Jobspipe or this provider's API."
---

# Jobspipe

## Purpose
Use Jobspipe with the user-connected `custom.jobspipe` credential.

Standing job-search config for Demo (Head/Director of Partnerships hunt):
- Titles: "Head of Partnerships", "Director of Partnerships"
  (also worth trying: "VP Partnerships", "Head of Strategic Partnerships", "VP Alliances")
- Filters: `--remote --country US --max-age-days 4` for twice-weekly scans
- Salary floor: `--min-salary-usd 130000` (Demo's minimum base; uses estimated
  USD fields when the posting states no range)
- Prefer `ghost_score` < 30 and `status` = active when triaging

## CLI
`bin/jobspipe-search` — flags: `--title` (repeatable), `--keywords` (comma list),
`--company` (repeatable), `--remote`, `--country` (repeatable), `--location`
(repeatable), `--max-age-days`, `--source-or` / `--source-not` (comma lists),
`--min-salary-usd`, `--limit`. Prints one JSON object per line.

`bin/jobspipe-mcp <tool> '<json-args>'` — calls any JobsPipe MCP tool over
Streamable HTTP with the stored credential (used for signals; there is no
public REST endpoint for them). Example:
`bin/jobspipe-mcp list_signals '{}'`.

## Signals (live, created 2026-09-30)
Three signals, all `mode: "jobs"`, email destination `your-email@example.com`,
cadence `instant`, evaluated continuously at ZERO credit cost:
- Partnerships Leadership (`59b52841-4a19-4e9e-9703-41800122d558`) — Head/Director/VP of Partnerships titles
- Alliances Channel Ecosystem (`286d415c-781f-408e-8f73-d6adbadb93e0`) — Alliances/Channel/Ecosystem/BD leadership titles
- Target Company Watch (`ca86a802-6cd7-45ef-a508-3d9271b07967`) — partnerships-type roles at Fivetran, Databricks, dbt Labs, Confluent, Snowflake, Stripe, HubSpot, Cloudflare, Notion, Airtable
Common filters: remote US, `employer_type_not: [agency, broker]`,
`max_ghost_score: 30`, `status: active`. The Daily Job Sweep reads the signal
alert emails from Outlook and triages them; manual `jobspipe-search` runs are a
fallback only (one-time credits never refill — 635 left as of 2026-09-30).

Key limits learned 2026-09-30:
- Webhook destinations require a paid plan (builder $49/mo+); free plan is email/Slack only.
- `create_signal` with a webhook destination fails on free with "Monthly request quota exceeded" (monthly credits = 0 on free).
- No MCP tools exist to update/pause/delete signals — use the dashboard (saved login for jobspipe.dev in Secure Vault).
- Signal filters and destinations can't be edited; delete and recreate.

## Tooling
Add service-specific CLIs under `~/workspace/skills/jobspipe/bin/`.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.jobspipe`.

## Operating Rules
1. Use this skill when the user asks for Jobspipe or this provider's API.
2. Restrict authenticated requests to: api.jobspipe.dev, mcp.jobspipe.dev.
3. Do not print, log, or persist raw credentials.
4. If auth is missing or rejected, follow the Auth section rather than asking for a key.
