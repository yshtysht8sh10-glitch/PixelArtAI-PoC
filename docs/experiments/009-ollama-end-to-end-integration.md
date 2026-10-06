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
