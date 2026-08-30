# w3d1-scaffold
Completed GPU profiling experiment using Qwen2.5-1.5B-Instruct.

## Tasks Completed
- Loaded Qwen2.5-1.5B-Instruct using Transformers.
- Tested inference performance with:
  - FP16
  - INT8 quantization
- Measured:
  - GPU VRAM usage
  - GPU utilization
  - Tokens per second
- Profiled different context lengths:
  - 512
  - 2048
  - 4096
- Compared batch size performance (batch 1 vs batch 8).
- Generated:
  - `profile.json`
  - `batch_check.json`

## Key Observations
- VRAM usage increases as context length increases.
- FP16 consumes more VRAM than INT8.
- GPU utilization does not always represent actual throughput.
- Increasing batch size improves inference throughput.
