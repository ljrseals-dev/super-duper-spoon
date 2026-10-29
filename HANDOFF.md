# ElevenX Voice Studio UI — Design Handoff

**For:** Claude (design tightening pass)  
**Source:** ElevenX 0.3.0 integrated broadcast package  
**Date:** 2026-10-28

## What this is

Single-page local Voice Studio for reference-based TTS (Coqui XTTS-v2).
Dark theme, cyan/violet accent, Inter system stack.

## Screens / regions (one SPA)

1. **Header** — Brand mark “X”, ELEVENX wordmark, “LOCAL VOICE STUDIO · EARLY ACCESS” pill
2. **Hero** — Eyebrow, H1 “Make words sound like someone.”, subtitle, local API status dot
3. **Create panel (01 / CREATE)**
   - Script textarea (0–5000 chars)
   - Delivery style chips: Neutral, Confident, Empathetic, Authoritative, Intense
   - Language select (16 langs)
   - Voice reference file upload (WAV/FLAC/OGG/AIFF, 3–30s)
   - Consent checkbox (required)
   - Stream sentences checkbox (WebSocket progressive PCM)
   - Primary CTA: “✦ Generate voice”
   - Status line
4. **Studio output panel (02 / LISTEN)**
   - Empty state: waveform bars + “Your next voice starts here”
   - Result state: title, meta (duration / rate / emotion / lang), audio player, Download WAV
   - Side info cards: Reference-based synthesis, Local-first workflow, Codec integration status
5. **Footer** — Product line + hardware note

## Responsive

- Desktop: 2-column grid (create | output)
- Mobile: stacked single column

## Files in this zip

- `index.html` — full self-contained UI (inline CSS + JS)
- `elevenx-poster.svg` — brand poster asset
- `HANDOFF.md` — this file
- `screenshots/` — captured states (if present)

## Design notes / tighten candidates

- Emotion chip grid can feel uneven on narrow widths
- File input styling is browser-native (opportunity for custom dropzone)
- Result player is minimal after generation
- Codec status copy still says “not active” — config now has codec enabled; copy should follow product truth
- Consent + stream checkboxes are easy to miss under the form
- No dedicated loading / error / streaming progress UI beyond status text
- Accessibility: focus rings and live regions exist partially; worth a full pass

## Tech constraints

- Must stay local-first / loopback-friendly
- Consent is a product requirement (do not remove)
- API: POST multipart synthesize + optional WebSocket stream
