# Source index and research leads

This list records starting points for continued verification. Prefer original papers, official proceedings, and author/institution pages over secondary summaries. A link is a research lead, not an endorsement of every claim in a paper.

## Test-time memory and fast weights

- Titans, “Learning to Memorize at Test Time” (2025): https://arxiv.org/abs/2501.00663
- Google Research, Titans + MIRAS overview: https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/
- ATLAS, “Learning to Optimally Memorize the Context at Test Time” (ICML 2026): https://proceedings.mlr.press/v306/behrouz26b.html
- TNT, “Improving Chunkwise Training for Test-Time Memorization” (ICLR 2026): https://proceedings.iclr.cc/paper_files/paper/2026/hash/bdb30687f1c2255c29b11b0b45204ebe-Abstract-Conference.html
- Memorizing Transformer (2022): https://arxiv.org/abs/2203.08913
- Wang, Shi & Fox, “Test-time regression: a unifying framework for designing sequence models with associative memory” (JMLR 2026; preprint 2025): https://jmlr.org/beta/papers/v27/25-0903.html and https://arxiv.org/abs/2501.12352
- Nguyen et al., “Push, Pop, Parallelize: Stack-Augmented Linear Attention via the Delta Rule” (DeltaStack, ICML 2026): https://proceedings.mlr.press/v306/nguyen26i.html

## Knowledge graphs and temporal reasoning

- Temporal Knowledge Graph Reasoning survey, IEEE 2025: https://doi.org/10.1109/DSC67331.2025.00019
- Temporal knowledge graph embedding survey, Knowledge-Based Systems (2024): https://doi.org/10.1016/j.knosys.2024.112454
- Spatio-temporal knowledge graphs survey, Neurocomputing (2025): source details should be checked against the publisher record before quoting claims.

## Neuro-symbolic and lifelong learning

- “A survey on neural-symbolic learning systems,” Neural Networks (2023): https://doi.org/10.1016/j.neunet.2023.06.028
- “Towards Lifelong Learning of Large Language Models: A Survey,” ACM Computing Surveys (2025): https://doi.org/10.1145/3716629
- “A Comprehensive Survey of Continual Learning,” IEEE TPAMI (2024): https://doi.org/10.1109/TPAMI.2024.3367329
- “Continual Learning with Knowledge Distillation: A Survey,” IEEE TNNLS (2025): search the official publisher record before using specific claims.
- “Predictive Coding Networks and Inference Learning: Tutorial and Survey,” ACM Computing Surveys (2026): https://doi.org/10.1145/3797870

## Alternative learning, dynamics, and self-organization

- Liu et al., “KAN: Kolmogorov-Arnold Networks” (2024): https://arxiv.org/abs/2404.19756
- Hasani et al., “Liquid Time-constant Networks” (AAAI 2021): https://doi.org/10.1609/aaai.v35i9.16936; preprint https://arxiv.org/abs/2006.04439
- Mordvintsev et al., “Growing Neural Cellular Automata” (Distill 2020): https://doi.org/10.23915/distill.00023
- Kaiser & Sutskever, “Neural GPUs Learn Algorithms” (2015): https://arxiv.org/abs/1511.08228

## Storage and learned indexes

- Kraska et al., “The Case for Learned Index Structures,” Google Research / SIGMOD 2018: https://research.google/pubs/the-case-for-learned-index-structures/

## Further primary-source search targets

- Original papers and official implementations for Mamba/Mamba-2, RWKV, RetNet, S4/S5, xLSTM, Gated Linear Attention, Gated DeltaNet, Hyena.
- Original papers for Hopfield networks, modern Hopfield networks, VSA/Hyperdimensional Computing, Tensor Product Representations, Neural Turing Machines, Differentiable Neural Computers.
- Original papers for JEPA, predictive coding, Deep Equilibrium Models, tensor networks, Monarch/Butterfly structured matrices, neural operators, equivariant networks, and capsule networks.
- Official documentation for llama.cpp/GGUF, CPU SIMD kernels, Vulkan/DirectML/Metal backends, and relevant compiler/runtime versions.

## Citation discipline

For each future entry, record title, authors, year, venue, DOI/arXiv/official URL, architecture category, claimed result, exact benchmark/hardware, limitations, and whether the number concerns training, inference, memory, or accuracy. If a source has not been verified, label it “to verify” rather than inventing exact bibliographic details.