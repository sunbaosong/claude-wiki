---
type: concept
title: "CUDAGraphMode"
complexity: intermediate
domain: ai-infra
created: 2026-04-15
updated: 2026-04-15
tags:
  - domain/ai-infra
  - concept
  - vllm
  - cuda-graph
  - compilation
status: mature
related:
  - "[[CUDA Graph]]"
  - "[[vLLM]]"
  - "[[vLLM-Ascend]]"
sources:
  - "[[vllm-ascend-cudagraph-modes]]"
---

# CUDAGraphMode

**CUDAGraphMode** is an enumeration defined in upstream vLLM that controls how CUDA Graph capture is performed for inference.

## Definition

Defined in `vllm/config/compilation.py`:

| Enum | Value | Meaning |
|------|-------|---------|
| `NONE` | 0 | No CUDA graph, all execution in eager mode |
| `PIECEWISE` | 1 | **Piecewise capture**: one graph per layer/operation |
| `FULL` | 2 | **Full capture**: entire model forward pass as a single graph |
| `FULL_DECODE_ONLY` | `(FULL, NONE)` | Only decode stage uses FULL graph |
| `FULL_AND_PIECEWISE` | `(FULL, PIECEWISE)` | Mixed mode: decode uses FULL, prefill uses PIECEWISE |

## Default

The default mode in vLLM v1 is **`FULL_AND_PIECEWISE`**.

## Support in vLLM-Ascend

[[vLLM-Ascend]] implements forced coercion rules because not all modes are fully supported yet:

- ✅ `NONE` (enforce_eager) — fully supported
- ✅ `PIECEWISE` — fully supported
- ✅ `FULL_DECODE_ONLY` — supported  
- ✅ `FULL` — supported
- ⚠️ `FULL_AND_PIECEWISE` — forced fallback to `PIECEWISE` (still in development)

See: [[vllm-ascend-cudagraph-modes]] for detailed support matrix.

## Encoder-Decoder Special Case

For encoder-decoder models, any `FULL_DECODE_ONLY` is automatically coerced to `PIECEWISE` because only PIECEWISE has been tested on this architecture.

## See Also

- [[CUDA Graph]]
- [[vLLM]]
- [[vLLM-Ascend]]
- [[vllm-ascend-cudagraph-modes]]
