# Week 3 Labs

This repository contains my Week 3 lab work focused on LLM inference, GPU serving, vLLM, quantisation, benchmarking, and capacity planning.

## Overview

During Week 3, the labs progressed from profiling inference on a real GPU to serving a locked model with vLLM, measuring concurrency scaling, and translating benchmark capacity into cost and scale-out decisions.

---

## Labs

### Lab W3D1 - Profile Inference on a Real GPU

This lab focuses on profiling LLM inference on a Google Colab T4 GPU and observing real GPU memory and runtime behavior.

---

### Lab W3D2 - Inference Anatomy

This lab studies the main stages of LLM inference, including prefill, decode, TTFT, token generation behavior, KV cache, and GPU utilization.

---

### Lab W3D3 - The Engine Swap

This lab replaces the previous inference engine with **vLLM** while keeping an OpenAI-compatible `/v1` API.

It measures throughput under increasing concurrency and demonstrates the benefit of continuous batching.

`GREEN CHECK: PASS`

---

### Lab W3D4 - Quantise and Lock the Model

This lab compares fp16 and AWQ serving with vLLM, validates tool/function calling, and locks the model configuration for the final benchmark.

Locked serving model:

`Qwen/Qwen2.5-1.5B-Instruct-AWQ`

`GREEN CHECK: PASS`

---

### Lab W3D5 - Benchmark Harness and Capacity

This lab benchmarks the locked AWQ model at concurrency levels `1, 2, 4, 8, 16` using the reference benchmark harness.

Final capacity result:

* Target p95 SLO: `2.0 s`
* Knee concurrency: `8`
* Throughput at the knee: `484.75 tokens/s`
* p95 latency at the knee: `1.8863 s`
* TTFT p95 at the knee: `0.1611 s`
* Errors: `0`

Concurrency `16` reached `721.11 tokens/s`, but its p95 latency increased to `2.2885 s`, exceeding the SLO. Therefore, concurrency `8` is the usable capacity point.

`GREEN CHECK: PASS`

#### Extra Lab - Cost per Million Tokens and Scale-Out

Using a representative T4-class GPU rate of `$0.35/hour`:

* Cost at the knee: `$0.2006 per 1M output tokens`
* `1.0x` knee throughput: `1 replica` — `$0.35/hour`
* `1.5x` knee throughput: `2 replicas` — `$0.70/hour`
* `2.0x` knee throughput: `2 replicas` — `$0.70/hour`
* `3.0x` knee throughput: `3 replicas` — `$1.05/hour`

Although concurrency `16` is cheaper per token on paper (`$0.1348/M`), it violates the `2.0 s` p95 SLO, so it is not counted as safe capacity.

`GREEN CHECK: PASS`

---

## Week 3 Outcome

By the end of Week 3, the serving stack progressed from basic GPU inference measurements to a validated and benchmarked vLLM deployment with:

* An OpenAI-compatible inference endpoint
* AWQ quantisation
* Tool-call support
* Concurrency and throughput benchmarking
* p95 latency-based capacity selection
* Cost-per-million-token analysis
* Replica scale-out planning
