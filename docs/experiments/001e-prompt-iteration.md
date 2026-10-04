# Experiment 001-E: Prompt Iteration

## Purpose

Test whether a more explicit spatial and semantic prompt improves the best baseline model, `qwen3-coder:30b`, without changing the 16x16 canvas or four-color palette.

This run follows Experiment 001-D, where the model produced a coherent slime-like silhouette but omitted the black outline and face.

## Controlled variables

- Runtime: Ollama
- Model: `qwen3-coder:30b`
- Canvas: 16x16
- Palette: unchanged
- Output encoding: unchanged

## Changed variable

Only the prompt is changed. The new prompt explicitly requires:

- one connected body,
- rounded/blob-like silhouette,
- continuous black outline where practical,
- red interior,
- exactly two visible eyes,
- black eye pixels,
- white only as small eye highlights,
- no isolated pixels,
- transparent margin around the sprite.

## Prompt

See [../../prompts/001e-text-to-pixel-explicit.txt](../../prompts/001e-text-to-pixel-explicit.txt).

## Success criteria

### Mechanical

- [ ] Exactly 16 rows
- [ ] Exactly 16 characters per row
- [ ] Only 0/1/2/3
- [ ] No extra prose

### Visual / semantic

- [ ] Coherent slime silhouette
- [ ] Black outline is visibly used
- [ ] Red is the primary body color
- [ ] Exactly two recognizable eyes
- [ ] White, if used, is limited to eye highlights
- [ ] No isolated stray pixels

## Result

Pending.
