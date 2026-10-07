# Routing Matrix: Firefly web vs Pexo

**Version 2 — revised 2026-10-06 after Copilot audit.** Capability claims below are snapshots; items marked *(volatile)* were last verified on the date shown. When a volatile claim decides the route, re-check it live instead of trusting this file.

## Cost basis (read live every run)

- Firefly web on the paid Adobe account: Adobe Firefly models showed "Unlimited access" (no per-generation charge) when last verified 2026-10-06. Partner models inside Firefly cost credits. **Read the live "Uses N credits" / "Unlimited access" readout before generating** — plans change, and Adobe's unlimited offer has exclusions (e.g. Teams/Enterprise, selected models/resolutions).
- Pexo: every billable batch needs explicit user approval; estimates come from Pexo's CONFIRM step, relayed verbatim.

## Firefly capability split *(volatile — last verified 2026-10-06)*

| Capability | Status on paid consumer plan | Routing implication |
|---|---|---|
| Text-to-image (Adobe models) | Yes — grid or single output depending on model | Default for all stills |
| Generative Fill / Expand, object/background removal | Yes | Default for image edits |
| Generate Video (short single clips) | Yes | Try first for simple clips |
| Timeline / multi-clip video editor | Exists per vendor docs — **revalidate live** before routing on it | Finished multi-shot assembled videos default to Pexo until verified |
| Audio generation (music, speech, sound effects) | Firefly has been adding audio features — **revalidate live**; not a full post-production pipeline | Mixed audio post-production (music + narration + subtitles in one finished piece) goes to Pexo |
| Lip sync | Documented for translation workflows / eligible enterprise plans; **not** in standard Generate Video on consumer plans | Lip-sync work goes to Pexo |
| Reference/style/composition controls | Yes (model-dependent) | — |

## Default to Firefly

- Any still image or image edit, unless the user explicitly names Pexo.
- Simple single video clips with no audio, narration, lip sync, or multi-shot assembly — try Firefly first; escalate on quality only with user approval.

## Pexo-only (skip Firefly, name the deciding capability)

- Finished multi-shot videos with transitions and assembly.
- Lip sync / talking-head work.
- Mixed audio post-production: AI music + TTS narration/voiceover + subtitles in one pipeline.
- A specific Pexo-catalog model look the user asks for (catalog is dynamic — read it live, don't trust snapshots).
- URL-to-video / script-to-video pipeline work.

## Pexo claims validated against the real skill *(verified 2026-10-06 from pexo-agent SKILL.md)*

- Accepts image inputs: **verified** — upload script plus `<original-image>` / `<original-video>` / `<original-audio>` asset tags; images: jpg, png, webp, bmp, tiff, heic.
- Output: 5–120s finished videos; 16:9, 9:16, 1:1 — **verified** in skill doc.
- Auto model selection across its catalog (Seedance 2, Kling 3.0, HappyHorse named in skill doc — catalog changes; read live).
- Billing: every billable batch requires explicit approval; insufficient-credits flow documented — **verified** in skill doc.

## Historical cost observations (not routing logic)

- Oct 2026: ~432 credits for a 10s header video; ~1,081 credits for two clips. Real spends on this account, kept here for context only — never use as estimates; estimates come from Pexo per batch.

## Handoff notes

- Firefly → Pexo: pass Firefly outputs as reference image inputs plus the brief verbatim.
- Pexo owns project creation, polling, previews, billing confirmation, and delivery — this router duplicates none of that.
