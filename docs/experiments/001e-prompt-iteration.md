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

### Raw output

```text
0000000000000000
0000000000000000
0000000000000000
0000000000000000
0000000222200000
0000002222220000
0000022222222000
0000222222222200
0002222222222220
0022221111222222
0222221111222222
0222221111222222
0222221111222222
0022222222222220
0002222222222200
0000000000000000
```

### Mechanical validation

- [x] Exactly 16 rows
- [x] Exactly 16 characters per row
- [x] Only 0/1/2/3
- [x] No extra prose

### Visual / semantic validation

- [x] Coherent slime silhouette
- [ ] Black outline is visibly used
- [x] Red is the primary body color
- [ ] Exactly two recognizable eyes
- [x] White, if used, is limited to eye highlights — white is not used
- [x] No isolated stray pixels

### Assessment

PARTIAL. The prompt iteration changed the output, but only in a localized region. Compared with Run 001-D, the silhouette is effectively preserved while rows 10-13 gain a centered 4x4 block of black pixels (`1111`). This demonstrates that the model can modify a previous-looking spatial pattern in response to stronger semantic instructions, but it did not realize the requested structure correctly.

The black block is not a continuous outline and does not form two distinguishable eyes. The model appears to have understood that black pixels were required for facial detail, but collapsed the requested two-eye structure into one rectangular feature.

Notably, the result is extremely close to the previous 30B output. This may indicate deterministic or strongly preferred generation under the same model/prompt context, and should be investigated separately from artistic quality.

### Conclusion

Explicit prompting improved **palette-role compliance** (black was introduced) without producing the requested facial topology. The next prompt iteration should represent the eye constraint more structurally rather than only semantically—for example, require two separated black components with at least one red pixel column between them.

Do not proceed to local-edit testing yet; first determine whether the model can produce two distinct facial features in the initial generation.
