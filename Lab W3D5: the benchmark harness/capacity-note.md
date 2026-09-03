# Capacity note (team, one page)

## The numbers

- Locked model: Qwen/Qwen2.5-1.5B-Instruct-AWQ
- Target p95 end-to-end latency (your SLO today): 2.0 seconds
- Knee concurrency (highest concurrency whose p95 is still under target): 4
- Tokens per second at the knee: 275.39
- Max sustainable request rate at the target p95: 2.75 req/s

## The limiting family

- Memory-bound/decode-limited: throughput continues increasing as concurrency rises, but p95 latency crosses the 2.0-second SLO at concurrency 8, indicating that the decode-side memory-bandwidth limit is being approached.

## Why the knee, not the peak

- The knee is the highest concurrency that still meets the 2.0-second p95 SLO. Although peak throughput reaches 683.82 tokens/s at concurrency 16, its 2.4308-second p95 latency violates the SLO, so concurrency 4 is the appropriate operating point.
