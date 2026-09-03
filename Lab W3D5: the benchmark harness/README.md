Lab W3D5 – Benchmark Harness

Model: Qwen/Qwen2.5-1.5B-Instruct

Target p95 SLO: 3.0 s

Benchmark results:

* Concurrency 1: 57.7 tokens/s, p95 1.313 s
* Concurrency 2: 105.0 tokens/s, p95 1.287 s
* Concurrency 4: 206.9 tokens/s, p95 1.287 s
* Concurrency 8: 330.6 tokens/s, p95 1.314 s
* Concurrency 16: 446.7 tokens/s, p95 1.533 s

Knee concurrency: 16 (sweep-bounded)

Tokens/s at knee: 446.7
p95 at knee: 1.533 s
Errors: 0

Finding:
The sweep did not reach the true saturation point. Throughput was still increasing at concurrency 16 while p95 latency remained below the 3.0 s target.

Green Check: PASS

