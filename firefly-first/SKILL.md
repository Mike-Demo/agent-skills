---
name: "firefly_first"
description: "Cost-saving router for generative media creation and editing: try Adobe Firefly's web app first (live interface shows no additional charge on the paid Adobe account) and only fall back to billable Pexo video generation when Firefly genuinely can't do the job. Trigger for explicit image/video creation or editing requests where cost matters; always honors an explicit provider choice."
---

# Firefly First (Credit Saver)

## Purpose
Minimize Pexo credit spend by routing generative media requests through Adobe Firefly's web app first, escalating to Pexo only for work Firefly cannot do — without ever overriding the user's explicit choices.

## Trigger
Use when the request is to **create or edit media** (an image, a video clip, or a finished video) and cost matters, e.g.:
- "generate an image of…", "make a thumbnail/social card/icon", "edit this photo…"
- "make a short video clip of…", "turn this into a video", "produce a promo video"

Do NOT trigger for:
- Finding, searching, viewing, or analyzing existing media
- Playback, format conversion, compression, or ordinary file edits (crop/rename)
- Delivery-only asks ("send me that video", "download this")
- Questions *about* tools or pricing with no creation/editing ask

## Precedence
When instructions conflict, this order wins:
1. Safety and policy
2. The user's explicit requirements — including an explicit provider choice ("use Pexo for this")
3. The downstream skill's own auth/billing/confirmation rules
4. This router's cost preference (Firefly first)
5. Quality preferences

A user saying "use Pexo" always beats the router. A vague "make it cheap" never overrides an explicit provider choice.

## Workflow
1. Classify the request per `references/routing-matrix.md`. If any of these are unclear, ask before any billable step: media type (still / clip / finished video), explicit provider preference, budget cap, deadline.
2. Treat the matrix as versioned guidance, not permanent truth: capabilities marked volatile were last verified on the dates shown. When a volatile claim decides the route, re-check it live (Firefly UI, Pexo skill behavior) rather than trusting the snapshot.
3. **Firefly-able** (stills; image edits; simple single clips): run via the `adobe-firefly` skill first. Show the result.
4. **Pexo-only** (finished multi-shot video, lip sync, mixed audio post-production, subtitles/transitions pipeline, specific Pexo models, URL/script-to-video): go straight to `pexo-agent` — state in one line which capability forced the route.
5. **Firefly outage**: if the web app is unreachable or erroring, do not silently switch to paid generation. Report the outage and offer: wait/retry, or proceed with Pexo with explicit approval.
6. **Pexo insufficient credits**: stop. Tell the user credits are short and where to top up (`https://pexo.ai/home?billing=credits`); resume only after they confirm.
7. **Firefly tried, not good enough**: show the output, name exactly what falls short, and ask before any Pexo spend. A failed free attempt never authorizes a paid one.
8. Escalation handoff to Pexo: the brief verbatim, Firefly attempts as reference inputs, and nothing else the Pexo skill doesn't ask for.

## Output Contract
- The routing decision (Firefly / Pexo / Firefly-then-Pexo) with one-line justification citing the capability that decided it
- For Firefly path: generated file(s), verified as real media bytes
- For Pexo path: the exact batch sent for approval — named provider, model/settings, Pexo's displayed estimate — and the user's explicit approval before confirmation

## Operating Rules
1. Default stills and image edits to Firefly; honor an explicit user provider choice without argument.
2. Cost language: say "the live interface shows no additional charge" — never "free/unlimited" as a permanent promise. Read the live readout every run.
3. Never spend Pexo credits on exploration or on a vague brief. Clarify first.
4. Every potentially billable batch — including retries and changed batches — needs its own explicit approval, naming provider, batch contents, model/settings, and the displayed estimate.
5. Pexo's own billing-confirmation rules apply unchanged; this router adds no shortcut around them.
6. Cost estimates come from Pexo's CONFIRM step, relayed verbatim. Never invent estimates.
7. This skill routes; it does not replace the `adobe-firefly` or `pexo-agent` skills' auth, safety, or confirmation rules.
