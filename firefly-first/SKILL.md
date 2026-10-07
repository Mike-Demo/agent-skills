---
name: "firefly_first"
description: "Cost-saving router for media generation: try Adobe Firefly's web app first (free/unlimited on the paid Adobe account) and only fall back to billable Pexo video generation when Firefly genuinely can't do the job. Trigger before any Pexo request, and for any video ask where cost matters."
---

# Firefly First (Credit Saver)

## Purpose
Minimize Pexo credit spend by routing every media request through free Adobe Firefly web generation first, escalating to Pexo only for work Firefly cannot do.

## Workflow
1. Classify the request using the decision matrix in `references/routing-matrix.md`.
2. **Firefly-able** (any still image; simple single-clip video with no lip sync, music, narration, or multi-shot assembly): run it via the `adobe-firefly` skill first. Show the result to the user.
3. **Pexo-only** (multi-shot 5–120s finished video, lip sync, AI music/TTS narration, subtitles/transitions, or a specific Pexo model look): skip Firefly and go straight to the `pexo-agent` skill — say why Firefly was skipped.
4. **Firefly tried, not good enough**: show the Firefly output, name exactly what falls short, and ask before spending Pexo credits. Never auto-escalate a failed free attempt into a paid one.
5. When escalating to Pexo, hand over: the brief, any Firefly attempts (as reference), and the Pexo skill's own billing-confirmation rules still apply unchanged — every billable batch needs the user's explicit approval.

## Output Contract
- The routing decision (Firefly / Pexo / Firefly-then-Pexo) with one-line justification
- For Firefly path: generated file(s), verified as real media bytes
- For Pexo path: project created per the pexo-agent skill; credit estimate shown to the user before any billing confirmation

## Operating Rules
1. Stills never go to Pexo. Firefly's Adobe models are unlimited — there is no cost reason to generate a still image anywhere else.
2. Never spend Pexo credits on exploration. If the shape of the request is unclear, clarify with the user before any billable step.
3. A Firefly attempt that fails does not authorize Pexo spend. Report the failure, show what was produced (if anything), and ask.
4. When Firefly is skipped, say so in one line: what Pexo-only capability the request needs (e.g. "needs lip sync — Firefly can't do that").
5. Cost estimates come from Pexo itself (its CONFIRM step), never invented. Relay Pexo's estimate verbatim and wait for explicit approval.
6. This skill routes; it does not replace the `adobe-firefly` or `pexo-agent` skills' own auth, safety, or confirmation rules.
