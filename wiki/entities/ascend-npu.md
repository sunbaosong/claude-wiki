---
type: entity
entity_type: organization-product
created: 2026-04-15
updated: 2026-04-15
tags:
  - domain/ai-infra
  - hardware
  - ascend
  - npu
status: developing
related:
  - "[[vLLM-Ascend]]"
  - "[[vLLM]]"
sources:
  - "[[vllm-ascend-cudagraph-modes]]"
---

# Ascend NPU

Ascend NPU (Neural Processing Unit) is Huawei's AI accelerator architecture for deep learning inference and training.

## In vLLM

[[vLLM-Ascend]] ports the vLLM inference engine to run on Ascend NPUs, providing similar API and capabilities as CUDA version.

## CUDA Graph Equivalent

On Ascend NPUs, the equivalent of CUDA Graph is implemented via ACLGraph.

## See Also

- [[vLLM-Ascend]]
- [[vLLM]]
- [[CUDAGraphMode]]
