# Experiment 001: Text -> Pixel

## Purpose

Confirm whether a local LLM can generate a valid 16x16 pixel matrix from a natural-language description while obeying strict format and palette constraints.

## Environment

- Runtime: Ollama
- Model: `llama3:latest`
- Canvas: 16x16
- Palette size: 4

## Palette

| Index | Meaning |
| --- | --- |
| 0 | Transparent |
| 1 | Black |
| 2 | Red |
| 3 | White |

## Prompt

See [../../prompts/001-text-to-pixel.txt](../../prompts/001-text-to-pixel.txt).

## Expected output constraints

- Exactly 16 lines
- Exactly 16 characters per line
- Allowed characters: `0`, `1`, `2`, `3`
- No Markdown fence
- No explanation
- No blank lines

## Validation checklist

- [x] 16 rows
- [x] Every row is 16 characters
- [x] Only 0/1/2/3 are used
- [x] No extra prose
- [ ] Sprite is visually recognizable as a front-facing red slime

## Runs

### Run 001-A

- Date: 2026-10-04
- Model: `llama3:latest`
- Ollama version: Not recorded
- Duration: Not recorded

#### Raw output

```text
0000000000000000
0011111111111111
0022222222222222
0033333333333333
0033333333333333
0033333333333333
0022222222222222
0011111111111111
0000000000000000
0000000000000000
0000000000000000
0000020000000000
0000000000000000
0000010000000000
0000000000000000
0000000000000000
```

#### Mechanical validation

- Width: PASS — 16 characters in every row
- Height: PASS — exactly 16 rows
- Palette validity: PASS — only 0/1/2/3 used
- Extra text: PASS — no prose or Markdown wrapper
- Parseable: PASS

#### Visual assessment

FAIL. The output satisfies the structural format but is not recognizable as a centered, front-facing red slime. It mainly forms horizontal color bands, uses white as a large filled region rather than only an eye highlight, and includes isolated pixels below the main shape.

#### Observations

This is a useful first result: `llama3:latest` understood the strict matrix serialization rules much better than the semantic drawing task. Mechanical compliance is 4/4, while visual/semantic compliance fails.

The result suggests that the experiment should distinguish **format compliance** from **pixel-art quality** instead of treating generation as a single pass/fail criterion.

#### Next

Keep this failed result unchanged as baseline data. Run the exact same prompt against `qwen2.5-coder:7b` and `qwen3-coder:30b` before changing the prompt. This will show whether model choice alone improves spatial/pixel reasoning. After the same-prompt comparison, iterate on the prompt if necessary.


### Run 001-B

- Date: 2026-10-04
- Model: `qwen2.5-coder:7b`
- Ollama version: Not recorded
- Duration: Not recorded

#### Raw output

```text
0000000000000000
0011001111110111
0022222222222222
0033333333333333
0033333333333333
0033333333333333
0022222222222222
0011001111110111
0000000000000000
0000000000000000
0000000000000000
0000020000000000
0000000000000000
0000010000000000
0000000000000000
0000000000000000
```

#### Mechanical validation

- Width: PASS — 16 characters in every row
- Height: PASS — exactly 16 rows
- Palette validity: PASS — only 0/1/2/3 used
- Extra text: PASS — no prose in the reported model output
- Parseable: PASS

#### Visual assessment

FAIL. The sprite is still not recognizable as a centered, front-facing red slime. As with Run 001-A, the output is dominated by horizontal color bands, white occupies a large filled area, and isolated pixels appear below the main shape.

#### Observations

The output is strikingly similar to the `llama3:latest` baseline. Rows 3-7 and 9-16 are identical to Run 001-A; the main visible change is in rows 2 and 8. This suggests that changing from Llama 3 to Qwen2.5-Coder 7B did not materially improve the spatial drawing behavior under the current prompt.

Mechanical constraint compliance remains strong, while semantic/spatial compliance remains poor.

#### Next

Run the exact same prompt against `qwen3-coder:30b` before changing the prompt. If the 30B model produces the same band-like pattern, the next experiment should focus on prompt representation or intermediate spatial guidance rather than model size alone.
