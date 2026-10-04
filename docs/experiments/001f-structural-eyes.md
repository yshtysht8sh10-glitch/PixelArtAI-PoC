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

Pending.
