# Concept

## Problem

Typical image-generation systems return a finished raster image. That is convenient for illustration, but awkward for traditional game workflows that need:

- exact pixel dimensions,
- a fixed palette,
- deterministic pixel-level edits,
- sprite-to-sprite consistency,
- small animation deltas,
- and exportable frame data.

## Hypothesis

A language model may be able to treat a bitmap as structured text data and directly manipulate a constrained 2D pixel matrix.

If this works, the editor can ask the model to make changes such as:

- "close the right eye",
- "turn the character to the right",
- "raise the right arm",
- "make the next walking frame",

while preserving most existing pixels.

## Product direction

The long-term editor should separate the UI from the AI backend.

```text
Pixel Editor
    |
    v
IPixelAiProvider
    |
    +-- OllamaProvider
    +-- OpenAIProvider
    +-- OtherProvider
```

The local Ollama path is the primary PoC target. Cloud APIs should be optional rather than mandatory.

## Core constraints

The model output should be machine-validatable.

For the initial PoC:

- Canvas: 16x16
- Palette: 4 entries
- Pixel encoding: one palette index per character
- No colors outside the palette
- No prose around the pixel data
