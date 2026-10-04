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


### Run 001-B — INVALID

> Invalid run: the command `ollama run qwen2.5-coder:7b` was entered inside the already-running Llama 3 interactive session, so the model was not actually switched. This run is retained as an experiment-operation record and must not be used for model comparison.

- Date: 2026-10-04
- Intended model: `qwen2.5-coder:7b`
- Actual model: `llama3:latest`
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


### Run 001-C

- Date: 2026-10-04
- Model: `qwen2.5-coder:7b`
- Ollama version: Not recorded
- Duration: Not recorded

#### Raw output

```text
0000000000000000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000002222220000
0000000000000000
```

#### Mechanical validation

- Width: PASS — 16 characters in every returned row
- Height: FAIL — 15 rows returned instead of 16
- Palette validity: PASS — only 0 and 2 are used
- Extra text: PASS — no prose in the reported model output
- Parseable: FAIL under the strict 16x16 schema because one row is missing

#### Visual assessment

FAIL. The output is a centered red rectangle/vertical block, not a recognizable front-facing slime. It contains no black outline, facial details, or white eye highlights.

#### Observations

Unlike the invalid Run 001-B, this is a genuine `qwen2.5-coder:7b` result. The model preserved row width and palette legality, but failed the exact-height constraint and the semantic drawing task.

This is a different failure mode from the Llama 3 baseline: Qwen2.5-Coder 7B produced a much simpler centered geometric block rather than horizontal palette bands. That difference is useful evidence that the model is affecting the spatial output, even though neither model currently produces usable pixel art.

#### Next

Run the exact same prompt against `qwen3-coder:30b` with a fresh Ollama session. Do not modify the prompt yet. After that run, compare structural compliance and visual/spatial behavior across the valid Llama 3, Qwen2.5-Coder 7B, and Qwen3-Coder 30B results.


### Run 001-D

- Date: 2026-10-04
- Model: `qwen3-coder:30b`
- Ollama version: Not recorded
- Duration: Not recorded

#### Raw output

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
0022222222222222
0222222222222222
0222222222222222
0222222222222222
0022222222222220
0002222222222200
0000000000000000
```

#### Mechanical validation

- Width: PASS — 16 characters in every row
- Height: PASS — exactly 16 rows
- Palette validity: PASS — only allowed palette indices are used
- Extra text: PASS — no prose in the reported model output
- Parseable: PASS

#### Visual assessment

PARTIAL. The output is not yet a complete character sprite because it has no outline, eyes, or facial details. However, unlike the previous valid runs, the red pixels form a coherent centered silhouette with a narrow top and broad rounded/blob-like body. It is recognizably attempting a slime-like shape.

#### Observations

This is the strongest spatial result so far. `qwen3-coder:30b` satisfies all mechanical serialization constraints and produces a coherent 2D silhouette rather than horizontal bands or a simple rectangle.

The model still ignores important semantic palette instructions: black is not used for the outline/facial details and white is not used for eye highlights. Therefore the experiment does not yet pass the full requested sprite specification.

The improvement from Qwen2.5-Coder 7B to Qwen3-Coder 30B is substantial enough that model capability appears to matter under the unchanged prompt. At the same time, the remaining failures show that model size alone does not solve all instruction-following and pixel-art composition requirements.

#### Next

Treat Run 001-D as the best baseline from the unchanged-prompt comparison. Before moving to local editing, perform a second prompt iteration with `qwen3-coder:30b` that makes the required outline, eyes, and facial structure more explicit while keeping the 16x16/palette/output constraints unchanged. Preserve Run 001-D as the pre-prompt-tuning baseline.
