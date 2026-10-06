---
name: "rebrandly"
description: "Create and manage branded short links via the Rebrandly API. Use when the user wants a short, trackable link for a URL — e.g. for social posts, QR campaigns, or portfolio links."
---

# Rebrandly

## Purpose
Use Rebrandly with the user-connected `custom.rebrandly` credential to shorten
URLs into branded links and manage existing ones (list, update, delete).
Confirm with the user before deleting a link — deleted short links break
wherever they were shared.

## Tooling
Add service-specific CLIs under `~/workspace/skills/rebrandly/bin/`.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.rebrandly`.

