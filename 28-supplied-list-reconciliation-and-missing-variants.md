# Cycle 28 — Reconcile the supplied architecture list against the registry

Date: 2026-10-15

Purpose: compare the user's long candidate list against the existing registry, preserve existing entries, and add missing named variants without treating promotional claims as established facts. This is a coverage audit, not a ranking or architecture choice.

## 1. Already represented in the registry (sometimes under a family name)

These were present before this reconciliation, so do not duplicate them as new families:
- Transformer, MHA/MQA/GQA, MLA, local/sparse/linear attention, Performer/FAVOR+, FlashAttention, Transformer-XL, Compressive Transformer, RMT, kNN-LM, RAG.
- MoE / sparse expert routing; BitNet/ternary quantization; Tensor Train and other tensor networks; Monarch/Toeplitz structured matrices; KAN; FNO/neural operators.
- Mamba/Mamba-2, S4/S5/HiPPO, RetNet, RWKV, xLSTM, Gated DeltaNet/delta-rule fast weights, Hyena, Griffin, HGRN, Mega, liquid networks, reservoir computing, SNN/spiking predictive coding.
- Titans, MIRAS, ATLAS, TNT, fast weights, test-time training, continual learning and external memory.
- JEPA, predictive coding, energy-based models, Neural ODE/CDE, Deep Equilibrium Models, diffusion language models, HRM/TRM/SE-RRM, Coconut/continuous latent reasoning concepts, graph/neuro-symbolic reasoning, VSA/HDC, MPS/MPO/tensor networks.
- Adaptive test-time compute, early exit, speculative decoding, KV-cache optimization, architecture search/evolution, and reusable program/skill libraries.

“Represented” means the family is in the archive, not that every named variant or every performance claim had already been documented.

## 2. Add as explicit named entries / variants

### Transformer variants, tokenization and depth/routing
- **NoPE / hybrid NoPE-RoPE** — positional-encoding strategy. Correction: SmolLM3 uses a hybrid strategy that removes RoPE in selected layers, not a blanket claim that it has no positional information at all. [SmolLM3 technical report](https://github.com/huggingface/blog/blob/main/smollm3.md)
- **Multi-Token Prediction (MTP)** — auxiliary prediction heads/training objective and, in some systems, a speculative-style decoding opportunity. Do not assume it generates several accepted tokens per ordinary forward pass or guarantees a multiple-fold CPU speedup.
- **BLT (Byte Latent Transformer)** — byte-level LM with entropy-based dynamic patches; distinct from ordinary subword tokenization. Add **BLT-D, BLT-S and BLT-DV** as 2026 generation extensions: diffusion-assisted multi-byte proposals and speculative/self-verification variants. Sources: [ACL 2025 paper](https://aclanthology.org/2025.acl-long.453/), [Fast BLT, ICML 2026](https://proceedings.mlr.press/v306/kallini26a.html).
- **MegaByte** — hierarchical byte-level Transformer with global patch modeling and local byte generation; related to BLT by byte-level modeling, but not the same architecture.
- **Mixture-of-Depths (MoD)** — token routing across depth; a subset of token positions receives compute in a layer. This is distinct from MoE, which routes tokens among expert modules.
- **Mixture of Recursions (MoR)** — recurrent/shared-layer Transformer with routing over how computation is reused. Keep separate from MoD and Universal Transformer.
- **Universal Transformer / ACT / PonderNet / URM** — recursive depth and adaptive halting are already partly covered in cycle 15; add **URM** as a named recent variant, but track exact paper/version and evaluation separately.
- **TT-Transformer / TT-Rec / TT-LLM** — explicit language-model applications of Tensor-Train factorization. Tensor networks were already covered generically; the claimed reduction from 14–16 GB to 0.8–1.5 GB is not a universal property and needs a source/model configuration.
- **Quiet-STaR** — latent/internal thought generation during training/inference; related to reasoning-time compute, not an external memory architecture.
- **Tree of Thoughts (ToT)** — search/control strategy over candidate reasoning branches, not a base neural architecture. Distinguish it from learned internal reasoning methods and from a claim that it is the exact implementation behind proprietary models.
- **Coconut (Chain of Continuous Thought)** — latent-space reasoning without rendering every intermediate step as text. Add as a named method; benchmark claims must retain their task and setup.
- **Ouro / LoopLM** — recurrently reuses the same language-model weights over latent states, with adaptive/early stopping. Record as a specific architecture family, not evidence that any 1.4B model universally matches 3–4B models.
- **Hyperloop Transformer** — recurrent/looped Transformer with hyper-connections; keep the 2026 model-size comparisons marked as reported, version-specific results.

### Attention and context memory
- **NSA (Native Sparse Attention) / DSA (DeepSeek Sparse Attention)** — add as named sparse-attention methods. Do not merge the two acronyms without noting the paper/model version; retain separate training vs decode speed measurements and quality conditions.
- **MoBA (Mixture of Block Attention)** — content-routed block-sparse attention; public Moonshot code says continued training is required to obtain the intended benefits, so it is not a guaranteed drop-in inference optimization. [Paper](https://arxiv.org/abs/2502.13189), [official code](https://github.com/MoonshotAI/MoBA).
- **Infini-attention** — combines local attention with compressed recurrent memory for long contexts.
- **YOCO (You Only Cache Once)** — decoder architecture designed to reuse/share KV cache between stages; track actual memory and latency separately.
- **cosFormer** — linear-attention variant; Performer was already present, cosFormer was not explicit.
- **AFT (Attention Free Transformer)** — attention-free token interaction variant. Do not state that RWKV directly derives from AFT without a source establishing that lineage.
- **Megalodon / MEGA** — sequence-model families using gated/exponential moving-average mechanisms; add explicitly beside Hyena and other long-sequence alternatives.
- **SubQ / SSA** — retain as **unverified research lead** until an identifiable primary paper, authors, code, and reproducible benchmark are found. Do not repeat “12 million tokens” or “50× FlashAttention” as fact without primary evidence.
- **Graph-RWKV** — retain as **hypothesis/unverified name**, not an established canonical architecture unless a traceable source is supplied.

### Recurrent, hybrid, and alternative blocks
- **RWKV-6 Finch and RWKV-7 Goose** — RWKV was already covered; add version-specific variants and primary sources rather than a duplicate RWKV family.
- **Jamba, Bamba, Mamba-2-Hybrid, Hymba, Zamba/Zamba-2, SAMBA, Hawk/RecurrentGemma** — several hybrid families were already mentioned in broader form; expand the registry with the named configurations and verify each model's layer schedule from its own technical report. Ratios such as 7:1 or 29:3 are configuration-specific, not universal family properties.
- **Gated DeltaNet hybrids** — Gated DeltaNet was already present; explicitly record attention-plus-delta/linear hybrids such as Qwen3-Next and Kimi Linear as model configurations, with exact release/version evidence.
- **Monarch-Mamba / M2-Mamba** — Monarch and Mamba were separately present; the specific hybrid needs a primary source before being treated as an established model.
- **BitSSM / Ternary Mamba** — speculative combination of ternary weights and SSM; keep as an unverified lead until a reproducible paper or implementation is located.
- **MatMul-Free LM / MLGRU** — add as a named recurrent, ternary-weight language-model design, while keeping the claimed “no matrix multiplications at all” scoped to the specific operator implementation.
- **gMLP** — add explicitly as an attention-free gated-MLP sequence architecture.
- **T-LM / Toeplitz Language Models** — add explicitly as structured Toeplitz sequence modeling; distinguish the architecture from generic Toeplitz matrices.
- **Monarch Mixer (M2)** — add as a specific structured-matrix mixer, distinct from generic Monarch matrices.
- **FNO / Fourier Neural Operator** — neural operators were in the taxonomy, but not as a language-model family; record as primarily a scientific/operator-learning method, not a proven text architecture.
- **Quantum-inspired recurrent units** — unverified lead. “Quantum-inspired” does not imply actual quantum computing or proven superior memory capacity; require a concrete paper/model.
- **Neural ODE with memory attractors** — Neural ODE was present; the specific text-memory proposal remains a hypothesis until sourced.
- **LFM / LFM2 (Liquid AI)** — liquid networks were present generically; add the concrete LFM/LFM2 model family and its published model-specific benchmarks.
- **BDH / Dragon Hatchling (Pathway)** — add as a distinct sparse, locally interacting neuron-particle architecture; keep comparisons to GPT-2 restricted to the published training/data/parameter setup.
- **CTM (Continuous Thought Machines, Sakana AI)** — add explicitly as a time/synchronization-based neural architecture; distinguish it from chain-of-thought prompting.
- **SpikingBrain** — add as a spiking/attention/MoE hybrid model line, not as evidence that all SNNs are efficient on ordinary CPUs.
- **SNNs** were already represented generally; the specific SpikingBrain conversion/data-efficiency and sparsity numbers need primary-source verification.

### Memory, test-time adaptation, and reasoning
- **TTT-Layers / TTT-Linear** — test-time-training families were already represented; add these named layer designs and distinguish mutable hidden-state/weights from ordinary KV cache and from external memory.
- **Google Nested Learning / Hope** — add as a framework plus a self-modifying recurrent architecture; Hope is a Titans-related design with multi-timescale/continuum memory, not simply “Titans with more RAM.” [Google Research](https://research.google/blog/introducing-nested-learning-a-new-ml-paradigm-for-continual-learning/).
- **EBT (Energy-Based Transformers)** — add as an energy-scoring/inference family. Keep claims about extra test-time compute and ICLR 2026 status tied to the exact paper and benchmark.
- **Composer** — classify as automated architecture discovery/search, not a single fixed architecture. Track the search space, objective, compute budget, and reproducibility.
- **HRM and TRM** — already mentioned in cycle 15, so do not duplicate; link this list to their existing entries. Their strong ARC/Sudoku/maze results are narrow algorithmic-reasoning evidence, not proof of general-purpose language superiority.
- **TTT, Titans, ATLAS, TNT** — already covered in the memory file; the user's list is useful as a cross-index, not a reason to duplicate them.

### Non-Transformer paradigms / representation systems
- **LCM (Large Concept Model)** — add explicitly beside JEPA; predicts sentence/concept-level representations and is not the same as JEPA.
- **GNN + LLM hybrids** and **neurosymbolic systems** — already represented broadly; cross-index the named combination rather than create duplicate families.
- **MPS / Tree Tensor Networks** — tensor networks were already covered generically; add Tree Tensor Networks explicitly and distinguish tensor-network language models from general tensor factorization.
- **HDC/VSA** — already covered; add the user's binary-vector/bitwise-computation framing as an implementation variant, not a blanket guarantee of “several MB” or exact reasoning.
- **Gzip + kNN / compression-distance classification (NCD)** — add as a non-neural compression-based classification baseline. Do not generalize classification results into open-domain generation or reasoning.
- **SNN** — already covered; keep event-driven energy claims hardware-dependent.
- **Composer** — architecture-search system, not model architecture.
- **“Ternary diffusion”** — classify as a combination of diffusion generation and low-bit/ternary parameterization, not an established unified architecture unless a specific source supports that name.

## 3. Important corrections to the supplied summary

1. **NoPE:** SmolLM3 uses a hybrid NoPE/RoPE schedule; “no positional information at all” is misleading.
2. **MTP:** multi-token prediction as a training objective is not identical to verified multi-token decoding. Actual speed depends on acceptance/verification, architecture, and runtime.
3. **MoD vs MoE vs MoR:** these route different things: token computation across depth, tokens across experts, and recurrent/shared computation, respectively.
4. **Sparse/linear attention:** asymptotic complexity or a paper's GPU benchmark does not prove faster inference on a CPU, especially at short context lengths.
5. **KV cache:** MLA, YOCO, Infini-attention, recurrent state, and external memory reduce different components; their memory figures are not directly comparable without model width, precision, context length, batch size, and cache definition.
6. **HRM/TRM:** benchmark success on structured puzzles is meaningful but not a fair stand-in for open-domain dialogue or language-model quality.
7. **“Top-model quality,” “twice as fast,” “100× faster,” “millions of tokens,” and memory-size claims:** retain only as source-attributed results with model, hardware, task, baseline, and measurement method. Otherwise mark unverified.
8. **Taxonomy classes differ:** base architecture, positional encoding, training objective, decoding/search strategy, memory mechanism, compression/quantization, and architecture-search system must be separate fields, not treated as peer architectures.
9. **No-source leads:** SubQ/SSA, Graph-RWKV, quantum-inspired cells, BitSSM/Ternary Mamba, Monarch-Mamba/M2-Mamba, and Neural-ODE memory attractors remain unverified until a traceable primary source is recorded.

## 4. Source leads checked during this audit

- BLT, ACL 2025: https://aclanthology.org/2025.acl-long.453/
- Fast BLT, ICML 2026: https://proceedings.mlr.press/v306/kallini26a.html
- SmolLM3 architecture notes: https://github.com/huggingface/blog/blob/main/smollm3.md
- MoBA paper: https://arxiv.org/abs/2502.13189
- MoBA official implementation: https://github.com/MoonshotAI/MoBA
- HRM paper: https://arxiv.org/abs/2506.21734
- Nested Learning / Hope: https://research.google/blog/introducing-nested-learning-a-new-ml-paradigm-for-continual-learning/

## 5. Next step under the mandatory taxonomy gate

Update the taxonomy checklist with these named variants, then continue the field-coverage audit for any missing families. Do not begin a full one-by-one architecture deep dive until the map distinguishes architecture, training, inference/search, memory, and hardware axes and records a source or an explicit “unverified” status for each candidate.
