# Model lock (team record)

## The locked model

- Model id: `Qwen/Qwen2.5-1.5B-Instruct-AWQ`
- Quantisation: `awq`
- Why this one: Passed the smoke test and the quality spot check showed no obvious degradation.

## The launch flags

--model Qwen/Qwen2.5-1.5B-Instruct-AWQ --dtype half --max-model-len 4096
--gpu-memory-utilization 0.85 --quantization awq
--enable-auto-tool-choice --tool-call-parser hermes

- Tool-call parser: `hermes`

## The smoke score

- Score (valid behaviours out of 10): `10`
- Distractor stayed call-free in the majority: `yes`
- Passed the gate (>= 8/10 and distractor majority clean): `yes`
- Measured against: `both - AWQ 10/10, fp16 10/10`

## Quality spot check note

- The AWQ build held up across the five prompts with no obvious degradation.