# 🌿 Touch Grass Card

A tiny open-weight AI that lives **entirely in your browser** and deals you one small outdoor mission — then your job is to put the phone away and go do it.

Built for the [Hacktoberfest Open-Source AI Challenge: Week 1 — "Touch Grass"](https://dev.to/challenges/hacktoberfest-week1-2026-10-05). Entering the **Best Use of Gemma** category.

## What it does

Pick where you are, how many minutes you can spare, and a vibe. A locally-running **Gemma 3 270M** generates a mission card:

- **Title** — a catchy name for the mission
- **Mission** — exactly what to do, in 2–3 sentences
- **Look for** — three things to notice while you're out
- **Proof** — one photo to take when you're done

Hit "I went outside and did it" and the page keeps a little counter of missions completed on your device.

## Why open weights matter here

- **Runs with zero signal.** After the one-time model download, inference happens on-device (WebGPU, WASM fallback). It works on a trail, in airplane mode, anywhere.
- **Private by architecture.** There is no server. Your location text never leaves the page.
- **Costs nothing to run.** No API keys, no per-token bills, no accounts — a static file.- **Swappable.** The whole "brain" is one model ID in `index.html`. Swap in a bigger Gemma (or a fine-tune) and the page gets smarter without changing anything else.

## Run itAny static server works:

```bash
cd hacktoberfest-touch-grass
python3 -m http.server 8000
# open http://localhost:8000
```

Or enable GitHub Pages on this repo and share the link.

**First load downloads the model once** (~1 GB fp32 on both WebGPU and the WASM fallback — see Tech notes for why the smaller quantized WebGPU path is not used) and your browser caches it. After that, reloads are fast and fully offline-capable.
## Tech notes

- Model: [`onnx-community/gemma-3-270m-it-ONNX`](https://huggingface.co/onnx-community/gemma-3-270m-it-ONNX) via `@huggingface/transformers` 4.2.0.
- A known [ONNX Runtime WebGPU fp16 overflow bug](https://github.com/microsoft/onnxruntime/issues/26732) makes this model emit garbage `<unused56>` tokens instead of text — the app deliberately uses **fp32 on both WebGPU and the WASM fallback** (verified end-to-end in a real browser, zero garbling across 16/16 test draws) and has a `<unused\d+>` garbage-token guard that surfaces raw output instead of a blank card.
- The app only *generates* mission text. I personally field-tested one card (walk to a nearby park) — photo and notes in `FIELD_NOTES.md`.
## Build log

- **Oct 10 (Fri):** scaffolded the page; ran 6 cards end-to-end in Chrome on my Mac, fp32 path clean.
- **Oct 11 (Sat):** ran 10 more draws (16 total, all clean). Of the 16 missions only ~3 were actually practical — small models are honest that way. The safety instruction in the system prompt was also ignored twice (`A Mobile Sensor Jammer`, `Rain Cloud Rescue`, which told me to find a missing/kidnapped person and call police), which was a useful reminder that a system prompt is a nudge, not a guarantee.
- **Oct 11 (Sat, midday):** drew card #9 *"Unplug to Breathe"* (take a short walk to a nearby park) and actually went. Park near home in SW Corvallis, sunny.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app — one file, no build step |
| `FIELD_NOTES.md` | Field-test log: the card I drew, the photo, and honest notes |
| `dev-post.md` | The DEV.to submission draft |

## License
MIT.
