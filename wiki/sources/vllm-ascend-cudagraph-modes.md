---
type: source
title: "vLLM-Ascend CUDAGraph Modes Support Summary"
source_type: technical-notes
author:
date_published: 2026-04-15
url:
confidence: high
created: 2026-04-15
updated: 2026-04-15
tags:
  - domain/ai-infra
  - vllm
  - ascend
  - cuda-graph
  - compilation
status: mature
related:
  - "[[vLLM]]"
  - "[[Ascend NPU]]"
  - "[[CUDA Graph]]"
  - "[[CUDAGraphMode]]"
  - "[[vLLM-Ascend]]"
sources:
  - "[[.raw/vllm-ascend-cudagraph-modes.md]]"
---

# vLLM-Ascend CUDAGraph Modes Support Summary

Summary of CUDAGraph mode support in vLLM-Ascend as of 2026-04-15.

## Overview

vLLM-Ascend implements forced coercion rules for different CUDAGraph modes in `vllm_ascend/platform.py`. Not all upstream modes are fully supported yet.

## Upstream CUDAGraphMode Definition

| Enum | Value | Meaning |
|------|-------|---------|
| `NONE` | 0 | No CUDA graph, all eager mode |
| `PIECEWISE` | 1 | **Piecewise capture**: one graph per layer/operation |
| `FULL` | 2 | **Full capture**: entire model forward pass as one graph |
| `FULL_DECODE_ONLY` | `(FULL, NONE)` | Only decode uses FULL graph |
| `FULL_AND_PIECEWISE` | `(FULL, PIECEWISE)` | Mixed: decode FULL, prefill PIECEWISE |

Default in vLLM v1: `FULL_AND_PIECEWISE`

## vLLM-Ascend Support Matrix

| User Config | Actual Mode | Supported | Notes |
|-------------|-------------|-----------|-------|
| `enforce_eager=True` | `NONE` | ✅ Full | No cudagraph at all |
| `PIECEWISE` | `PIECEWISE` | ✅ Full | One graph per layer |
| `FULL_DECODE_ONLY` | `FULL_DECODE_ONLY` | ✅ Supported | For decode performance analysis |
| `FULL_AND_PIECEWISE` | `PIECEWISE` | ⚠️ **Forced fallback** | Mixed mode not fully implemented yet (TODO) |
| `FULL` | `FULL` | ✅ Supported | Whole model forward as one graph |

## Forced Coercion Rules

Source: `vllm_ascend/platform.py` ~lines 338-345

1. **`FULL_AND_PIECEWISE` → `PIECEWISE`**
   - Reason: Mixed mode support still in development
   - Status: TODO, will be fully supported in future

2. **Encoder-Decoder + `FULL_DECODE_ONLY` → `PIECEWISE`**
   - Reason: Only PIECEWISE tested for encoder-decoder architectures
   - Applies to any encoder-decoder model

## Validation Results (2026-04-15)

All four scenarios validated successfully:

| Scenario | NPU | Config | Actual | Result |
|----------|-----|--------|--------|--------|
| enforce_eager | 0 | `--enforce-eager` → `NONE` | `NONE` | ✅ Captured |
| PIECEWISE | 1 | `cudagraph_mode=PIECEWISE` | `PIECEWISE` | ✅ Captured |
| FULL_DECODE_ONLY | 2 | `cudagraph_mode=FULL_DECODE_ONLY` | `FULL_DECODE_ONLY` | ✅ Captured |
| FULL_AND_PIECEWISE | 3 | `cudagraph_mode=FULL_AND_PIECEWISE` | `PIECEWISE` | ✅ Captured (fallback works) |

## Mode Selection Guide

| Use Case | Recommended Mode |
|----------|------------------|
| Debugging, single-operator analysis | `enforce_eager` |
| Normal inference, default | `PIECEWISE` (or `FULL_AND_PIECEWISE` will fallback) |
| Decode performance analysis | `FULL_DECODE_ONLY` |
| Future when FULL support is complete | `FULL_AND_PIECEWISE` |

## Code Locations

- Forced coercion: `/vllm-workspace/vllm-ascend/vllm_ascend/platform.py:338-345`
- Enum definition: `/vllm-workspace/vllm/vllm/config/compilation.py`
- ACLGraph implementation: `/vllm-workspace/vllm-ascend/vllm_ascend/compilation/acl_graph.py`

## Changelog

- **2026-04-15**: Initial document, summary based on current master branch
