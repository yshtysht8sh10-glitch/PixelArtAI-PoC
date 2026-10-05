# Experiment 005: Vision Pixel Patch — Wink

## Purpose

Test whether a vision-capable local model can produce a better generic pixel patch for a visual edit than the text/coder model used in Experiment 004.

## Model

- Runtime: Ollama 0.35.0
- Model: `qwen3-vl:8b`
- Input: 512x512 nearest-neighbor enlargement of the original 16x16 sprite
- Requested edit: make the slime wink by closing its left eye

## Model output

```json
{
  "changes": [
    { "x": 6, "y": 8, "color": 2 }
  ]
}
```

## Validation

- [x] Valid JSON
- [x] Coordinate is in bounds
- [x] Palette color is valid
- [x] Right eye remains unchanged
- [x] Unrelated pixels remain unchanged
- [x] Existing left-eye pixel is restored to the underlying body color rather than made transparent
- [ ] A recognizable closed/winking eye is drawn

## Assessment

PARTIAL / visual-edit FAIL.

The vision model made a more context-aware replacement than Experiment 004: it changed the black left-eye pixel to red (body color 2), correctly avoiding a transparent hole in the slime. This suggests the image context helped it understand the pixel's visual background.

However, it still interpreted "close the left eye" as erasing the visible eye rather than inventing a visible closed-eye shape. No new black pixels were added, so the result does not read as a wink.

## Comparison with Experiment 004

- `qwen3-coder:30b`: (6,8) black -> transparent (0), creating a hole.
- `qwen3-vl:8b`: (6,8) black -> red (2), correctly restoring the body but still removing the eye.

Vision improved contextual color reasoning, but did not solve the core visual-generation problem for the wink.

## Conclusion

The generic pixel-patch architecture remains viable for locality and validation, but this single vision test does not demonstrate autonomous pixel-art redesign. The next experiment should distinguish whether the failure comes from the minimal-change wording or from insufficient visual-generation ability—for example by explicitly allowing the model to add nearby pixels while still withholding the desired wink shape.
