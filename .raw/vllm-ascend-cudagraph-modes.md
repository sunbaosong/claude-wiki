# vLLM-Ascend ACLGraph CUDAGraph 模式支持情况总结

本文档总结当前 (2026-04-15) vLLM-Ascend 对不同 CUDAGraph 模式的支持情况，以及强制转换规则。

## 目录

- [CUDAGraphMode 定义 (upstream vLLM)](#cudagraphmode-定义-upstream-vllm)
- [vLLM-Ascend 当前支持情况](#vllm-ascend-当前支持情况)
- [强制转换规则](#强制转换规则)
- [验证结论](#验证结论)
- [参考代码位置](#参考代码位置)

---

## CUDAGraphMode 定义 (upstream vLLm)

vLLM upstream 定义了以下几种模式 (`vllm/config/compilation.py`):

| 枚举值 | 组合值 | 含义 |
|--------|--------|------|
| `NONE` | `0` | 不使用 CUDA Graph，全部 eager 模式 |
| `PIECEWISE` | `1` | **分段捕获**：每层（每个操作）捕获一个独立的 graph |
| `FULL` | `2` | **完整捕获**：整个模型前向捕获为一个大图 |
| `FULL_DECODE_ONLY` | `(FULL, NONE)` | **仅 decode 完整捕获**：只对 decode 阶段使用 FULL graph，prefill 使用 eager |
| `FULL_AND_PIECEWISE` | `(FULL, PIECEWISE)` | **混合模式**：decode 使用 FULL graph，prefill 使用 PIECEWISE |

> 这是 vLLM v1 的默认模式：`FULL_AND_PIECEWISE`

---

## vLLM-Ascend 当前支持情况

vLLM-Ascend 在 `/vllm_ascend/platform.py` 中对不同模式做了预处理和强制转换：

| 用户配置 | 实际运行模式 | 是否支持 | 说明 |
|---------|-------------|---------|------|
| `enforce_eager=True` | `NONE` | ✅ 完全支持 | 不使用任何 cudagraph，全部 eager mode |
| `PIECEWISE` | `PIECEWISE` | ✅ 完全支持 | 分段捕获，每层一个 graph |
| `FULL_DECODE_ONLY` | `FULL_DECODE_ONLY` | ✅ 已支持 | decode 全程 FULL graph，适合 decode 性能分析 |
| `FULL_AND_PIECEWISE` | `PIECEWISE` | ⚠️  **强制 fallback 到 PIECEWISE** | FULL graph 混合模式尚未完全支持，TODO 中 |
| `FULL` | `FULL` | ✅ 已支持 | 整个模型前向捕获为一个图 |

---

## 强制转换规则

源代码位置：`vllm_ascend/platform.py` 约 338-345 行

```python
# TODO: Full graph is fully supported later, the default will be full graph.
if compilation_config.cudagraph_mode == CUDAGraphMode.FULL_AND_PIECEWISE:
    compilation_config.cudagraph_mode = CUDAGraphMode.PIECEWISE

# Encoder-decoder models currently only support piecewise mode
if model_config and model_config.is_encoder_decoder is True:
    if compilation_config.cudagraph_mode == CUDAGraphMode.FULL_DECODE_ONLY:
        logger.warning("encoder-decoder model doesn't support FULL_DECODE_ONLY, fallback to PIECEWISE ")
    compilation_config.cudagraph_mode = CUDAGraphMode.PIECEWISE
```

### 转换规则说明：

1. **`FULL_AND_PIECEWISE` → `PIECEWISE`**
   - **原因**：FULL graph + PIECEWISE 混合模式的支持还在开发中
   - **状态**：TODO，将来会完全支持
   - 当前降级：退回到纯 `PIECEWISE` 模式

2. **Encoder-Decoder 模型 + `FULL_DECODE_ONLY` → `PIECEWISE**
   - **原因**：encoder-decoder 架构目前只测试过 PIECEWISE 模式
   - 任何 encoder-decoder 模型都会强制降级到 PIECEWISE

---

## 本次采集验证

我们对四种场景分别采集验证：

| 场景 | NPU | 配置 | 实际运行 | 结果 |
|------|-----|------|---------|------|
| enforce_eager | 0 | `--enforce-eager` → `NONE` | `NONE` | ✅ 成功采集 |
| PIECEWISE | 1 | `cudagraph_mode=PIECEWISE` | `PIECEWISE` | ✅ 成功采集 |
| FULL_DECODE_ONLY | 2 | `cudagraph_mode=FULL_DECODE_ONLY` | `FULL_DECODE_ONLY` | ✅ 成功采集 |
| FULL_AND_PIECEWISE | 3 | `cudagraph_mode=FULL_AND_PIECEWISE` | `PIECEWISE` | ✅ 成功采集，但实际是 PIECEWISE |

> 这验证了代码中的强制转换逻辑：**`PIECEWISE` 和 `FULL_AND_PIECEWISE` 最终都是 PIECEWISE**

---

## 如何选择模式

| 使用场景 | 推荐模式 |
|---------|---------|
| 调试、单算子分析 | `enforce_eager` |
| 常规推理、默认情况 | `PIECEWISE` (或 `FULL_AND_PIECEWISE`，但会 fallback) |
| 专门分析 decode 阶段性能 | `FULL_DECODE_ONLY` |
| 未来 FULL 支持完善后 | `FULL_AND_PIECEWISE` |

---

## 参考代码位置

- 强制转换逻辑：`/vllm-workspace/vllm-ascend/vllm_ascend/platform.py:338-345`
- 枚举定义：`/vllm-workspace/vllm/vllm/config/compilation.py`
- ACLGraph 实现：`/vllm-workspace/vllm-ascend/vllm_ascend/compilation/acl_graph.py`

---

## 更新记录

- **2026-04-15**：初始文档，基于当前 master 分支代码总结
