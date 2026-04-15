---
type: entity
entity_type: project
created: 2026-04-15
updated: 2026-04-15
tags:
  - domain/ai-infra
  - project
  - vllm
  - ascend
status: developing
related:
  - "[[vLLM]]"
  - "[[Ascend NPU]]"
  - "[[CUDAGraphMode]]"
sources:
  - "[[vllm-ascend-cudagraph-modes]]"
---

# vLLM-Ascend

vLLM-Ascend is the port of [[vLLM]] to [[Ascend NPU]] (Huawei Ascend AI accelerators. It enables LLM inference on Ascend NPUs with the same vLLM API.

## CUDAGraph Mode Support

As of 2026-04-15, vLLM-Ascend has the following support for [[CUDAGraphMode]]:

| User Config | Actual Mode | Supported | Notes |
|-------------|-------------|-----------|-------|
| `enforce_eager=True` → `NONE` | ✅ Full support |
| `PIECEWISE` → `PIECEWISE` | ✅ Full support |
| `FULL_DECODE_ONLY` → `FULL_DECODE_ONLY` | ✅ Supported |
| `FULL_AND_PIECEWISE` → `PIECEWISE` | ⚠️ Forced fallback (development incomplete |
| `FULL` → `FULL` | ✅ Supported |

## Forced Coercion Rules

Implemented in `vllm_ascend/platform.py at lines 338-345:

1. `FULL_AND_PIECEWISE` → `PIECEWISE` (development TODO)
2. Encoder-Decoder + `FULL_DECODE_ONLY` → `PIECEWISE` (only PIECEWISE tested)

Full details: [[vllm-ascend-cudagraph-modes]]

## Code Locations

- Forced coercion: `vllm_ascend/platform.py:338-345
- ACLGraph implementation: `vllm_ascend/compilation/acl_graph.py`

## See Also

- [[vLLM]]
- [[Ascend NPU]]
- [[CUDAGraphMode]]
- [[CUDA Graph]]
- [[vllm-ascend-cudagraph-modes]]
