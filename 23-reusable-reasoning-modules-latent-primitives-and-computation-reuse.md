# Reusable reasoning modules, latent primitives, and computation reuse (2026)

This pass studies reuse of operations and skills, rather than just retrieval of facts or old answers. The methods below are related but not equivalent.

## 1. AlgoTrace: latent algorithmic primitives

**AlgoTrace: Algorithmic Primitives and Compositional Geometry of Reasoning in Language Models** (ICML 2026)  
https://proceedings.mlr.press/v306/lippl26a.html

The authors cluster latent activations from models solving TSP, 3SAT, AIME, and graph-navigation tasks, associate clusters with reasoning operations, and extract function-vector-like primitives. Experiments report that injecting primitives can elicit associated operations, steer behavior across tasks, and compose algebraically; reasoning fine-tuning improves composition in tested models.

This is evidence about latent control and compositional structure, not proof that the vectors are exact standalone algorithms, portable across arbitrary architectures, or a way to reduce wall-clock inference time.

## 2. ReX: shared latent experience bank

**Reusable Experiences: Latent Routing and Modular Composition in LLMs** (ACL 2026)  
https://aclanthology.org/2026.acl-long.1388/

ReX learns a shared bank of skill vectors, encodes each input into a compact experience code, then routes and composes skills into a lightweight adapter without an explicit task ID. The paper reports improvements over task-specific fine-tuning on its multi-task NLP benchmarks.

ReX reuses parameterized skills, not just text examples or cached answers. Costs include bank storage, routing, and constructing/applying the adapter. The NLP results do not establish performance on arbitrary relational reasoning or CPU-only hardware.

## 3. Discover modules from compound reasoning traces

**From Reasoning Traces to Reusable Modules: Understanding Compositional Generalization in Language Model Reasoning** (ICML 2026)  
https://proceedings.mlr.press/v306/kong26c.html

The paper models reasoning traces as hierarchical selections over atomic skills and routing decisions. Experiments investigate how reinforcement learning can identify reusable structure in compound traces and recombine it into new configurations. The authors report that training on compound traces can generalize better than training on isolated modules. They propose SFT for broad component coverage and RL exploration for novel compositions.

This is a training recipe for discovering skills, not inference-time memoization; its training cost must be counted.

## 4. UniR: composable plug-in reasoning modules

**Universal Reasoner: A Single, Composable Plug-and-Play Reasoner for Frozen LLMs** (ICML 2026)  
https://proceedings.mlr.press/v306/kim26f.html  
Code: https://github.com/hangeol/UniR

UniR trains a reasoning module separately using verifiable rewards and combines its output logits with a frozen backbone. Multiple modules can be composed by adding logits. The paper reports experiments in mathematical reasoning and machine translation, including transfer in tested settings.

This separates reusable reasoning components from the base model's weights, but does not eliminate backbone computation. Modularity is not automatically a speedup; modules may also conflict.

## 5. Primitives plus a composition grammar

**A Compositional Framework for Open-ended Intelligence** (Microsoft Research, June 2026)  
https://www.microsoft.com/en-us/research/publication/a-compositional-framework-for-open-ended-intelligence/

The framework separates representational/algorithmic primitives from composition operators for selection, sequencing, recursion, and branching. It proposes next-primitive prediction to learn reusable operations and a grammar for combining them. This is a conceptual framework with case studies, not proof that any finite primitive set yields general intelligence.

## 6. Modularity is not new

**Design and independent training of composable and reusable neural modules** (Neural Networks, 2021)  
https://www.sciencedirect.com/science/article/pii/S0893608021001222

This work explores role-specific modules that can be trained independently and composed. It provides historical context: the newer 2026 work adds latent primitives, learned experience routing, RL-driven module discovery, and plug-in reasoning guidance, rather than inventing modularity itself.

## 7. Four meanings of reuse

1. **Retrieve a fact or episode:** downstream reasoning may still be recomputed.
2. **Reuse a learned skill:** select a module, adapter, rule, or latent primitive to perform an operation again.
3. **Reuse a partial result:** retrieve a prior subgoal answer, proof state, or activation and skip computation.
4. **Replay a whole solution or trace:** only safe when its preconditions and validity still hold.

This pass primarily supports category 2. It does not provide a universal method for category 3.

## 8. Requirements for real computation reuse

A correct result cache needs a canonical representation of the subproblem, dependencies on facts and assumptions, version/context tracking, invalidation when dependencies change, provenance, confidence, and a fallback for ambiguous matches. Lookup and validation must cost less than recomputation.

Similarity alone is insufficient. A cached inference can become invalid when an exception becomes active; a cached proof can fail when a premise changes. Reuse should be tested against new entities, paraphrases, new combinations, changed conditions, contradictions, exceptions, and corrected facts.

## 9. Compact CPU-first evaluation

Compare explicit reusable procedures, a learned skill bank with router, latent primitives from a neural model, dependency-aware result memoization, and a no-reuse baseline. Measure correctness, false-reuse rate, cache hit rate, wall-clock latency, peak RAM, disk use, and invalidation cost. A high hit rate is not useful if stale results silently cause errors.

## Sources

- AlgoTrace (ICML 2026): https://proceedings.mlr.press/v306/lippl26a.html
- ReX (ACL 2026): https://aclanthology.org/2026.acl-long.1388/
- From Reasoning Traces to Reusable Modules (ICML 2026): https://proceedings.mlr.press/v306/kong26c.html
- Universal Reasoner / UniR (ICML 2026): https://proceedings.mlr.press/v306/kim26f.html
- A Compositional Framework for Open-ended Intelligence (Microsoft Research, 2026): https://www.microsoft.com/en-us/research/publication/a-compositional-framework-for-open-ended-intelligence/
- Design and independent training of composable and reusable neural modules (2021): https://www.sciencedirect.com/science/article/pii/S0893608021001222
