# Cycle 32 — Deep dive 03: Attention variants — MHA, MQA, GQA and MLA

Date: 2026-10-15
Status: Third architecture deep dive. These are attention/KV-cache design variants, not four unrelated model families.

## 1. The shared baseline

In autoregressive Transformer inference, each new token attends to earlier tokens. To avoid recomputing previous key/value representations at every step, implementations normally cache them in a **KV cache**.

For each layer and prior token, the cache stores keys (K) and values (V). Its approximate element count is:

- MHA: 2 × number_of_query_heads × head_dimension × cached_tokens × layers
- GQA: 2 × number_of_KV_heads × head_dimension × cached_tokens × layers
- MQA: 2 × 1 × head_dimension × cached_tokens × layers
- MLA: compressed latent KV state plus its position-related state, per token and layer

Multiply element counts by bytes per stored element to estimate cache bytes; actual memory also includes metadata, alignment, temporary buffers, and implementation-specific allocations. Batch size multiplies cache needs too.

Primary references:
- Transformer / MHA (Vaswani et al., 2017): https://arxiv.org/abs/1706.03762
- MQA (Shazeer, 2019): https://arxiv.org/abs/1911.02150
- GQA (Ainslie et al., 2023): https://arxiv.org/abs/2305.13245
- MLA / DeepSeek-V2 (2024): https://arxiv.org/abs/2405.04434

## 2. MHA — Multi-Head Attention

Each query head has its own corresponding key and value head. Heads can learn different attention patterns, but storing K and V for every head makes the cache comparatively large.

**Strengths:** established design; strong general-purpose baseline; broad implementation support.
**Costs:** cache size grows linearly with sequence length, layer count, batch size, and KV-head count.
**Important:** reducing the cache does not automatically reduce the model's weight footprint by the same amount. KV cache is runtime state, separate from model weights.

## 3. MQA — Multi-Query Attention

All query heads share one key head and one value head. This sharply reduces KV-cache size and memory traffic associated with reading the cache during decoding.

**Strengths:** compact cache; can improve decode throughput when KV bandwidth is the bottleneck.
**Costs:** less independent K/V representation than MHA; quality and speed effects depend on the model and implementation.
**Caveat:** a smaller cache is not a guarantee of faster CPU inference; projection costs, kernels, cache locality, and model dimensions still matter.

## 4. GQA — Grouped-Query Attention

Several query heads share each K/V head. GQA lies between MHA and MQA: the number of KV heads is smaller than the number of query heads, but greater than one.

**Strengths:** practical middle ground between cache size and representational flexibility; used in modern decoder models.
**Costs:** actual gains depend on whether the runtime efficiently reuses shared K/V data. A poorly optimized kernel may fail to realize theoretical savings.
**Key point:** the number of KV heads—not merely the number of query heads—determines the main cache-size multiplier.

## 5. MLA — Multi-Head Latent Attention

Introduced in DeepSeek-V2, MLA compresses the key/value information into a lower-dimensional latent representation and caches that representation instead of the full per-head K/V tensors. It uses additional projections to reconstruct the information needed for attention and includes a decoupled positional pathway for RoPE compatibility.

**Strengths:** can reduce KV-cache storage substantially while retaining multi-head attention behavior.
**Costs:** additional projection/reconstruction computation and implementation complexity; performance depends on whether the runtime can exploit the compressed representation efficiently.
**Evidence:** DeepSeek-V2 reports a 93.3% KV-cache reduction versus its DeepSeek 67B comparison baseline. This is a result for that particular model comparison—not a universal MLA reduction percentage or a prediction for CPU speed.

Follow-up research has explored adapting existing MHA/GQA models to MLA, but conversion and fine-tuning results should be treated as method- and model-specific:
- MHA2MLA (ACL 2025): https://aclanthology.org/2025.acl-long.1597/
- TransMLA (2025): https://arxiv.org/abs/2502.07864

## 6. What the variants optimize — and what they do not

| Variant | Main cache strategy | Main trade-off |
|---|---|---|
| MHA | Store K/V per query head | Largest cache among these standard forms; straightforward baseline |
| MQA | Share one K/V head across all query heads | Small cache; more aggressive sharing |
| GQA | Share K/V within groups of query heads | Intermediate cache and sharing |
| MLA | Cache compressed latent K/V state | Smaller cache at the cost of projections and more specialized implementation |

These variants do **not** by themselves:
- shrink all model weights;
- remove attention's dependence on previous tokens;
- guarantee faster prefill (processing a prompt) and decode (generating one token at a time) equally;
- make a long context free: cache still grows with context length;
- turn a Transformer into a non-Transformer architecture.

## 7. CPU-first assessment for the compact assistant

For the user's target—CPU-first, roughly 1–2 GB RAM and an absolute core ceiling around 4 GB—the practical questions are:

1. Does the chosen model fit in memory after weights, runtime buffers, and cache are counted?
2. At the intended context length, how many MB/GB does its KV cache consume at the actual cache precision?
3. Does the CPU runtime implement that exact attention variant efficiently?
4. Are prompt processing and token-by-token decode measured separately?
5. Does the variant preserve quality on the same model family, task mix, and evaluation protocol?

**Initial ranking for investigation, not a universal quality ranking:**
- GQA: pragmatic first candidate when selecting a pretrained small model, because it is widely used and reduces cache relative to MHA.
- MQA: worth testing when cache/bandwidth is the dominant constraint, but quality and kernel behavior must be checked.
- MLA: compelling for long-context cache reduction, but do not assume its specialized projection path will be faster on an older CPU without runtime support.
- MHA: retain as a reference baseline, especially for controlled comparisons.

For a short context and tiny batch, KV cache may be a minor fraction of total RAM; then weight size, quantization, and dense matrix kernels can matter more. For longer conversations, cache may become more important. Measure both cases.

## 8. Benchmark protocol

For each model/runtime, record:
- exact architecture, query-head count, KV-head count, layer count, head dimension, context, batch, cache precision and weight quantization;
- peak process RSS (not only Python allocations), model weight bytes, cache bytes if exposed;
- cold start and model-load time;
- prompt prefill tokens/s and decode tokens/s over a fixed prompt/output length;
- quality on the same evaluation set, including held-out compositions and UNKNOWN calibration;
- CPU utilization, memory bandwidth if measurable, and runtime/kernel version.

Compare variants only when model quality and configuration are reasonably comparable. A paper's GPU throughput claim is not a CPU performance result.

## 9. Evidence classification

- **Established:** MHA, MQA, GQA and MLA are documented attention/KV-state designs.
- **Paper-specific evidence:** reported cache reductions and benchmark improvements apply to the reported models and settings.
- **Not established without testing:** that MLA or MQA is fastest on a given CPU, that lower cache means lower total RAM by the same ratio, or that a converted model preserves all quality.
- **System-dependent:** speed relies on runtime kernels, memory bandwidth, sequence length, batch size, quantization, and the hardware.

## 10. Decision

Treat attention design as a component axis in the architecture registry, separate from the broader family (Transformer, SSM/RWKV, graph/neuro-symbolic, etc.). For practical small-model selection, record GQA/MQA/MLA alongside weight size and quantization; do not use the attention label alone as a winner signal.

Next deep dive: state-space and recurrent sequence models (S4/S5, Mamba/Mamba-2, RetNet, RWKV), compared against the same Transformer baseline.
