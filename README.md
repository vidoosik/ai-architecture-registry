# AI Architecture Registry

A living research archive for compact, CPU-first intelligent systems. GitHub is the canonical archive; Notion is the easier-to-read companion.

## Project objective

Explore architectures that could support grounded dialogue, compositional reasoning, explicit uncertainty and contradiction handling, memory, coding with execution/verification, humor, and creative generation under tight CPU/RAM constraints.

**Target envelope:** CPU-only inference as the baseline; core roughly 2–4 GB, ideally 1–2 GB RAM; external knowledge/storage roughly 10–100 GB. These are design goals, not achieved results.

## How to read this archive

- [00 — Research map and evaluation rules](00-research-map.md)
- [01 — Transformer and attention families](01-transformers-attention.md)
- [02 — Recurrent, state-space, and linear-time models](02-recurrent-ssm-linear.md)
- [03 — Memory, fast weights, and test-time learning](03-memory-fast-weights.md)
- [04 — Symbolic, graph, neuro-symbolic, and structured reasoning](04-symbolic-graph-reasoning.md)
- [05 — Learning, world models, energy, and alternative computation](05-learning-world-models-energy.md)
- [06 — Generation, multimodal, agents, and planning](06-generation-multimodal-agents.md)
- [07 — Compression, inference optimization, and hardware](07-compression-inference-hardware.md)
- [08 — Source index and research leads](08-sources.md)
- [09 — Overlooked architectures and research leads](09-overlooked-architectures-and-research-leads.md)
- [10 — Candidate comparison and decision guide](10-candidate-comparison-and-decision-guide.md)
- [11 — Taxonomy completion checklist](11-taxonomy-completion-checklist.md)

- [12 — Compact reasoning experiments and graph tests](12-compact-reasoning-experiments.md)
- [13 — CPU/GPU backends and measured optimization](13-cpu-gpu-backends-and-measured-optimization.md)
- [14 — Hybrid architecture hypotheses and test plan](14-hybrid-architecture-hypotheses-and-test-plan.md)
- [15 — New research leads: algorithmic reasoning and adaptive computation](15-new-research-leads-algorithmic-reasoning-and-adaptive-compute.md)
- [16 — Further architecture leads: diffusion, reservoirs, spiking, adaptive compute](16-further-architecture-leads-diffusion-reservoir-spiking-adaptive-compute.md)
- [17 — Equilibrium models, associative memory, VSA, and active inference](17-equilibrium-associative-memory-vsa-active-inference.md)

- [18 — Formal reasoning, hypernetworks, meta-learning, sparse memory, and local learning](18-formal-reasoning-hypernetworks-meta-learning-sdm-local-learning.md)
- [19 — Continual learning, adaptive memory, test-time training, architecture evolution, and continuous-depth models](19-continual-learning-adaptive-memory-test-time-training-nas-continuous-depth.md)
- [20 — Continuous-time attention, diffusion language generation, and local-credit learning](20-continuous-attention-diffusion-language-local-credit-learning.md)
- [21 — Depth-progressive learning, HKAN, and typed program synthesis](21-depth-progressive-learning-hkan-typed-program-synthesis.md)
- [22 — Memory consolidation, retrieval experience, and computation reuse](22-memory-consolidation-retrieval-experience-and-computation-reuse.md)
- [23 — Reusable reasoning modules, latent primitives, and computation reuse](23-reusable-reasoning-modules-latent-primitives-and-computation-reuse.md)
- [24 — Automatic procedure discovery, program synthesis, proof-state reuse, and safe recomputation](24-automatic-procedure-discovery-program-synthesis-and-safe-reuse.md)

## Evidence labels

- **Established:** known architecture or technique with published work.
- **Reported result:** a result claimed in a paper or official project source; always preserve its scope (training vs inference, benchmark vs general claim).
- **Hypothesis:** plausible design idea that still needs an ablation or controlled experiment.
- **Not equivalent:** techniques that sound related but solve different problems.

## Research discipline

Do not equate fewer FLOPs with faster CPU inference; memory traffic, cache locality, SIMD, branching, model quality, and implementation overhead matter. Do not equate training speedups with inference speedups. Evaluate on held-out compositions and scenarios, not only training examples. Track latency, peak RAM, disk use, quality, uncertainty calibration, and failure modes.

## Archive scope

This is a consolidated research archive based on the research notes available in the conversation context. It is broader than the initial taxonomy, but it is not a guarantee that every line from every historical conversation has been recovered. Use the completion checklist to track source-backed coverage and add future discoveries without silently replacing earlier findings.

Last expanded: 2026-10-15.