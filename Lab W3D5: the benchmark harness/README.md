# Lab W3D5 - Benchmark Harness and Capacity

## Objective

Benchmark the locked vLLM model across increasing concurrency levels, identify the highest concurrency that still satisfies the p95 latency SLO, and convert that safe capacity into a cost and scale-out plan.

## Locked Serving Configuration

- Model: `Qwen/Qwen2.5-1.5B-Instruct-AWQ`
- Quantisation: `AWQ`
- dtype: `half`
- Max model length: `4096`
- GPU memory utilization: `0.85`
- Tool-call parser: `hermes`
- Auto tool choice: enabled
- Environment: Google Colab T4

## Benchmark

The reference benchmark harness was run at concurrency levels:

`1, 2, 4, 8, 16`

with `20` requests per level.

### Results

| Concurrency | Tokens/s | TTFT p95 | p95 Latency | Errors |
|---:|---:|---:|---:|---:|
| 1 | 91.56 | 0.1136 s | 1.4353 s | 0 |
| 2 | 157.18 | 0.2074 s | 1.8853 s | 0 |
| 4 | 282.00 | 0.1200 s | 1.7513 s | 0 |
| 8 | 484.75 | 0.1611 s | 1.8863 s | 0 |
| 16 | 721.11 | 0.2553 s | 2.2885 s | 0 |

## Capacity Result

Target p95 SLO:

`2.0 seconds`

The highest tested concurrency that remains under the SLO is:

`Knee = 8`

At the knee:

- Throughput: `484.75 tokens/s`
- p95 latency: `1.8863 s`
- TTFT p95: `0.1611 s`
- Errors: `0`
- Measured request rate: approximately `4.67 requests/s`

Concurrency `16` produces higher throughput (`721.11 tokens/s`) but its p95 latency is `2.2885 s`, which exceeds the target. It is therefore past the usable knee.

### Verification

```text
levels: 5, concurrencies: [1, 2, 4, 8, 16], total errors: 0
capacity-note.md: all fields filled
GREEN CHECK: PASS
```

---

# Extra Lab W3D5 - Cost per Million Tokens and Scale-Out

## Cost per Million Tokens

GPU hourly price used:

`$0.35/hour`

| Concurrency | Tokens/s | p95 Latency | Cost / 1M Tokens |
|---:|---:|---:|---:|
| 1 | 91.56 | 1.4353 s | $1.0618 |
| 2 | 157.18 | 1.8853 s | $0.6185 |
| 4 | 282.00 | 1.7513 s | $0.3448 |
| 8 | 484.75 | 1.8863 s | $0.2006 |
| 16 | 721.11 | 2.2885 s | $0.1348 |

The cheapest raw cost per token occurs at concurrency `16`, but that level violates the `2.0 s` p95 SLO. The cost that corresponds to safe usable capacity is therefore:

`$0.2006 per 1M output tokens at concurrency 8`

## Scale-Out Plan

Each replica is kept at the safe knee instead of pushing a single GPU beyond the SLO.

| Required Throughput | Replicas | Hourly Cost | Effective p95 |
|---:|---:|---:|---:|
| 484.75 tokens/s (1.0x) | 1 | $0.35 | 1.8863 s |
| 727.125 tokens/s (1.5x) | 2 | $0.70 | 1.8863 s |
| 969.50 tokens/s (2.0x) | 2 | $0.70 | 1.8863 s |
| 1454.25 tokens/s (3.0x) | 3 | $1.05 | 1.8863 s |

### Extra Lab Verification

```text
recomputed costs, knee and scale-out plan all agree
GREEN CHECK: PASS
```

## Artifacts

- `Lab_W3D5_the_benchmark_harness_and_Extra_Lab.ipynb`
- `bench_report.json`
- `knee.json`
- `capacity-note.md`
- `cost_report.json`
