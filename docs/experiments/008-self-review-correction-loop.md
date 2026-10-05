# Experiment 008: Self-Review / Correction Loop

## Purpose

Test whether a model can detect and repair a low-level pixel-patch mistake after seeing the result of its own edit.

This follows Experiment 007, where GPT-5.6 Sol intended to create a three-pixel horizontal closed eye but produced a black-red-black pattern instead.

## Architecture under test

```text
Natural-language edit
        ↓
AI proposes Pixel Patch
        ↓
Apply patch deterministically
        ↓
Show resulting sprite back to AI
        ↓
AI critiques only the requested visual change
        ↓
AI returns corrective Pixel Patch
```

The editor still contains no semantic wink template. The validator/application layer only handles generic pixels.

## Input

User request:

```text
Make the slime clearly look like it is winking by closing its left eye.
```

Result after Experiment 007's first patch:

```text
0000000000000000
0000000000000000
0000000000000000
0000000000000000
0000022222200000
0000222222220000
0002222222222000
0022222222222200
0022212121222200
0002222222200000
0000222222220000
0000022222200000
0000000000000000
0000000000000000
0000000000000000
0000000000000000
```

Palette: 0 transparent, 1 black, 2 red, 3 white.

The right eye is at (9,8) and must remain unchanged.

## Review task

Evaluate whether the resulting sprite clearly expresses the requested wink. If the local edit is incorrect, return only the minimum corrective Generic Pixel Patch. Do not use a predefined semantic eye/wink template.

## GPT-5.6 Sol review

The edited left-eye area at x=5..7, y=8 is:

```text
1 2 1
```

This contradicts the intended continuous horizontal closed-eye mark. The minimum correction is to change the center pixel (6,8) from red to black.

```json
{
  "changes": [
    { "x": 6, "y": 8, "color": 1 }
  ]
}
```

## Corrected result

Row y=8 becomes:

```text
0022211121222200
```

Local expression:

```text
■■■  ■
wink open-eye
```

## Assessment

- [x] Detected the first-pass patch inconsistency
- [x] Preserved the right eye
- [x] Preserved unrelated pixels
- [x] Returned a minimal corrective patch
- [x] Correction matches the intended continuous three-pixel closed-eye representation
- [x] No semantic renderer/template required

Result: **PASS for correction of the known low-level patch error**.

## Important limitation

This experiment demonstrates correction of an internally inconsistent first-pass patch, not yet independent proof that the final three-pixel mark is visually optimal or universally recognizable as a wink. A stronger future test should render the corrected bitmap and ask a fresh visual evaluation stage that is not given the intended pixel shape.

## Conclusion

A generate → apply → review → correct loop can recover from at least this class of coordinate/color mistake while keeping the editor generic. This suggests that deterministic patch application plus an AI review pass may be more robust than trusting a single generated patch.
