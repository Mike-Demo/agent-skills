---
name: "firecrawl"
description: "Search the web and scrape pages to markdown via the Firecrawl API. Use when research needs page content, not just links — e.g. gathering restaurant or venue details, reading job postings, or extracting site content."
---

# Firecrawl

## Purpose
Use Firecrawl with the user-connected `custom.firecrawl` credential.

## Tooling
`bin/firecrawl` — CLI for the Firecrawl v2 API:
```
firecrawl search "kosher restaurant San Francisco" --limit 5
firecrawl search "query" --scrape   # include page markdown per result
firecrawl scrape https://example.com/menu
```
Search returns title/url/snippet per result (2 credits per search); `--scrape`
adds markdown content. Scrape returns one page as markdown (onlyMainContent).
Searches and scrapes spend paid credits — confirm with the user before
running them, or batch multiple lookups into one confirmed run.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.firecrawl`.

