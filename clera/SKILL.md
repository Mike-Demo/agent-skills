---
name: clera
description: "Candidate dashboard for the Clera account via the authenticated MCP server (mcp.getclera.com). Use when Demo asks about Clera applications, matches, or candidate-dashboard status."
---

# Clera MCP skill

Candidate dashboard for Demo's Clera account (account kind: candidate) via
the authenticated MCP server `https://mcp.getclera.com/mcp`.

## Auth

Manual OAuth 2.0 + PKCE flow (the Secure Vault hosted flow can't complete
this one — it demands a Client ID and rejects DCR-registered ones). Do
dynamic client registration at `https://clerk.getclera.com/oauth/register`
with **curl**, not urllib — urllib gets RemoteDisconnected against that
host. Tokens held in local credential storage (mode 600, never committed
to the repo), scopes `profile email offline_access`; the CLI auto-refreshes.

## CLI: bin/clera-mcp

`whoami | matches | pipeline | roles [--query Q] | jobs [--query Q] |
applications | saved | preferences | resumes | interviews`
plus `call <tool> <json-args>` for any of the 38 tools.

## Clera's own playbook (from the whoami guide — follow it)

- Two role sources, fixed order: **searchCleraRoles first** (startups Clera
  works with; every result is an intro Clera can make), external board
  (`searchJobs`) only when that comes up short or the candidate asks. Always
  say which source a result came from.
- `listMatches` is the Matches page. `likeMatch` = "I want it", nothing goes
  to the company — **never say "applied"**. `declineMatch` needs a reason
  and changes account state — confirm with Demo before declining.
- Interview accept/decline are answers the company gets — confirm first.
- **Never show opportunityId, jobId or questionId to the user.**
- Application tracker tools (list/track/update/deleteApplication,
  generateApplicationDocuments) cover jobs anywhere, not just Clera.
  Confirm before any write or delete (track, update, deleteApplication) —
  show what will change first.

## Setup items

`whoami` may report setup items (e.g. "Add your skills from LinkedIn") —
flag them to Demo when seen.

## Clera workflow note (from Demo, 2026-10-01)

When Demo thumbs-ups (likes) a job on Clera, they're supposed to message
Clera's AI next. So likeMatch is step one; the AI conversation is step two.
If Demo reports liking something, ask whether they messaged the AI / offer
to draft the message.
