# PixelArtAI-PoC

Exploring AI-assisted pixel art generation and editing with local LLMs and constrained pixel data.

## Goal

This repository is a proof of concept for a pixel-art editor where an LLM manipulates pixel data directly instead of generating a finished image.

The first target is a fully local workflow using Ollama, so the core experience can work without mandatory per-use API fees.

## Research question

**RQ1:** Can a local LLM generate and edit usable pixel art while strictly preserving canvas size, palette constraints, and output format?

## Initial local models

- `llama3:latest`
- `qwen3-coder:30b`
- `qwen2.5-coder:7b`
- `llama3.2-vision:11b`
- `llama3.2-vision:90b`

## Experiment roadmap

1. **Text -> Pixel**: generate a 16x16 sprite from a text prompt.
2. **Local edit**: modify a small part of an existing sprite while preserving everything else.
3. **Pose change**: change orientation or pose while keeping character identity and palette.
4. **Image -> Pixel**: use a vision model to convert a reference image into constrained pixel data.
5. **Provider abstraction**: make the editor backend swappable between Ollama and optional cloud APIs.

See [docs/experiment-plan.md](docs/experiment-plan.md).

## Repository structure

```text
docs/
  concept.md
  experiment-plan.md
  experiments/
    001-text-to-pixel.md
prompts/
  001-text-to-pixel.txt
results/
  001/
src/
```

## Current status

PoC setup in progress. Experiment 001 is prepared for the first Ollama run.
