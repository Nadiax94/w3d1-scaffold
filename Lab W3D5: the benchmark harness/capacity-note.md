# Capacity note (team, one page)

## The numbers

- Locked model: Qwen/Qwen2.5-1.5B-Instruct
- Target p95 end-to-end latency (your SLO today): 3.0 seconds
- Knee concurrency (highest concurrency whose p95 is still under target):
  16 (sweep-bounded)
- Tokens per second at the knee: 446.7
- Max sustainable request rate at the target p95:
  approximately 10.4 req/s

## The limiting family

- No limiting family was conclusively reached inside this sweep: throughput was still rising at concurrency 16 while p95 remained below the 3.0 s SLO, so the true saturation point is beyond the tested range.

## Why the knee, not the peak

- I report capacity at the SLO because throughput beyond that point is not useful capacity if request latency no longer meets the promised p95 target.
