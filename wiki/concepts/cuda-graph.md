---
type: concept
title: "CUDA Graph"
complexity: intermediate
domain: ai-infra
created: 2026-04-15
updated: 2026-04-15
tags:
  - domain/ai-infra
  - concept
  - cuda
  - compilation
  - optimization
status: developing
related:
  - "[[CUDAGraphMode]]"
  - "[[vLLM]]"
sources:
  - "[[vllm-ascend-cudagraph-modes]]"
---

# CUDA Graph

**CUDA Graph** is a CUDA programming model feature that allows pre-capturing a sequence of kernel operations into a single graph that can be launched repeatedly with much lower overhead.

## Purpose

In LLM inference, CUDA Graph reduces launch overhead when the same sequence of operations is repeated (e.g., in decode phase), improving inference throughput and latency.

## Modes in vLLM

vLLM defines five [[CUDAGraphMode]] options:

- `NONE` — no graph, all eager execution
- `PIECEWISE` — one graph per layer
- `FULL` — one graph for entire model
- `FULL_DECODE_ONLY` — only decode uses full graph
- `FULL_AND_PIECEWISE` — mixed mode (default in vLLM v1)

## Support Status in vLLM-Ascend

See [[vllm-ascend-cudagraph-modes]] for detailed support matrix. `FULL_AND_PIECEWISE` is not fully supported yet and falls back to `PIECEWISE`.

## See Also

- [[CUDAGraphMode]]
- [[vLLM]]
- [[vLLM-Ascend]]
