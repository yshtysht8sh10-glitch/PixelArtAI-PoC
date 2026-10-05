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

### Raw model output

```json
{
  "changes": [
    { "x": 6, "y": 8, "color": 0 }
  ]
}
```

### Validation

- [x] Valid JSON only
- [x] Only `changes` are returned
- [x] All coordinates are in bounds
- [x] All colors are valid palette indices
- [x] Right eye at (9,8) remains unchanged
- [x] Unrelated pixels remain unchanged
- [ ] Applying the patch creates a recognizable closed/winking left eye
- [x] Patch is minimal

### Applied result

```text
0000000000000000
0000000000000000
0000000000000000
0000000000000000
0000022222200000
0000222222220000
0002222222222000
0022222222222200
0022220221222200
0002222222200000
0000222222220000
0000022222200000
0000000000000000
0000000000000000
0000000000000000
0000000000000000
```

### Assessment

FAIL for visual editing quality, PASS for patch-format compliance.

The model chose to replace the existing left-eye pixel at (6,8) with transparent color 0. This removes the eye rather than drawing a recognizable closed/winking eye.

The result also reveals an important semantic issue: because (6,8) lies inside the red body, replacing the eye with transparent color creates a transparent hole in the body. A visually sensible eye removal would at minimum restore the underlying body color (2), but even that would only erase the eye rather than depict a wink.

This is consistent with Experiment 003, where the structured edit set the left eye's pixel list to empty. Changing from semantic feature JSON to a generic pixel-patch output did not cause the model to invent a visual representation for a closed eye.

### Conclusion

The generic patch architecture successfully guarantees locality and minimal changes, but the current text-only model does not demonstrate sufficient pixel-art visual reasoning for this edit. The next experiment should not merely add more output constraints. A useful next question is whether a vision-capable local model, given a rendered sprite/image rather than only textual pixel rows, can reason about the desired visual edit more effectively.

