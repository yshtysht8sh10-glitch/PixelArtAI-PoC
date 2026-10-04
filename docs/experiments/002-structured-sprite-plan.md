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

### Raw model output

```json
{
  "canvas": { "width": 16, "height": 16 },
  "body": {
    "fill": 2,
    "rows": [
      { "y": 4, "xStart": 5, "xEnd": 10 },
      { "y": 5, "xStart": 4, "xEnd": 11 },
      { "y": 6, "xStart": 3, "xEnd": 12 },
      { "y": 7, "xStart": 2, "xEnd": 13 },
      { "y": 8, "xStart": 2, "xEnd": 13 },
      { "y": 9, "xStart": 3, "xEnd": 12 },
      { "y": 10, "xStart": 4, "xEnd": 11 },
      { "y": 11, "xStart": 5, "xEnd": 10 }
    ]
  },
  "eyes": [
    { "name": "left", "color": 1, "pixels": [{ "x": 6, "y": 8 }] },
    { "name": "right", "color": 1, "pixels": [{ "x": 9, "y": 8 }] }
  ],
  "highlights": []
}
```

### Validation

- [x] Valid JSON only
- [x] Canvas is exactly 16x16
- [x] Body rows use valid coordinates
- [x] Exactly two named eyes
- [x] Eye coordinates are in bounds
- [x] Eyes are spatially separated
- [x] Plan can be rendered deterministically
- [x] Rendered sprite is recognizably slime-like

### Deterministic render

Using palette 0=transparent, 1=black, 2=red, 3=white and painting body spans first, then eyes:

```text
0000000000000000
0000000000000000
0000000000000000
0000000000000000
0000022222200000
0000222222220000
0002222222222000
0022222222222200
0022221221222200
0002222222200000
0000222222220000
0000022222200000
0000000000000000
0000000000000000
0000000000000000
0000000000000000
```

### Assessment

PASS.

The structured representation solves the specific failure seen in Experiments 001-E/F. The model produced two distinct eye features at symmetric coordinates (6,8) and (9,8), while the body is represented as valid horizontal spans entirely inside the canvas.

The body is symmetric around x=7.5. Eye positions are also symmetric around x=7.5 and are separated by two red pixels (x=7 and x=8) on y=8.

The deterministic renderer can therefore guarantee the 16x16 output dimensions and preserve the semantic distinction between left and right eyes without relying on the model to serialize all 256 pixels correctly.

### Conclusion

The structured-plan approach is substantially more promising than direct full-bitmap generation for controllable pixel-art editing. The next experiment should implement a small schema validator/renderer and then test a local edit such as "close the left eye" by modifying structured feature data rather than regenerating the entire bitmap.

