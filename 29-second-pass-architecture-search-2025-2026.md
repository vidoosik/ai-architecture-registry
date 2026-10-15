# Cycle 29 — Second-pass architecture search: late-2025 and 2026 additions

Date: 2026-10-15

## Scope and result

A second targeted search was run after cycle 28, focusing on 2025–2026 architecture papers, primary conference/publisher pages, and research-group publications. It found additional named variants missing from the explicit checklist. This is a coverage update, not a recommendation or claim that any method is best for the user's CPU-only compact assistant.

## New candidates to index

### Transformer component redesigns
- **DAPE V2 / CDAPE** — processes the attention-score tensor as a feature map and convolves across attention heads, aimed at length extrapolation. This is a positional/attention-score modification, not a replacement for the Transformer. Primary paper: https://aclanthology.org/2025.acl-long.522/
- **SAS (Simulated Attention Score)** — generates additional effective attention patterns from learned projections, decoupling effective head count from parameterized head count. Component-level method, not a new sequence-model family. Paper linked by the authors' technical overview: https://web.stanford.edu/~jksun/blog/llm-architecture.html
- **KERN MoE router** — changes expert routing from the conventional Softmax/exponent kernel to normalized ReLU/cosine-style routing. It is an MoE routing variant, not a new MoE family. See the ICLR 2026 paper linked from the overview above.
- **GeoNorm** — proposes a geodesic residual-update/normalization method. Treat as a layer/optimization component and verify independent reproducibility before inferring broad gains.
- **DAPE V2, SAS, KERN and GeoNorm** were surfaced together by a Stanford research overview. The overview is useful for discovery but is secondary synthesis, not a substitute for each primary paper: https://web.stanford.edu/~jksun/blog/llm-architecture.html

### Hybrid/recurrent generation
- **Decoder-Hybrid-Decoder (DHD) / SambaY** — combines SSM and attention paths, including a gated memory pathway between SSM hidden states. The authors connect this design to Phi-4-mini-Flash-Reasoning. This is a concrete hybrid configuration and should be compared with Jamba/Hymba/Zamba-style hybrids, not merged with them. Source overview and primary-paper link: https://web.stanford.edu/~jksun/blog/llm-architecture.html
- **Latent Recurrent Transformer (LRT)** — adds a recurrent latent pathway using a high-level hidden state from the previous token while retaining standard attention and the KV-cache interface. It is a Transformer augmentation, not a pure RNN or a KV-cache replacement. Microsoft Research (May 2026): https://www.microsoft.com/en-us/research/publication/latent-recurrent-transformer-architecture-exploration-training-strategies-and-scaling-behavior/

### Newer sparse and long-context attention variants
- **Gated Sparse Attention (GSA)** — combines sparse token selection with gates and adaptive sparsity control. Claims such as 12–16× at 128K are paper-specific and require matching hardware/baseline details. https://arxiv.org/abs/2601.15305
- **Hierarchical Sparse Attention (HSA) / HSA-UltraLong** — hierarchical sparse attention for long-context length generalization; the reported 16M context is not evidence that every task can retrieve or reason reliably over 16M tokens. ACL 2026: https://aclanthology.org/2026.acl-long.464/
- **Evolving Sparsity** — dynamically updates token-importance estimates across decode steps and layers rather than using only static importance. ACL 2026: https://aclanthology.org/2026.acl-long.530/
- **RetroAttention** — revises past attention outputs using later-arriving KV entries and a lightweight output cache; a KV-cache/attention approximation method rather than a base architecture. https://arxiv.org/abs/2508.09001
- **BA-Att (Block Approximate Sparse Attention)** — approximate block-sparse attention for diffusion language models and other generators. Keep classified as an attention operator, not a new generation paradigm. https://arxiv.org/abs/2605.19726

### Energy-based and modular expert models
- **NRGPT / eNeRgy-GPT** — an energy-based alternative that frames inference as exploration over token-energy landscapes; evaluated in settings including Shakespeare, ListOPS and OpenWebText. It is a research-stage model, not proof of general superiority to GPT. IBM Research / ICLR 2026: https://research.ibm.com/publications/nrgpt-an-energy-based-alternative-for-gpt
- **MoEP (Modular Expert Paths)** — a modular expert-path sparsity method. Index as an MoE-related routing/parameterization variant; verify model sizes, baselines and reproducibility before adopting performance claims. https://doi.org/10.1016/j.neunet.2026.109617

## New benchmark/source leads, not architectures by themselves
- **Every Token Counts / 16M context** is an evaluation/long-context claim associated with HSA-UltraLong, not a separate architecture.
- **Neural Architecture Search from an NLP perspective** (2026 survey) can be used to discover missing model families, but NAS itself is a search process rather than one architecture: https://link.springer.com/article/10.1007/s10462-026-11550-5
- **Speed Always Wins** (2025 survey) provides a cross-family taxonomy for efficient LLM architectures: https://arxiv.org/abs/2508.09834

## Audit result: can the deep-dive phase begin?

The second pass has found additional named entries, so they were added to the checklist and this cycle. The map is now substantially more complete, but no web search can prove absolute exhaustiveness. The archive already has a mandatory taxonomy gate; it should be considered **ready to begin structured deep dives with a live gap log**, rather than waiting for a mythical final list. If a new family appears during study, add it to the map and then resume.

## Evaluation discipline

For each candidate, keep separate fields for:
1. Base architecture/family.
2. Component change (position, attention, routing, normalization, residual path).
3. Training objective and data recipe.
4. Inference/search/decode method.
5. Memory/cache representation.
6. Quantization/runtime/hardware.
7. Published quality, context length, latency and memory—with exact model, task, baseline, hardware, and source.
8. Evidence status: established family, paper-reported result, independently reproduced, hypothesis/unverified.

Do not equate theoretical complexity with real CPU latency; do not treat an author's benchmark as a universal guarantee. The next phase should study one architecture at a time with a consistent template and mark the user's hardware/resource constraints separately from general scientific merit.
