# Week 3 Labs

This repository contains my Week 3 lab work and assignments focused on LLM inference, GPU performance, vLLM serving, quantisation, benchmarking, and capacity analysis.

## Overview

During Week 3, the labs move from basic GPU inference profiling to running and evaluating a production-style LLM serving stack.

The work includes:

* Profiling inference performance on a real GPU
* Understanding prefill, decode, latency, and throughput
* Serving a model using vLLM
* Measuring batching and concurrency behavior
* Comparing fp16 and AWQ quantised models
* Locking a validated model configuration
* Benchmarking the locked model under increasing load
* Finding the practical serving capacity using a p95 latency SLO

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

The lab completes successfully with:

`GREEN CHECK: PASS`

---

### Lab W3D5 - Benchmark Harness and Capacity

This lab benchmarks the locked vLLM model under increasing levels of concurrent load.

The benchmark runs at concurrency levels:

`1, 2, 4, 8, 16`

For each level, the harness measures:

* Tokens per second
* p95 time to first token
* p95 end-to-end latency
* Request errors

The selected p95 latency SLO was:

`3.0 seconds`

The benchmark results were:

| Concurrency | Tokens/s | p95 Latency |
| ----------- | -------: | ----------: |
| 1           |     57.7 |     1.313 s |
| 2           |    105.0 |     1.287 s |
| 4           |    206.9 |     1.287 s |
| 8           |    330.6 |     1.314 s |
| 16          |    446.7 |     1.533 s |

At concurrency `16`, throughput reached approximately `446.7 tokens/s` while p95 latency remained below the `3.0 s` target.

The reported knee was therefore:

`16 (sweep-bounded)`

This means the benchmark did not yet reach the true saturation point within the tested range. Throughput was still increasing at concurrency 16 while latency remained within the selected SLO.

The final capacity result was:

* Knee concurrency: `16`
* Throughput at knee: `446.7 tokens/s`
* p95 latency at knee: `1.533 s`
* Target p95 SLO: `3.0 s`
* Request errors: `0`

The lab includes the benchmark report, capacity note, and knee result files.

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
│   ├── bench_report.json
│   ├── capacity-note.md
│   └── knee.json
│
└── README.md
```

---

## Week 3 Outcome

By the end of Week 3, the serving stack progressed from individual GPU inference measurements to a benchmarked vLLM deployment with a validated model configuration.

The final setup demonstrates:

* A working vLLM inference server
* OpenAI-compatible API serving
* Improved throughput through continuous batching
* A validated and locked model configuration
* Quantised model evaluation
* Concurrency benchmarking
* p95 latency measurement
* Capacity estimation based on an SLO
* Successful automated verification

The final Week 3 benchmark completed with zero request errors and a `GREEN CHECK: PASS`.
