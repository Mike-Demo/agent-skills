---
name: "rezi"
description: "Manage Rezi resumes and search Rezi's job board. Use when Demo wants to inspect, tailor, or write a resume in their Rezi account, or search Rezi jobs by role and location."
---

# Rezi

## Purpose
Use Rezi with the user-connected `custom.rezi` credential.

Demo uses Rezi Pro for tailored CVs. This skill lets you:
- `list_resumes` / `read_resume`: inspect their resumes (master + tailored versions)
- `get_resume_format`: check editable sections before writing
- `write_resume`: create a new tailored resume (omit `resume_id`) or update one
- `search_jobs` / `get_job_details`: search Rezi's job board by role + location
  (if the search backend returns 500s, retry before reporting it broken)

## CLI
`bin/rezi-mcp` — `rezi-mcp tools/list`, or
`rezi-mcp tools/call <tool> '<json-args>'`.
Examples:
- `rezi-mcp tools/call list_resumes`
- `rezi-mcp tools/call read_resume '{"resume_id": "..."}'`
- `rezi-mcp tools/call search_jobs '{"role": "Head of Partnerships", "location": "United States", "remote": true}'`

## Operating Rules
1. Never invent resume content: tailor from the master resume and the job
   description only.
2. `write_resume` creates/updates inside the user's Rezi account — confirm
   with the user before writing, and read back to verify.
3. Never submit job applications; Rezi tools only prepare resumes and surface
   jobs.

## Tooling
Add service-specific CLIs under `~/workspace/skills/rezi/bin/`.

CLIs must use authd or shared connector helpers for authenticated requests. They must not read OAuth client credentials, browser callback payloads, refresh tokens, access tokens, or Muse auth files directly.

## Auth
The credential is already stored; nothing here collects one. Never ask the user to paste a raw key in chat, set a secret environment variable, pass a secret flag, or write an auth file.

A 401 or 403 is a question about the request before it is a question about the key. Check that the credential was attached at all: a request built without the helpers named under Tooling carries nothing, and that looks exactly like a wrong or under-scoped token. Only once a request that did carry the credential is still rejected, call `credentials.request_api_access` with `reconnect` to replace it. The connector is stored as `custom.rezi`.
