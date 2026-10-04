# Experiment 003: Local Edit — Wink

## Purpose

Test whether `qwen3-coder:30b` can apply a natural-language local edit to the structured sprite representation while preserving unrelated data exactly.

User edit request: close the slime's left eye to make it wink.

## Input state

The input is the successful structured sprite from Experiment 002.

## Edit constraints

- Preserve canvas exactly.
- Preserve every body row exactly.
- Preserve the right eye exactly.
- Modify only the left eye.
- Return the complete updated JSON.

## Model output

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
    { "name": "left", "color": 1, "pixels": [] },
    { "name": "right", "color": 1, "pixels": [{ "x": 9, "y": 8 }] }
  ],
  "highlights": []
}
```

## Diff

Only one semantic field changed:

```diff
- { "name": "left", "color": 1, "pixels": [{ "x": 6, "y": 8 }] }
+ { "name": "left", "color": 1, "pixels": [] }
```

Canvas, body, right eye, and highlights are unchanged.

## Validation

- [x] Valid JSON
- [x] Canvas preserved exactly
- [x] Body preserved exactly
- [x] Right eye preserved exactly
- [x] Only left-eye data changed
- [x] Coordinates remain valid
- [x] Deterministically renderable
- [ ] Closed-eye visual is explicitly represented

## Assessment

PARTIAL PASS.

The local-edit architecture worked very well: the model changed only the requested feature and preserved all unrelated structured data exactly. This is strong evidence that natural-language editing of structured sprite state is viable.

However, the model interpreted "close the left eye" as removing all left-eye pixels. The rendered result therefore looks like one eye disappeared rather than a visible closed/winking eye.

This exposes a schema limitation rather than the previous full-bitmap control problem. The current schema can describe eye pixels, but it has no explicit semantic eye state or canonical rendering rule for a closed eye.

## Conclusion

Experiment 003 validates precise local structured editing, but also shows that the sprite schema needs richer feature semantics. A next iteration should represent eye state explicitly, for example `state: "open" | "closed"`, and let the deterministic renderer decide how each state is drawn.
