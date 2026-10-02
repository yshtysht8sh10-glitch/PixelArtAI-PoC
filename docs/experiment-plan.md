# Experiment Plan

## Research question

Can a local LLM generate and edit usable pixel art while strictly preserving structural constraints?

## Evaluation dimensions

Each experiment should record both mechanical correctness and visual usefulness.

### Mechanical checks

- Exact width
- Exact height
- Allowed palette indices only
- No extra prose
- Parseable output
- No accidental row-length changes

### Visual checks

- Requested object is recognizable
- Symmetry or asymmetry is intentional
- Character identity is preserved across edits
- Unrequested areas remain stable
- Pose changes are coherent

## Model comparison policy

Use the same prompt and the same source pixel data wherever possible.

Initial comparison candidates:

| Model | Intended test |
| --- | --- |
| `llama3:latest` | Baseline local text model |
| `qwen2.5-coder:7b` | Small code-oriented baseline |
| `qwen3-coder:30b` | Structured matrix editing |
| `llama3.2-vision:11b` | Reference-image understanding |
| `llama3.2-vision:90b` | Large vision-model comparison |

## Experiment sequence

### 001 - Text to Pixel
Generate a 16x16 red slime using only four palette indices.

### 002 - Local Edit
Take the successful output from 001 and close only the right eye.

### 003 - Pose / Direction Change
Turn the character to the right while preserving palette and identity.

### 004 - Image to Pixel
Provide a reference image to a vision model and convert it into constrained pixel data.

## Recording rule

For every run, record:

- Date/time
- Ollama version if relevant
- Model name
- Prompt
- Raw output
- Validation result
- Human visual assessment
- Failure mode
- Next hypothesis

Do not overwrite failed runs. Failures are useful data.
