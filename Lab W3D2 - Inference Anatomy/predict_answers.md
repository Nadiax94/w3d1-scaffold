# Lab W3D2 - Inference Anatomy
## Predict Answers

### 1) TTFT and prompt length

Answer:

up

Explanation:
A longer prompt increases TTFT because the prefill stage reads and processes the whole prompt before generating the first token.

---

### 2) TPOT dependency

Answer:

model size and memory bandwidth

Explanation:
After the first token, decoding generates one token at a time and performance depends mostly on model size and memory bandwidth.

---

### 3) KV Cache calculation

Given:

- Layers = 28
- KV heads = 2
- Head dim = 128
- fp16 = 2 bytes

Calculation:

2 × 28 × 2 × 128 × 2 = 28672 bytes

28672 / 1024 = 28 KB

Answer:

28 KB per token

---

### 4) KV Cache for 4096 tokens

Calculation:

4096 × 28 KB = 114688 KB

≈ 0.11 GB

Answer:

0.11 GB

---

### 5) Static batching

Answer:

The longest prompt finishes.

Explanation:
Static batching waits for the longest request because all sequences run together.
