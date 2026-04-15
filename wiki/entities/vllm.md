---
type: entity
entity_type: project
created: 2026-04-15
updated: 2026-04-15
tags:
  - domain/ai-infra
  - project
  - llm
  - inference
status: developing
related:
  - "[[vLLM-Ascend]]"
  - "[[CUDAGraphMode]]"
sources:
  - "[[vllm-ascend-cudagraph-modes]]"
---

# vLLM

vLLM is a high-throughput and memory-efficient inference and serving engine for large language models.

## Key Features

- PagedAttention for efficient KV cache management
- Continuous batching
- Optimized CUDA kernels
- CUDA Graph support for different capture modes

## CUDAGraph Modes

vLLM defines five [[CUDAGraphMode]] that control how CUDA Graph capture is done:

- `NONE` - no CUDA graph (eager mode)
- `PIECEWISE` - piecewise capture per layer
- `FULL` - full model capture
- `FULL_DECODE_ONLY` - decode only full capture
- `FULL_AND_PIECEWISE` - mixed mode (default in vLLM v1)

## Upstream vs vLLM vs vLLM-Ascend

[[vLLM-Ascend]] is the port of vLLM to Ascend NPUs. It has different support status for CUDAGraph modes due to hardware-specific implementation constraints.

## See Also

- [[vLLM-Ascend]]
- [[Ascend NPU]]
- [[CUDAGraphMode]]
- [[CUDA Graph]]
