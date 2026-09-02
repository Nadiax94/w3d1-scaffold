# Lab W3D4 — Quantise and Lock the Model

## Objective

Serve the AWQ model with vLLM, compare it with the fp16 version, run the function-calling smoke test, and lock the model that will be used for the rest of the course.

## Locked Model

- Model: `Qwen/Qwen2.5-1.5B-Instruct-AWQ`
- Quantisation: `AWQ`
- Tool-call parser: `hermes`
- Smoke test score: `10/10`
- Distractor compliance: `True`
- Green check: `PASS`

## Launch Flags

```text
--model Qwen/Qwen2.5-1.5B-Instruct-AWQ
--dtype half
--max-model-len 4096
--gpu-memory-utilization 0.85
--quantization awq
--enable-auto-tool-choice
--tool-call-parser hermes
