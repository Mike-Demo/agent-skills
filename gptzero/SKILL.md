---
name: "gptzero"
description: "Scan text with the GPTZero AI-detection API to check whether drafts read as human-written. Use when Demo wants a voice/authenticity check on drafted prose."
---

# GPTZero

## Purpose
AI-detection scans of drafted text (follow-ups, posts, emails) to check whether drafts read as human-written in Demo's voice

## Tooling
Add service-specific CLIs under `~/workspace/skills/gptzero/bin/`.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the hosts below.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.gptzero`.

## Operating Rules
1. Use this skill when Demo wants an AI-detection / voice-authenticity check on drafted prose.
2. Restrict authenticated requests to: api.gptzero.me.
3. Do not print, log, or persist raw credentials.
4. If auth is missing or rejected, follow the Auth section rather than asking for a key.

## Usage

`bin/gptzero-scan` — scan text, print a human-readable summary (word count,
P(ai)/P(human)/P(mixed), predicted class, reliability note).

```bash
gptzero-scan --text "Your draft here..."
gptzero-scan --file draft.txt
echo "Your draft here..." | gptzero-scan
gptzero-scan --file draft.txt --json   # raw API response
```

Notes:
- Requires ~250 characters minimum; GPTZero is unstable under ~100 words.
  Short drafts (under ~150 words) produce unstable scores, so treat the
  score as a rough signal, not a verdict. Calibrate first: scan Demo's
  known-human writing and compare drafts against that baseline rather
  than against 0.
- False positives happen (ESL bias documented in the literature; dense
  technical prose inflates scores).
- `api.gptzero.me` sits behind Cloudflare, which 403s urllib's default
  User-Agent (error 1010). The CLI sends a browser UA; any new code hitting
  this API must do the same.
