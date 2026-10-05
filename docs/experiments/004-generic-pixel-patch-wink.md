# Experiment 004: Generic Pixel Patch — Wink

## Purpose

Test whether a local LLM can perform a useful local pixel-art edit without requiring semantic renderer primitives such as predefined eye states.

The application provides the existing bitmap and a natural-language edit request. The model returns only a generic list of pixel changes.

## Why this experiment

Experiment 003 showed that structured local editing can preserve unrelated data, but a schema such as `eye.state = closed` would require the application to define rendering semantics for potentially unlimited object types, poses, viewing angles, and styles.

Experiment 004 removes that requirement. The model remains responsible for deciding what the requested visual change should look like. The application only validates and applies generic pixel patches.

## Architecture under test

```text
User instruction
    ↓
LLM + current bitmap
    ↓
Generic Pixel Patch
    ↓
Validator
    ↓
Apply patch to existing bitmap
    ↓
Edited bitmap
```

The validator does not need to understand eyes, humans, slimes, engines, trees, or any other semantic object.

## Model

Initial test: `qwen3-coder:30b` via Ollama.

## Prompt

See [../../prompts/004-generic-pixel-patch-wink.txt](../../prompts/004-generic-pixel-patch-wink.txt).

## Success criteria

- [ ] Valid JSON only
- [ ] Only `changes` are returned
- [ ] All coordinates are in bounds
- [ ] All colors are valid palette indices
- [ ] Right eye at (9,8) remains unchanged
- [ ] Unrelated pixels remain unchanged
- [ ] Applying the patch creates a recognizable closed/winking left eye
- [ ] The patch is reasonably minimal

## Result

Pending.
