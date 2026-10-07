# Routing Matrix: Firefly web vs Pexo

Free first: Adobe Firefly web app on the paid Adobe account — Adobe Firefly models show "Unlimited access" (verified live 2026-10-06). Partner models inside Firefly cost credits; default to Adobe models unless the user asks otherwise.

## Always Firefly (never Pexo)

| Request | Why |
|---|---|
| Any still image (text-to-image) | Unlimited, no cost rationale for Pexo |
| Image edits (Generative Fill / Expand, background removal, outpainting) | Firefly-native, free |
| Style concepts, icons, splash screens, social cards, thumbnails, storyboards | Stills — Firefly |
| Reference stills *for* a later Pexo video | Generate the still free, then hand it to Pexo as input |

## Try Firefly first, escalate on quality

| Request | Firefly attempt | Escalate to Pexo when |
|---|---|---|
| Simple single video clip (a few seconds, no audio needs) | Firefly web → Generate Video | Motion quality, realism, or prompt adherence falls short — show the clip, name the gap, ask before spending |

## Pexo-only (skip Firefly, say why)

| Request | Missing Firefly capability |
|---|---|
| Finished video 5–120s, multi-shot with transitions | No multi-shot assembly |
| Lip sync / talking head | No lip sync |
| AI music, TTS narration, voiceover, subtitles | No audio post-production |
| Specific model look (Seedance 2, Kling 3.0, HappyHorse…) | Model not in Firefly |
| URL-to-video / script-to-video pipeline | No page-scrape/script pipeline |

## Cost context (observed, not promised)

- Pexo is expensive: ~432 credits for a 10s header video; ~1,081 credits for two clips (observed Oct 2026). Every billable batch needs the user's explicit approval — this never changes.
- Firefly Adobe-model stills and simple generations: no credit charge on the paid account. Always read the live "Uses N credits" / "Unlimited access" readout before generating; plans change.

## Handoff notes

- Firefly → Pexo: pass the Firefly outputs as reference inputs (Pexo accepts image inputs); include the original brief verbatim.
- Pexo → user: the pexo-agent skill owns project creation, polling, previews, billing confirmation, and delivery. This router does not duplicate any of that.
