---
name: "superhuman_docs"
description: "Read Superhuman (Coda-backed) docs, pages, and tables via API. Use when Demo wants content, tables, or rows pulled from a docs.superhuman.com document."
---

# Superhuman Docs

## Purpose
Use Superhuman Docs with the user-connected `custom.superhuman-docs` credential.

## Tooling
CLI: `~/workspace/skills/superhuman-docs/bin/superhuman-docs`
- `whoami` — which account/token the credential is
- `docs [--limit N]` — list docs (works only for unscoped tokens)
- `pages <doc-url-or-id>` — page ids, names, browser links
- `page <doc-url-or-id> <page-id>` — page canvas exported as markdown
- `tables <doc-url-or-id>` — tables and views, with ids
- `columns <doc-url-or-id> <table>` — column names and types
- `rows <doc-url-or-id> <table> [--query 'Col:"val"'] [--limit N]` — rows as JSON

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Token scope

The stored token is **scoped** to specific docs (whoami says `"scoped": true`), so
`GET /docs` returns 403 — the CLI prints a hint for that. Work from a
`docs.superhuman.com` doc URL or bare doc id instead: `pages <url>` accepts both.
If the user supplies neither a URL nor a doc ID, ask for one — don't guess.
Base URL is `https://coda.io/apis/v1` (docs.superhuman.com/apis/v1 hangs from this
environment). The API is slow (~10s per call through the proxy); a single timeout
is not an auth failure.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.superhuman-docs`.

## Operating Rules
1. Use this skill when the user asks for Superhuman Docs or this provider's API.
2. Restrict authenticated requests to: coda.io, docs.superhuman.com.
3. Do not print, log, or persist raw credentials.
4. If auth is missing or rejected, follow the Auth section rather than asking for a key.
