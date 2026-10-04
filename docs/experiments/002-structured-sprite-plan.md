# Experiment 002: Structured Sprite Plan

## Purpose

Test a different architecture after Experiment 001 showed limits in direct natural-language-to-bitmap generation.

Instead of asking the LLM to emit all 256 pixels, ask it to produce a compact structured sprite plan. A deterministic renderer can then convert that plan into the 16x16 bitmap.

## Hypothesis

The LLM will handle semantic composition more reliably when it chooses named features and coordinates rather than serializing the complete raster.

This also creates a better editing model: future instructions such as "close the left eye" can modify an eye feature instead of regenerating the whole bitmap.

## Model

Initial test: `qwen3-coder:30b` via Ollama.

## Representation

- Body: horizontal spans per occupied row
- Eyes: named pixel groups
- Highlights: optional pixel groups
- Renderer responsibility: initialize transparent canvas, paint body spans, then features
- Validator responsibility: bounds, schema, eye count, symmetry/separation rules

## Prompt

See [../../prompts/002-structured-sprite-plan.txt](../../prompts/002-structured-sprite-plan.txt).

## Success criteria

- [ ] Valid JSON only
- [ ] Canvas is exactly 16x16
- [ ] Body rows use valid coordinates
- [ ] Exactly two named eyes
- [ ] Eye coordinates are in bounds
- [ ] Eyes are spatially separated
- [ ] Plan can be rendered deterministically
- [ ] Rendered sprite is recognizably slime-like

## Result

Pending.
