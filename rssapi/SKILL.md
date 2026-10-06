---
name: "rssapi"
description: "Monitor RSS and job-board feeds via Rssapi webhook push subscriptions. Use when subscribing to feed URLs so new items push to a webhook (instead of polling a monthly quota), or when managing those subscriptions."
---

# Rssapi

## Purpose
Use Rssapi with the user-connected `custom.rssapi` credential.

## Tooling
Add service-specific CLIs under `~/workspace/skills/rssapi/bin/`.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.rssapi`.

## Operating strategy (verified 2026-09-30)

- **Subscriptions over parsing.** A subscription is monitored continuously and pushes
  new items to the webhook (unlimited events on Entry plan). Each manual `get`/parse
  burns from the 100/month quota — never poll. Subscribe once, let webhooks deliver.
- **`subscribe` requires a real feed URL.** Page URLs are rejected with
  "This is not a valid Feed URL". There is no `detect` endpoint (404). Find feed URLs
  by scanning the page HTML for `<link rel="alternate" type="application/rss+xml">`
  tags yourself (free, no quota). WordPress job boards often expose `/jobs/feed/`.
- **Most niche job boards and company career pages publish no feed.** Verified
  2026-09-30: Partnership Leaders, Lesbians Who Tech, Hitmarker, gamesindustry.biz
  jobs, and 10 target-company career pages (Fivetran, Databricks, dbt Labs, Confluent,
  Snowflake, Stripe, HubSpot, Cloudflare, Notion, Airtable) have no subscribable feed.
  Only https://outintech.com/jobs/feed/ qualified (subscribed, id 76489; feed was
  empty at subscribe time — webhooks fire when items appear).
- **Webhook URL is dashboard-only.** Set once per application in the rssapi.net
  dashboard (the user sets it — no saved dashboard login exists). The webhook
  URL carries a token stored with the job-alerts receiver config; the token
  contains `?`, `/`, `=`, `:` so it must be percent-encoded in the query
  string.
- **24h webhook logs** in the dashboard are the recovery window if the receiver
  misses a push.
- **Do not duplicate** Remotive / We Work Remotely — the job-feeds skill scans those
  free. The 10 target companies are covered by the JobsPipe "Target Company Watch"
  instant-email signal instead.

## Subscriptions

Active subscriptions live in the Rssapi account, not in this file. The local
record is `~/workspace/goals/land-a-head-or-director-of-partnerships-role/hidden_files/rssapi-subscriptions.json`
— re-list via the API before acting and never rely on a committed ID list.
Current coverage: Out In Tech jobs, Remotive category feeds (sales, marketing,
product), We Work Remotely category feeds (sales-and-marketing,
management-and-finance), WordPress.org jobs. Category feeds only (not the
Remotive/WWR main firehoses) to keep the push signal clean. The job-feeds
skill's bin/rss-scan covers the same feeds and stays as a manual fallback —
the sweep does not run it while webhooks live.
