# Week 3 Labs

This repository contains my Week 3 lab work and assignments focused on LLM inference, GPU performance, vLLM serving, quantisation, benchmarking, and capacity analysis.

## Overview

During Week 3, the labs move from basic GPU inference profiling to running and evaluating a production-style LLM serving stack.

The work includes:

- Profiling inference performance on a real GPU
- Understanding prefill, decode, latency, and throughput
- Serving a model using vLLM
- Measuring batching and concurrency behavior
- Comparing fp16 and AWQ quantised models
- Locking a validated model configuration
- Benchmarking the locked model under increasing load
- Finding the practical serving capacity using a p95 latency SLO
- Converting serving capacity into cost-per-million-token estimates
- Planning replica scale-out while preserving the latency SLO

Each lab is organized in its own folder with its related notebooks, code, reports, and result files.

---

## Labs

### Lab W3D1 - Profile Inference on a Real GPU

This lab focuses on profiling LLM inference performance on a real GPU environment using a Google Colab T4.

The lab explores GPU memory usage and model inference behavior while examining how different serving conditions affect performance.

The main goal is to understand how model execution behaves on actual GPU hardware rather than relying only on theoretical estimates.

---

### Lab W3D2 - Inference Anatomy

This lab studies the main stages of LLM inference and how they affect performance.

It focuses on concepts such as prefill, decode, time to first token, token generation speed, KV cache behavior, and GPU utilization.

The lab helps connect latency and throughput measurements with what is happening internally during model generation.

---

### Lab W3D3 - The Engine Swap

This lab replaces the previous inference engine with **vLLM** while keeping the same OpenAI-compatible `/v1` API.

The model is served through vLLM on a Google Colab T4 GPU and tested under different concurrency levels.

The lab compares static batching with continuous batching and measures how throughput scales as more requests are processed together.

Concurrency levels such as `1`, `4`, and `8` are used to observe the effect of batching on serving performance.

The benchmark results are recorded in `ab_report.json`.

The lab completes successfully with:

`GREEN CHECK: PASS`

---

### Lab W3D4 - Quantise and Lock the Model

This lab compares fp16 serving with an AWQ quantised version of the model using vLLM.

The objective is to reduce the model's memory footprint while checking that the quantised model still preserves acceptable behavior.

The lab also validates function calling and structured outputs before selecting the configuration to use for later serving benchmarks.

The AWQ model successfully achieved a `10/10` smoke score and passed the validation checks.

The validated model configuration was then locked for use in the next benchmark lab.

Locked model:

`Qwen/Qwen2.5-1.5B-Instruct-AWQ`

The lab completes successfully with:

`GREEN CHECK: PASS`

---

### Lab W3D5 - Benchmark Harness and Capacity

This lab benchmarks the locked AWQ vLLM model under increasing levels of concurrent load using the reference benchmark harness.

The benchmark runs at concurrency levels:

`1, 2, 4, 8, 16`

For each level, the harness measures:

- Tokens per second
- p95 time to first token
- p95 end-to-end latency
- Request errors

The selected p95 latency SLO was:

`2.0 seconds`

The benchmark results were:

| Concurrency | Tokens/s | TTFT p95 | p95 Latency | Errors |
|---:|---:|---:|---:|---:|
| 1 | 91.6 | 0.114 s | 1.435 s | 0 |
| 2 | 157.2 | 0.207 s | 1.885 s | 0 |
| 4 | 282.0 | 0.120 s | 1.751 s | 0 |
| 8 | 484.8 | 0.161 s | 1.886 s | 0 |
| 16 | 721.1 | 0.255 s | 2.289 s | 0 |

At concurrency `8`, throughput reached approximately `484.75 tokens/s` while p95 latency remained below the `2.0 s` target.

At concurrency `16`, throughput increased to `721.11 tokens/s`, but p95 latency rose to `2.2885 s`, exceeding the SLO.

The reported knee was therefore:

`8`

The final capacity result was:

- Knee concurrency: `8`
- Throughput at knee: `484.75 tokens/s`
- p95 latency at knee: `1.8863 s`
- TTFT p95 at knee: `0.1611 s`
- Target p95 SLO: `2.0 s`
- Request errors: `0`

The lab includes the benchmark report, capacity note, and knee result files.

The final verification completed successfully with:

`GREEN CHECK: PASS`

---

### Extra Lab W3D5 - Cost per Million Tokens and Scale-Out

This extra lab converts the measured serving capacity into a cost figure and a scale-out decision.

Using a representative T4-class GPU price of:

`$0.35/hour`

The measured cost per million output tokens was:

| Concurrency | Tokens/s | p95 Latency | Cost / 1M Tokens |
|---:|---:|---:|---:|
| 1 | 91.6 | 1.435 s | $1.0618 |
| 2 | 157.2 | 1.885 s | $0.6185 |
| 4 | 282.0 | 1.751 s | $0.3448 |
| 8 | 484.8 | 1.886 s | $0.2006 |
| 16 | 721.1 | 2.289 s | $0.1348 |

Although concurrency `16` has the lowest raw cost per million tokens, it exceeds the `2.0 s` p95 SLO.

Therefore, the usable cost at the safe knee is:

`$0.2006 per 1M output tokens`

The scale-out plan keeps every replica operating at the safe knee concurrency.

| Required Throughput | Replicas Needed | Hourly Cost | Effective p95 |
|---:|---:|---:|---:|
| 484.75 tokens/s (1.0x) | 1 | $0.35 | 1.8863 s |
| 727.125 tokens/s (1.5x) | 2 | $0.70 | 1.8863 s |
| 969.50 tokens/s (2.0x) | 2 | $0.70 | 1.8863 s |
| 1454.25 tokens/s (3.0x) | 3 | $1.05 | 1.8863 s |

The extra lab shows that throughput beyond the knee should not be treated as usable capacity when it violates the latency SLO. Scaling out with additional replicas keeps each replica at the safe operating point.

The results are recorded in `cost_report.json`.

The final verification completed successfully with:

`GREEN CHECK: PASS`

---

## Repository Structure

```text
week3-labs/
├── w3d1/
│   └── Week 3 Day 1 lab files
│
├── w3d2/
│   └── Week 3 Day 2 lab files
│
├── w3d3/
│   ├── notebooks / code
│   └── ab_report.json
│
├── w3d4/
│   └── quantisation and model-lock results
│
├── w3d5/
│   ├── Lab_W3D5_the_benchmark_harness_and_Extra_Lab.ipynb
│   ├── bench_report.json
│   ├── capacity-note.md
│   ├── knee.json
│   └── cost_report.json
│
└── README.md
```

---

## Week 3 Outcome

By the end of Week 3, the serving stack progressed from individual GPU inference measurements to a benchmarked vLLM deployment with a validated and locked model configuration.

The final setup demonstrates:

- A working vLLM inference server
- OpenAI-compatible API serving
- Improved throughput through continuous batching
- A validated and locked AWQ model configuration
- Quantised model evaluation
- Concurrency benchmarking
- p95 latency measurement
- Capacity estimation based on an SLO
- Cost-per-million-token analysis
- Replica scale-out planning
- Successful automated verification

The final Week 3 benchmark and extra cost analysis completed with zero request errors and `GREEN CHECK: PASS`.
