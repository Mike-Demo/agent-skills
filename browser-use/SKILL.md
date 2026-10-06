---
name: "browser_use"
description: "Run hosted browser agents via the Browser Use Cloud API for web research, page interaction, and form or site checks that need a real browser session. Use when the user asks for a Browser Use cloud agent run."
---

# Browser Use

## Purpose
Use Browser Use with the user-connected `custom.browser-use` credential to run
hosted browser agents: give a task, get back structured page results. For
plain page-text reads, prefer `browser.open`; use this skill when the task
needs interaction (clicks, forms, logins, multi-step flows).

## Tooling
`bin/browser-use` — CLI for the Browser Use Cloud API v4 (hosted agents):
```
browser-use list                              # recent runs
browser-use run --task "..." [--model ultrafast] [--max-cost 1.00]
        [--output-schema '{"type":"object",...}'] [--no-wait] [--timeout 1800]
browser-use status <run_id>                   # cheap poll
browser-use result <run_id>                   # full result JSON
browser-use wait <run_id> [--timeout 1800]    # poll then print result
```
Two flows — pick one per run, don't mix them:
- Blocking: `run` waits by default and returns when the run finishes (or
  `--timeout` hits); then fetch `result`.
- Detached: `run --no-wait` returns a run_id immediately; poll `status` until
  completed/failed/cancelled (or use `wait <run_id>`), then fetch `result`.
`max-cost` caps spend per run. Confirm with the user before starting a paid
run — state the task and the max-cost cap. Inside a run, confirm before any
consequential action: submitting forms, changing account data, publishing,
or purchasing.
Model `bu-ultrafast` is the user's pick (`bu-fast` is the cheaper sibling); if the
API rejects a model name, fall back to a fast v4 model (e.g. gemini-3.5-flash).
Always pass proxyCountryCode "us"
(the CLI default) when browserSettings is present.

Python CLIs must import `/opt/hatch/skills/skill-creator/bin/dynamic_credentials.py` and call `add_surrogate_to_request(...)`, `url_with_surrogate_query_param(...)`, or `url_with_surrogate_path_segment(...)` before authenticated requests, matching where the provider reads the key. If they use `urllib`, read JSON responses with `read_json_response(resp)` from the same helper instead of calling `resp.read()` directly. They must send only `hsurr:*` values, and only to the provider's API host
(`api.browser-use.com`).

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.browser-use`.

