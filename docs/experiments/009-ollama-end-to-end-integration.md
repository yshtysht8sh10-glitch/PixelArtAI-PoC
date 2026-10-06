# Experiment 009: Ollama End-to-End Integration

## Goal

Connect the Pixel String Viewer directly to a local Ollama vision model and test an end-to-end pixel-art editing flow:

1. load a real pixel-art BMP,
2. render it in the browser,
3. send the rendered PNG plus exact pixel matrix and palette to Ollama,
4. observe streamed model output,
5. validate the model response,
6. apply the edit back to the viewer.

The main question is not only whether the model can edit pixel art, but whether a local VLM can be integrated into an application with practical latency and a safe output contract.

## Environment

- Viewer: `tools/pixel-string-viewer/index.html`
- Local inference server: Ollama
- Model: `qwen3-vl:8b`
- Test sprite: 85 × 83 pixels (7,055 pixels)
- Input: PNG preview + full pixel matrix + palette + natural-language instruction
- Instruction used: wink the character's left eye
- Browser served through a local HTTP server for Ollama API access

## Integration work

The Viewer was extended step by step.

### Ollama API terminal

The browser calls the local Ollama API at `http://localhost:11434`. The model name, Ollama URL, and context size are configurable in the UI.

### Vision input

The current canvas is converted to PNG and sent to the vision model together with the exact matrix and palette. Tiny pixel art is enlarged with nearest-neighbor scaling before being sent so the visual structure remains crisp.

For the 85 × 83 test sprite, the attached preview was enlarged to 510 × 498.

### Validation

The first implementation required the model to return a complete matrix. The response was accepted only when its format, dimensions, and palette IDs were valid.

### Streaming / Thinking display

The Ollama request was changed to streaming mode. The terminal displays model Thinking and Answer streams separately, together with elapsed time and character counts. This made it possible to distinguish a stalled request from a model that was actively processing the large input.

## Problems encountered

### 1. Browser connection

Opening the HTML directly with `file://` produced `Failed to fetch`. Serving the Viewer through a local HTTP server allowed the browser to reach Ollama.

### 2. Context size

The initial Ollama context was 4,096 tokens. The request required about 16K tokens and failed with:

`request (16823 tokens) exceeds the available context size (4096 tokens)`

The Viewer was updated to send `num_ctx = 32768`.

### 3. Model lifecycle / long request

During investigation, `ollama ps` showed `qwen3-vl:8b` loaded at 32,768 context with approximately 9% CPU / 91% GPU placement. The model temporarily remained in `Stopping...` while the long request was being torn down.

A direct CLI test confirmed that the model itself could run and answer a simple Japanese greeting.

### 4. Thinking behavior

With Thinking enabled, the terminal showed that the model was actively inspecting the image, palette, and matrix rather than being frozen. This was useful for debugging the integration.

## Full-matrix experiment result

The first end-to-end design asked the model to return all 7,055 pixels as a complete edited matrix.

Measured result:

| Metric | Result |
| --- | ---: |
| Sprite size | 85 × 83 |
| Pixels | 7,055 |
| Context configured | 32,768 tokens |
| Prompt tokens | 16,819 |
| Output tokens | 15,949 |
| Elapsed time | 821 sec (~13 min 41 sec) |
| Final Answer | none |
| Viewer edit applied | no |

The model spent its output budget reasoning about and reconstructing matrix rows. It reached the end of generation without producing a final Answer payload, so the Viewer correctly rejected the response with `Ollamaから本文が返りませんでした`.

## Finding

The full-matrix contract is not practical for this local-model experiment.

The failure is useful because it identifies a system-level bottleneck rather than only a model-quality issue:

- the full matrix makes the prompt large,
- reconstructing the full matrix encourages long reasoning,
- the output can be thousands of tokens even when only a few pixels need to change,
- latency and failure probability increase,
- unchanged pixels are unnecessarily exposed to generation errors.

## Next design: Generic Pixel Patch

The output contract is changed to a generic patch:

```json
{
  "changes": [
    {"x": 39, "y": 13, "color": "10"},
    {"x": 40, "y": 13, "color": "10"}
  ]
}
```

The application does not contain semantic concepts such as eyes, winks, characters, or predefined edit templates. The model decides which pixels to change. The application only validates generic constraints:

- `x` and `y` are integers and inside the bitmap,
- `color` exists in the current palette,
- the same coordinate is not duplicated,
- the patch is bounded to a reasonable maximum number of changes.

After validation, the Viewer applies the patch to the existing matrix and redraws the preview.

This separates responsibilities:

```text
PNG + matrix + palette + instruction
                ↓
             VLM
                ↓
        Generic Pixel Patch
                ↓
            Validator
                ↓
       Existing matrix update
                ↓
             Preview
```

## Next experiment

Run the same 85 × 83 sprite and the same wink instruction with Pixel Patch output and compare:

- prompt tokens,
- output tokens,
- elapsed time,
- number of changed pixels,
- validator result,
- visual quality.

The full-matrix run above is the baseline.


## Pixel Patch run result

The same 85 × 83 sprite was tested with the Generic Pixel Patch output contract.

| Metric | Full matrix | Pixel Patch |
| --- | ---: | ---: |
| Prompt tokens | 16,819 | 16,833 |
| Output tokens | 15,949 | 10,120 |
| Elapsed time | 821 sec | 458 sec |
| Final structured answer | none | yes |
| Patch applied | no | yes, 1 pixel |
| Visual result | n/a | no visible wink |

The model returned:

```json
{"changes":[{"x":50,"y":10,"color":"7"}]}
```

The Viewer validated and applied exactly one pixel. This was the first successful end-to-end path from natural-language instruction through Ollama to a validated automatic matrix update.

However, human visual review found no meaningful visible wink. The system integration succeeded, while the semantic/visual edit failed.

### Interpretation

Pixel Patch substantially reduced total generation time and allowed a final structured answer to complete, but the prompt remained approximately 16.8K tokens and Thinking still consumed about 10K output tokens. This shows that changing only the output contract does not remove the dominant cost of having the model inspect the full 7,055-pixel matrix.

## Experiment 010 direction: ROI + Review/Retry

The Viewer was therefore extended with two additional mechanisms.

### ROI-first editing

1. Send the rendered PNG and user instruction to the vision model.
2. Ask for a small rectangular region of interest (ROI) in original pixel coordinates.
3. Add a small generic margin.
4. Send only that ROI's matrix plus the palette to the edit call.
5. Request a Generic Pixel Patch with absolute coordinates.

This keeps semantic localization in the model while reducing the matrix text supplied to the expensive edit step.

### Vision Review / Retry

After each validated patch is applied:

1. render the edited bitmap,
2. send the resulting PNG to a separate Vision Review call,
3. ask whether the visible result satisfies the original instruction,
4. if FAIL, feed the review reason/suggestion into the next edit attempt,
5. stop on PASS or after a bounded maximum number of attempts.

The default maximum is three attempts and is controlled by application code, not by the model.

Conceptually:

```text
Instruction + PNG
       ↓
   ROI locator
       ↓
local matrix + palette + PNG
       ↓
   Pixel Patch
       ↓
    Validator
       ↓
      Apply
       ↓
 edited PNG
       ↓
 Vision Review
   ↓ PASS     ↓ FAIL
 complete   feedback
              ↓
          next edit
```

This preserves the core design constraint: the application has no hard-coded concepts such as eye, wink, character, or expression. The model chooses the semantic target and visual edit; application code provides generic validation, bounded retry control, and deterministic patch application.

### Metrics to collect next

For the same wink task, record:

- ROI localization time/tokens,
- each Edit call time/tokens,
- each Review call time/tokens,
- ROI dimensions,
- number of patch pixels per attempt,
- number of attempts,
- final PASS/FAIL,
- human visual judgment.

This will test whether reducing input context and adding closed-loop visual verification improves both latency and quality.


## First ROI + Review/Retry run

A real run with the 85 × 83 sprite used the instruction to wink the character's right eye.

The ROI locator returned:

```json
{"roi":{"x":40,"y":20,"width":3,"height":3},"reason":"short"}
```

The application added its generic margin and produced an 11 × 11 edit region:

```text
x=36, y=16, width=11, height=11
```

ROI localization metrics:

| Metric | Result |
| --- | ---: |
| Prompt tokens | 1,147 |
| Output tokens | 5,148 |
| Elapsed time | 117,459 ms (~117 sec) |
| Raw ROI | 3 × 3 |
| Expanded edit ROI | 11 × 11 |

This confirms that ROI-first editing drastically reduces the matrix region passed to the edit stage. However, the edit reasoning still became very long. Within the small 11 × 11 region, the model repeatedly reconsidered which coordinate represented the requested eye, including candidates around `(37,19)` and later `(45,17)`.

### New finding: visual-to-coordinate mapping

The bottleneck is no longer simply the size of the full 7,055-pixel matrix. The experiment exposed a more specific weakness:

```text
visual understanding
       ↓
ROI localization
       ↓
semantic feature in ROI
       ↓
exact matrix coordinate  ← unstable
       ↓
Pixel Patch
```

The model can identify a plausible visual region, but mapping the visible semantic feature to an exact matrix coordinate is unstable. Long Thinking does not necessarily improve this mapping and can cause the candidate coordinate to drift during reasoning.

## Experiment 010: Coordinate-aware ROI + Thinking OFF

The next Viewer revision changes the edit stage in two ways.

1. The 11 × 11 ROI is rendered as a dedicated magnified image with absolute x-coordinate labels across the top and absolute y-coordinate labels down the left.
2. Edit Thinking defaults to OFF.

The edit model now receives:

- the natural-language instruction,
- the coordinate-labeled ROI image,
- the exact local matrix,
- the palette,
- Review feedback when retrying.

The application remains semantics-free. It does not know what an eye, wink, face, or character is; it only supplies a deterministic mapping between image pixels and matrix coordinates.

The hypothesis is that explicit coordinate grounding will reduce visual-to-matrix ambiguity, while disabling long edit Thinking will reduce latency and prevent unnecessary coordinate drift.

Metrics to compare with the previous run:

- Edit elapsed time,
- Edit output tokens,
- selected patch coordinates,
- Review PASS/FAIL,
- number of retry attempts,
- human visual judgment.
