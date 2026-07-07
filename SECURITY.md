# Security Policy

## Reporting a vulnerability

If you find a way to make these nodes do something destructive or leak data outside their output folders, please open a **private security advisory**:

https://github.com/AdamGman/ComfyUI-AutoMovieDirector/security/advisories/new

Don't open a public issue for exploitable cases — ComfyUI workflows are shared and imported constantly, and a public write-up makes every user running this pack immediately attackable through a malicious workflow file.

## What counts as a vulnerability

- **Path escapes**: `output_dir`, `save_dir`, `filename_prefix`, or storyboard frame paths being abused to write or overwrite files outside the intended output locations (path traversal through a crafted workflow JSON)
- **Arbitrary file read**: `AMD_LoadFrame` or the stitcher being steered to read files a workflow shouldn't reach and embed them in outputs
- **Command injection**: anything in a prompt, plan JSON, or file path reaching the ffmpeg command line or the concat list in a way that executes attacker-controlled arguments
- **SSRF via `ollama_url`**: tricking the planner into making requests to internal services beyond a user-configured Ollama endpoint in a way the user didn't set themselves
- **Model auto-pull abuse**: the Ollama auto-download being redirected to fetch from somewhere other than the user's configured Ollama instance

## What's NOT in scope

- Bugs in ComfyUI itself, ComfyUI-LTXVideo, Ollama, or ffmpeg (report upstream)
- Prompt content: the LLM writes scene text; what a local model says is between you and your model
- Malicious *models* — GGUF/safetensors you download are your responsibility
- Resource exhaustion from queuing a 24-scene 4K movie on a laptop (that's not an exploit, that's Tuesday)
- Anything requiring an already-compromised machine or ComfyUI instance exposed to the open internet (don't do that)

## Response time

Personal project, one maintainer. Best effort, no SLA. Path-escape and command-injection reports get attention first.
