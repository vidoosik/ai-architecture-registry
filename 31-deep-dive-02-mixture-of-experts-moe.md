# Cycle 31 — Deep dive 02: Mixture of Experts (MoE)

Date: 2026-10-15
Status: Second architecture deep dive; compares sparse expert routing with dense Transformer FFNs.

## 1. What MoE changes

Mixture of Experts (MoE) replaces some dense feed-forward sublayers with multiple expert networks and a router. For each token, the router scores experts and selects a subset; the selected expert outputs are weighted/combined. The rest of the Transformer may remain unchanged, including attention, residual connections, normalization and autoregressive decoding.

MoE is therefore usually a **sparsely activated Transformer variant**, not a fully separate non-Transformer architecture.

Primary sources:
- Mixtral of Experts (2024): https://arxiv.org/abs/2401.04088
- DeepSeek-V3 Technical Report (2024): https://arxiv.org/abs/2412.19437
- DeepSeekMoE (2024): https://arxiv.org/abs/2401.06066

## 2. Intuition and basic mechanics

A dense FFN sends every token through the same FFN weights. An MoE layer contains experts FFN_1 ... FFN_E and a router R(h) that scores them. A top-k router selects k experts for token representation h, then combines their outputs:
- scores = R(h)
- S = TopK(scores, k)
- output = sum over i in S of gate_i(h) × FFN_i(h)

The router can vary the selected experts by token and layer. Experts are learned sub-networks; they are not automatically human-readable skills such as “math expert” or “English expert”. Specialization may emerge, but it must be measured rather than assumed.

## 3. Total parameters versus active parameters

- **Total parameters**: weights stored across all experts and shared components.
- **Active parameters per token**: weights actually used for that token's forward pass, including the selected experts and common layers.
- **Resident memory**: weights that must be kept in RAM/VRAM or otherwise fetched. Sparse activation does not make inactive expert weights disappear from storage.
- **Actual latency**: depends on routing, memory locality, expert batching, parallel hardware, communication overhead and kernel efficiency—not only active parameter count.

Examples from primary papers:
- Mixtral 8x7B: eight feed-forward experts per MoE layer, with two experts selected per token; the paper reports 47B total and 13B active parameters.
- DeepSeek-V3: 671B total parameters and 37B activated per token; its design combines DeepSeekMoE with MLA, a load-balancing strategy, MTP training and large-scale infrastructure optimizations.

These are large distributed-GPU systems. They do **not** imply that a 671B MoE is suitable for a 2–4 GB CPU-only assistant.

## 4. Main MoE variants to distinguish

- **Top-k sparse MoE**: selects a fixed number of experts per token.
- **Fine-grained MoE / DeepSeekMoE**: uses more, smaller routed experts and shared experts, with a particular goal of improving specialization and parameter efficiency.
- **Shared experts**: some experts process every token while routed experts add conditional capacity.
- **Routing/load-balancing methods**: auxiliary losses, router biases or other constraints aim to avoid a few experts receiving most tokens.
- **Expert-choice / token-choice routing**: different rules determine whether tokens choose experts or experts choose tokens; capacity and dropping/overflow behavior can differ.
- **MoE vs MoD vs MoR**: MoE routes tokens among experts; Mixture-of-Depths routes token positions through computation at a given depth; Mixture of Recursions routes/reuses recurrent shared computation. They are not synonyms.
- **MoE plus MLA/GQA/linear attention**: these can coexist. MLA changes the attention/KV representation; MoE changes the FFN computation path.

## 5. Benefits

1. **More model capacity for a given active-compute budget.** Each token uses only a subset of expert weights.
2. **Conditional computation.** Different tokens can use different expert combinations.
3. **Scaling demonstrated at large model sizes.** Mixtral and DeepSeek-V3 provide concrete examples of high-performing sparse MoE language models.
4. **Modular growth potential.** Expert modules can, in principle, be added or adapted without scaling every dense layer equally; training stability and interference still require evaluation.

## 6. Costs and failure modes

1. **Weights still have to live somewhere.** At inference, all experts may need to be resident or efficiently loaded. For a small-RAM machine, total weight storage is often the limiting factor.
2. **Sparse does not automatically mean fast.** A CPU may execute one dense compact model faster than an MoE because expert routing adds indirection and scattered memory access.
3. **Routing overhead and load imbalance.** Hot experts, capacity limits and token dispatch can waste compute or require communication.
4. **Distributed communication.** Large MoE training/inference can require all-to-all communication across GPUs; this is a major reason systems-level co-design matters.
5. **Quality can suffer from poor specialization or balancing.** Routing is learned, not a guaranteed semantic partition.
6. **Quantization and expert offloading add complexity.** Lower-bit weights may reduce memory but have hardware/kernel-dependent speed and quality effects.
7. **Active parameters are not an apples-to-apples quality metric.** Training data, total capacity, architecture, inference recipe and evaluation all affect quality.

## 7. CPU-first assessment for the compact assistant

**Initial conclusion: classic large MoE is a poor default for 1–2 GB RAM, with 4 GB as a hard ceiling.** Its main advantage is reducing active compute while retaining a large total parameter pool; it does not inherently reduce total weight storage enough for this target.

MoE is still worth keeping in the research map for:
- very small expert banks where most weights fit in memory;
- disk-backed specialists or modular skill systems, if switching/loading costs are measured;
- selective modules for distinct capabilities, provided the system has explicit routing and uncertainty checks.

Do not assume a hand-written router plus separately trained small models is equivalent to a trained MoE. It is a modular system design and needs its own baseline.

For a fair CPU experiment compare:
- a dense model and MoE with comparable quality/data/active compute where possible;
- model bytes, resident RSS, load/switch latency, prefill and decode speed;
- routing overhead, expert hit distribution, cache hit rate, cold-start and worst-case latency;
- held-out combinations and paraphrases, not just in-distribution average accuracy;
- quality and calibrated UNKNOWN, including cases where the router picks the wrong expert.

## 8. Evidence classification

- **Established family:** sparse MoE language models and top-k expert routing.
- **Paper-reported results:** Mixtral and DeepSeek-V3 model sizes and benchmark results, under their published training/inference settings.
- **Not guaranteed:** a 2–4 GB implementation being faster or smarter than a dense model; interpretable expert roles; large quality gains from expert count alone.
- **System dependency:** practical speed relies heavily on memory bandwidth, kernel support, batching and—in large deployments—interconnect topology.

## 9. Sources

1. Jiang et al., *Mixtral of Experts*: https://arxiv.org/abs/2401.04088
2. DeepSeek-AI, *DeepSeek-V3 Technical Report*: https://arxiv.org/abs/2412.19437
3. Dai et al., *DeepSeekMoE: Towards Ultimate Expert Specialization in Mixture-of-Experts Language Models*: https://arxiv.org/abs/2401.06066
4. Cycle 29, recent candidate search: https://github.com/vidoosik/ai-architecture-registry/blob/main/29-second-pass-architecture-search-2025-2026.md

## 10. Decision

Keep MoE as an important scaling strategy, but do not treat it as a memory-saving solution by default. For the compact CPU-first project, investigate only small/local expert configurations or modular specialist loading after measuring memory and switch latency. Continue to compare it against a dense Transformer baseline and against non-Transformer families.
