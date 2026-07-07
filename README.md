<div align="center">

# 🎬 Auto Movie Director

### A ComfyUI **custom node pack**: type a movie idea, get a finished short film — with sound.

Seven nodes (planner · renderer · stitcher · storyboard + helpers) you can wire any way you like.
The included workflow is just the recommended wiring — **the nodes are the product.**

[![License: MIT](https://img.shields.io/badge/License-MIT-orange.svg)](LICENSE)
[![ComfyUI](https://img.shields.io/badge/ComfyUI-custom_node_pack-blue)](https://github.com/comfyanonymous/ComfyUI)
[![LTX 2.3](https://img.shields.io/badge/LTX-2.3_video+audio-purple)](https://github.com/Lightricks/ComfyUI-LTXVideo)
[![Ollama](https://img.shields.io/badge/Ollama-any_model,_auto--installed-green)](https://ollama.com)

![demo](docs/demo_v2.gif)

*this clip — script, camera work, sound, editing — came from one typed sentence*

</div>

![pipeline](docs/pipeline_v3.png)

<div align="center">

<img src="docs/shot1_v2.jpg" width="32%"/> <img src="docs/shot2_v2.jpg" width="32%"/> <img src="docs/shot3_v2.jpg" width="32%"/>

*three shots from one auto-generated film — same character, nobody touched a node between scenes*

</div>

---

## You approve the film before it renders

Queue once in **storyboard mode**: a frame per scene in minutes, laid out like an edit bay with timecodes and the script under each shot. Like it? Flip one switch, queue again — every scene **starts from the exact frame you approved** and comes back with motion and a generated soundtrack, stitched into one MP4 that plays right on the node.

![storyboard](docs/storyboard_v2.png)

Per-scene prompt boxes live on the Planner node — type into any scene to override it, leave it blank and the AI writes it. Change the scene count and the boxes (with their thumbnails) follow.

## Quick start

```bash
cd ComfyUI/custom_nodes
git clone https://github.com/AdamGman/ComfyUI-AutoMovieDirector
```

Restart ComfyUI → open **`example_workflows/Auto Movie Director.json`** → type your idea → Queue.
That's the storyboard. Approve it, set mode to `2) full movie`, Queue again. 🍿

## How long does it take?

![render times](docs/timings_v3.png)

## Everything is a dial

| Story | Picture |
|---|---|
| **Scenes**: 1–24, each with its own optional prompt box | **Resolution**: anything /32 up to 2048² (1536×864 = 24 GB sweet spot) |
| **Length**: any seconds per scene (frames auto-snap to LTX's grid) | **Frame rate**: 8–60 fps (24 = cinema) |
| **LLM**: any [Ollama model](https://ollama.com/library) by name — **auto-downloaded** if missing | **Sampler / scheduler / steps / cfg / seed**: fully exposed |
| **Style**: one field appended to every scene | **`storyboard_strength`**: how hard scenes stick to approved frames |
| **Per-scene overrides**: your line beats the AI's | **`preview_size`**: storyboard speed vs detail |

**Four render modes:** `1) storyboard preview` → **`2) full movie (continuity)`** — the movie mode: the planner designs 2–5 recurring locations, each gets a rendered anchor plate so the camera returns to *the same room* every time; scenes tagged `flow` physically start from the previous scene's last frame so action carries across cuts; and every location's anchor **evolves** with what happens in it (tape the window in scene 9 and it's still taped in scene 14). Or `3) from storyboard frames` (each scene starts on its approved board frame) and `4) pure text2vid` (independent scenes, freest look). `flow_strength` / `cut_strength` dials control how hard continuity binds.

> Long films: launch ComfyUI with `--cache-lru 60` (the included start script does) to keep RAM bounded across many scenes.

## The nodes you get

All under the **AdamGman → 🎬 Auto Movie Director** category in the node menu:

| Node | Job |
|---|---|
| 🎬 **Movie Planner (Ollama)** | idea → plot, character sheet, per-scene prompts with shot grammar + sound design. Grows per-scene override boxes with live thumbnails right on the node. |
| 🎬 **Movie Renderer (LTX scenes)** | one node that expands into a full LTX render chain per scene at runtime — storyboard / img2vid / text2vid modes, every quality dial exposed. |
| 🎬 **Movie Stitcher** | ffmpeg-concats the scenes into one MP4 (H.264 + AAC), previews it on the node. |
| 🎬 Storyboard · Scene Writer · Load Frame · Path Join | the renderer's building blocks — usable standalone in your own graphs. |

## What the AI director actually does

The Planner turns your sentence into a **three-act screenplay**: a character sheet with countable anatomy repeated verbatim into every scene (that's what keeps the same hero across cuts), an explicit shot type and camera move per scene (wide → tracking → push-in — never the same twice in a row), scene-to-scene continuity (time of day progresses, damage persists), and a concrete **`Audio:` line per scene** that LTX renders into an actual soundtrack. No Ollama running? A built-in act-structure fallback still delivers.

<details>
<summary><b>Requirements</b></summary>

| What | Why |
|---|---|
| [ComfyUI](https://github.com/comfyanonymous/ComfyUI) 0.27+ | node-expansion API |
| [ComfyUI-LTXVideo](https://github.com/Lightricks/ComfyUI-LTXVideo) | LTX video+audio nodes |
| [ComfyUI-GGUF](https://github.com/city96/ComfyUI-GGUF) | only for GGUF-quantized LTX transformers |
| [Ollama](https://ollama.com) | the screenwriter LLM (optional — fallback built in) |
| `imageio-ffmpeg` | stitching (`pip install imageio-ffmpeg` if you don't have VideoHelperSuite) |

**LTX 2.3 models** in your model folders: transformer (fp8 **or** GGUF + distilled LoRA), Gemma text encoder + LTX text projection, video VAE + audio VAE, and the LTX spatial upscaler (recommended — sharpens storyboard frames before they guide the render).

**Optional but worth it:** [LTX 2.3 Crisp Enhance](https://civitai.com/models/2535622/ltx-23-enhancers) — the example workflow ships with it pre-wired at 0.5 (A/B tested: visibly more micro-detail, water beading, crisper materials). Don't have it? Delete that one node.

</details>

<details>
<summary><b>Where files land</b></summary>

Everything for one movie lives in one project folder:

```
output/auto_movie/<your_movie_title>_<id>/
├── <name>_<run>.mp4       ← 🍿 the finished film (video + audio)
├── plot.txt               ← the script
├── storyboard/            ← storyboard.png · scene_XX.png · scenes.txt
└── takes/<run>/           ← the individual scene MP4s of each render
```

</details>

<details>
<summary><b>Tips that matter</b></summary>

- **Character consistency**: describe your hero *physically* in the idea — body plan, limb count, eye color, size ("a boxy rust-orange robot on two black tank treads with one big cyan eye…"). The planner repeats those exact facts in every scene prompt.
- **Keep seed + prompts unchanged** between storyboard and full render — that's how approved frames are matched.
- **img2vid vs text2vid**: img2vid follows your approved frames (and inherits their look); text2vid is freest and most photographic. Try both — the plan is cached, so re-queuing is cheap.
- Long films: decoded frames stay in RAM during the run — 12+ scenes at high res wants 32 GB+ free.

</details>

---

<div align="center">

[Security policy](SECURITY.md) · **by [AdamGman](https://github.com/AdamGman)** · MIT

</div>
