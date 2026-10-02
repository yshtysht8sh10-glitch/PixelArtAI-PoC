# Experiment 001: Text -> Pixel

## Purpose

Confirm whether a local LLM can generate a valid 16x16 pixel matrix from a natural-language description while obeying strict format and palette constraints.

## Environment

- Runtime: Ollama
- Model: TBD per run
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

- [ ] 16 rows
- [ ] Every row is 16 characters
- [ ] Only 0/1/2/3 are used
- [ ] No extra prose
- [ ] Sprite is visually recognizable as a front-facing red slime

## Runs

### Run 001-A

- Date:
- Model:
- Ollama version:
- Duration:

#### Raw output

```text
TBD
```

#### Mechanical validation

- Width:
- Height:
- Palette validity:
- Extra text:
- Parseable:

#### Visual assessment

TBD

#### Observations

TBD

#### Next

TBD
