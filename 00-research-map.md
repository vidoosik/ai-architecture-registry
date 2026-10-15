# Research map and evaluation protocol

## Goal

Investigate a compact intellectual assistant, not merely a calculator, lookup system, or collection of hand-written if/else rules. The intended system should combine learned language competence with explicit, inspectable state and reasoning, while staying useful on CPU-only hardware.

## Target capabilities

1. Dialogue and grounded facts: distinguish known, inferred, conflicting, and unknown information.
2. Compositional reasoning: combine relations and rules in new held-out scenarios; explain why a conclusion follows.
3. Memory and revision: preserve episodes, update beliefs, represent exceptions, and avoid catastrophic forgetting.
4. Coding: propose code, run it in a controlled environment, inspect results, and revise based on tests.
5. Humor and creativity: handle ambiguity, context shifts, analogies, and novel combinations rather than only retrieve templates.
6. Multilingual understanding: shared concepts across languages, not merely independent token IDs.

## Constraints and design targets

- CPU-first; no discrete GPU required for the baseline.
- Core target 2–4 GB, ideally 1–2 GB RAM; hard ceiling around 4 GB if justified.
- External knowledge on SSD: roughly 10–100 GB.
- Latency and usability gates must be explicit. A system is not “ready to chat” merely because unit tests pass.
- These are goals, not achieved benchmarks.

## Main architecture families to compare

- Dense language models: decoder Transformers and compact distilled/quantized variants.
- Recurrent/linear-time sequence models: RWKV, RetNet, SSMs, Mamba-family, gated linear attention, Delta-rule models.
- External/associative memory: retrieval, kNN, fast weights, modern Hopfield, Titans/MIRAS/ATLAS-style test-time memory.
- Explicit symbolic and graph reasoning: typed entities, relations, rules, contexts, temporal facts, provenance, exceptions, uncertainty.
- Neuro-symbolic hybrids: learned perception/language linked to explicit reasoning and tools.
- Predictive/world models and energy-based alternatives.
- Conditional compute and specialist routing.
- Verified tool-using agents.

## Evaluation matrix

For every candidate, record:
- Model size and peak resident RAM.
- Disk footprint and startup/load time.
- CPU tokens/s or task latency, single-thread and multi-thread where relevant.
- Quality on standard benchmarks and on a project-specific held-out suite.
- Generalization to unseen entity names, relation compositions, exceptions, counterfactuals, and paraphrases.
- Ability to state “unknown” and preserve contradictory evidence.
- Incremental learning cost and forgetting after updates.
- Energy/power if measurable.
- Implementation complexity and availability of a CPU-friendly reference implementation.

## Required experimental hygiene

1. Define the baseline and freeze test data before tuning.
2. Separate training, validation, and held-out worlds/scenarios.
3. Change one major factor at a time.
4. Report median and tail latency, not a single best run.
5. Record exact hardware, OS, compiler, runtime, quantization, context length, batch, and command.
6. Distinguish measured values, source-reported values, estimates, and hypotheses.
7. Keep a failure log. Passing a small synthetic test is evidence of a narrow capability, not proof of general intelligence.

## Promising hybrid hypothesis

A compact language model could handle parsing, paraphrase, and flexible generation; a sparse, typed memory/graph could store facts, episodes, confidence, time, and provenance; a small rule/procedure layer could derive inspectable consequences; retrieval could load specialist knowledge on demand; a verifier could test claims and code. The unresolved question is whether this hybrid can reach adequate open-ended dialogue and creativity without a stronger learned generative core.

## Priority experiments

1. Compare a small quantized language model against a hybrid graph + retrieval baseline on the same tasks.
2. Test compositional generalization on unseen graph structures, not just known triples.
3. Measure whether fast-weight/linear-memory models improve long-context retention on CPU at a quality level worth their complexity.
4. Add explicit unknown/conflict/provenance semantics and test calibration.
5. Test code generation with actual execution and unit-test feedback.
6. Test humor and creative tasks separately; do not infer these abilities from factual QA scores.