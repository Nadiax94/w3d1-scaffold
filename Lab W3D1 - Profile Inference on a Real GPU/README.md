# Lab W3D1 - Profile Inference on a Real GPU

## Overview
This lab profiles inference performance of Qwen2.5-1.5B-Instruct on a real GPU.

The experiment compares:
- FP16 inference
- INT8 quantized inference

with different context lengths.

## Experiments Performed

- Loaded Qwen2.5-1.5B-Instruct using Transformers.
- Measured:
  - VRAM usage
  - GPU utilization
  - Generation speed (tokens/s)

- Tested context lengths:
  - 512 tokens
  - 2048 tokens
  - 4096 tokens

- Compared batch performance:
  - Batch size 1
  - Batch size 8

## Results

Observations:
- VRAM usage increases with larger context lengths.
- FP16 uses more GPU memory than INT8.
- GPU utilization does not always represent actual throughput.
- Larger batches improve inference throughput.

## Files

- `profile.json` : GPU profiling results for different dtypes and contexts.
- `batch_check.json` : Batch 1 vs Batch 8 throughput comparison.

## Model

Qwen2.5-1.5B-Instruct
