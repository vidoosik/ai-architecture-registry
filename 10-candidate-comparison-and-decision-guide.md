# Compact architecture comparison and decision guide

This is a decision aid, not a leaderboard. “Potential fit” is a hypothesis under the stated CPU/RAM constraints; it is not a claim that the method has already passed the target benchmarks.

## High-level comparison

| Family | Strength worth testing | Main limitation/risk | CPU-first role to test |
|---|---|---|---|
| Small quantized Transformer | Flexible language, broad pretrained ecosystem | Weight bandwidth, decode latency, KV cache | Baseline language/generation core |
| RWKV / RetNet / SSM / Mamba-family | Recurrent or linear-time sequence processing | Quality and CPU kernel maturity vary; sequential state updates | Alternative language core, especially long context |
| Gated linear attention / Delta-rule fast weights | Compact sequence state and updatable associations | Memory interference, implementation and quality uncertainty | Short-term associative memory / sequence core |
| Retrieval + external documents | Large durable knowledge outside RAM | Retrieval misses, stale facts, weak multi-hop reasoning alone | Disk-backed knowledge access |
| Typed knowledge graph + rules | Inspectable relations, exact derivations, exceptions | Parsing brittleness and rule coverage | Explicit factual state and verifiable inference |
| Neuro-symbolic hybrid | Combine language flexibility with exact state/rules | Interface between neural parsing and symbolic semantics | Leading system-level hypothesis |
| VSA / hyperdimensional computing | Compact binding/bundling operations | Approximate decoding and capacity limits | Experimental structured memory / feature binding |
| Hopfield / associative memory | Content-addressable retrieval | False matches, interference, retrieval compute | Associative recall component |
| JEPA / world models | Predictive latent state and planning | Training/data demands; not a standalone language solution | State prediction/planning submodule |
| EBM / DEQ / predictive coding | Iterative inference or energy-based compatibility | Solver/sampling cost and convergence | Specialized verifier or candidate-reranker |
| Tensor networks / structured matrices | Reduce parameter count or constrain transforms | Rank/quality trade-offs and kernel overhead | Compression experiments within a model |
| MoE / specialist routing | Conditional use of specialized capacity | Routing overhead; experts may cause memory traffic | Only if active experts fit memory and routing pays off |
| Diffusion / flow / GAN | Strong generation in appropriate modalities | Iterative sampling or training complexity | Image/audio/media subsystem, not initial text core |
| Tool-using agent | Can search, execute code, and verify outputs | Tool latency and orchestration failures | Essential outer control layer for code verification |

## Recommended experimental order

### Stage A — establish baselines
1. Pick one small quantized pretrained Transformer with a known CPU runtime.
2. Fix prompt/task sets, context lengths, thread counts, and RAM measurement.
3. Measure cold load, prefill, decode, peak RAM, and quality.
4. Add a disk-backed retrieval baseline and a typed graph/rule baseline on identical questions.

### Stage B — test the hybrid hypothesis
1. Use a language model to parse/express natural language.
2. Store explicit facts with entity IDs, relation types, context, time, source, confidence, and status.
3. Use a rule engine only for derivations whose premises can be shown.
4. Keep unknown, contradicted, inferred, and directly asserted states distinct.
5. Make the language model explain results using the trace rather than inventing hidden premises.
6. Compare with the language model alone and retrieval-only baseline.

### Stage C — test sequence/memory alternatives
Compare one or two realistic CPU-ready candidates (e.g. RWKV/SSM/linear-attention) with the Transformer baseline. Test the same quality target, quantization, and prompts. Add fast-weight/test-time-memory methods only when a usable implementation and fair comparison exist.

### Stage D — specialized capabilities
- **Code:** generate → execute → test → inspect error → revise.
- **Uncertainty:** unknown facts, conflicting sources, stale facts, and explicit abstention.
- **Humor:** ambiguity, context shifts, incongruity, linguistic/cultural dependencies.
- **Creativity:** constrained novelty, analogy, concept blending, consistency across revisions.
- **Continual learning:** add new facts/tasks, then re-test old ones for forgetting.

## Required unseen-scenario test design

Hold out entire worlds/scenarios, not just shuffled examples. Include:
- unseen entity names and paraphrased relation descriptions;
- new combinations of known relations;
- exceptions that suppress defaults only under their stated conditions;
- removal of an exception and restoration of the default;
- competing explanations with insufficient evidence to choose;
- temporal changes and stale/conflicting sources;
- counterfactual questions;
- novel code requirements with executable tests;
- humor/creative tasks scored independently from factual QA.

Report both answer accuracy and failure type: false conclusion, missed conclusion, unjustified certainty, invalid explanation, contradiction collapse, retrieval miss, or language-generation failure.

## Practical architecture hypothesis

The first system worth testing is not “graph instead of neural network.” It is a **hybrid**:
- small pretrained language model for robust parsing, dialogue, paraphrase, and generation;
- typed sparse graph/database for durable facts, time, provenance, exceptions, and explicit unknown/conflict states;
- deterministic rule/procedure layer for auditable inference;
- retrieval for documents and specialist knowledge;
- tool execution and tests for code;
- evaluator/critic for checking claims and uncertainty.

The biggest unresolved risk is language and creativity quality at the target model size. Explicit memory can improve factual control but cannot be assumed to replace a learned language model.

## Evidence and decision policy

Do not choose a candidate based on theoretical FLOPs, parameter count, or a headline speedup alone. Require:
1. a published or independently reproducible quality result;
2. exact hardware and implementation details;
3. end-to-end measurements on target CPU;
4. generalization tests beyond training templates;
5. peak memory and storage costs;
6. known failure modes and maintenance complexity.

For each experiment, log: hypothesis, baseline, changed variable, data split, command/configuration, hardware/software, results, failures, and decision (keep/drop/retest).