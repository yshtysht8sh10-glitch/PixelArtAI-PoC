# Experiment 001-F: Structural Eye Constraint

## Purpose

Test whether pixel-array constraints can produce two distinct eyes more reliably than the semantic instruction "exactly two visible eyes."

Experiment 001-E introduced black facial pixels, but the model collapsed them into one centered 4x4 black block.

## Controlled variables

- Runtime: Ollama
- Model: `qwen3-coder:30b`
- Canvas: 16x16
- Palette: unchanged
- Output encoding: unchanged

## Changed variable

The eye requirement is rewritten as a spatial/topological constraint:

- two separate black pixel groups,
- the groups must not touch,
- at least one red pixel must separate them,
- symmetrical placement,
- explicitly prohibit one large central black rectangle.

## Prompt

See [../../prompts/001f-structural-eyes.txt](../../prompts/001f-structural-eyes.txt).

## Success criteria

- [ ] Exactly 16 rows
- [ ] Exactly 16 characters per row
- [ ] Only palette indices 0/1/2/3
- [ ] Coherent centered slime body
- [ ] Two distinct black eye components
- [ ] At least one red pixel separates the eyes
- [ ] Eyes do not touch
- [ ] No large central black rectangle
- [ ] No extra prose

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

### Validation

- [x] Exactly 16 rows
- [x] Exactly 16 characters per row
- [x] Only palette indices 0/1/2/3
- [x] Coherent centered slime body
- [ ] Two distinct black eye components
- [ ] At least one red pixel separates the eyes
- [ ] Eyes do not touch
- [ ] No large central black rectangle
- [x] No extra prose

### Assessment

FAIL for the structural-eye hypothesis.

The output is byte-for-byte identical to Experiment 001-E. The additional topological constraints did not change the generated matrix at all. The model again produced one centered 4x4 black block instead of two separated eye components.

This is stronger evidence than merely receiving another poor drawing: increasingly explicit natural-language constraints produced no observable change in the output.

### Conclusion

For this setup, further elaborating the natural-language prompt is unlikely to be the highest-value next step. The PoC should move toward a more structured generation/editing mechanism rather than repeatedly adding prose constraints.

Candidate next directions include generating a structured intermediate representation (body/eyes/features with coordinates), validating it programmatically, or asking the model to edit explicit coordinates rather than regenerate the full bitmap.
