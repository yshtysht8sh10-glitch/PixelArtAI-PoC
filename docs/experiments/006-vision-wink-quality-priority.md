# Experiment 006: Vision Pixel Patch — Visual Quality Priority

## Purpose

Determine whether Experiment 005 failed because of the strong minimal-change wording or because the vision model cannot independently invent a recognizable pixel-art representation of a wink.

## Model

- Runtime: Ollama 0.35.0
- Model: `qwen3-vl:8b`
- Image: same 512x512 nearest-neighbor enlargement used in Experiment 005
- Original sprite: 16x16

## Changed variable

Experiment 005 said to change only pixels necessary for the wink. Experiment 006 explicitly prioritizes human-recognizable visual quality and states that simply erasing the eye is insufficient.

The model is allowed to add/change nearby pixels, but is **not told what shape a closed eye should have**.

This preserves the core research question: can the vision model invent the required local pixel-art form itself?

## Prompt

See [../../prompts/006-vision-wink-quality-priority.txt](../../prompts/006-vision-wink-quality-priority.txt).

## Success criteria

- [ ] Valid JSON patch
- [ ] All coordinates/palette values valid
- [ ] Right eye preserved
- [ ] Unrelated sprite area preserved
- [ ] More than simple eye erasure when necessary
- [ ] Result is recognizable to a human as a wink
- [ ] Model independently chooses the closed-eye pixel shape

## Result

Pending.
