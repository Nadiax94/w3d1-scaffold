# W3D3 Lab: The Engine Swap

## Objective

Replace the Week 2 inference engine with **vLLM** while keeping the same OpenAI-compatible `/v1` API, then compare vLLM continuous batching against Monday's static batching baseline under concurrency.

Model used:

`Qwen/Qwen2.5-1.5B-Instruct`

Hardware:

`Google Colab - NVIDIA T4 GPU`

---

## Baseline

Monday's static batching results from `baselines.json`:

| Batch Size | Tokens/s |
|---|---:|
| 1 | 36.0 |
| 4 | 52.4 |
| 8 | 103.4 |

Static batching scaling from batch 1 to batch 8:

`103.4 / 36.0 = 2.87x`

---

## Prediction

Before running vLLM, I predicted:

- vLLM concurrency-8 throughput would be about `1.4x` Monday's batch-8 throughput.
- Static batching scaling: `2.87x`
- Predicted vLLM scaling from concurrency 1 to 8: `4.0x`
- vLLM scaling should be larger than static batching.
- Expected scaling improvement: about `1.4x`

---

## vLLM Configuration

The vLLM OpenAI-compatible server was launched with:

```text
--dtype half
--max-model-len 4096
--gpu-memory-utilization 0.85
--port 8000
