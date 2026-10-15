# Hybrid architecture hypotheses and test plan

## Main system-level hypothesis

A practical candidate to test is a hybrid rather than a categorical choice between neural and graph systems:

1. A small language model for natural-language parsing, paraphrase, dialogue, and fluent generation.
2. A persistent typed fact/event graph for entities, relations, conditions, time, source/provenance, confidence, and status.
3. A rule/inference engine for derivations whose premises can be exposed.
4. An exception/default manager that handles conditional overrides and recomputes conclusions when facts change.
5. Disk-backed retrieval for documents and specialist knowledge.
6. Tool execution for code/tests and external operations.
7. A verifier that checks claims against graph traces, sources, and tool outputs and reports unknowns/conflicts.

This must be compared against a small neural model alone, graph/rules alone, and retrieval-only baselines. It is not proven to match a top-tier model.

## Context-semantic graph direction

The proposed representation emphasizes entity identity; a small active set of context-relevant properties; typed relations/interactions; context/time; conditional rules; competing causes and uncertainty; and history/provenance. Examples discussed include a person and their role in a political context, a surfboard's physical properties versus interactions with water and a surfer, and a cat whose default sleeping place changes due to heat, noise, smell, food, or owner-related conditions.

The system should not select among competing causes without evidence. It should preserve the history and weaken or retract conclusions when new evidence arrives, rather than erase old information silently.

## Event-triggered liquid-network hypothesis

An earlier concept paired a persistent graph with an event-triggered Liquid Neural Network and a small language model, aiming to activate only relevant knowledge/state branches instead of repeatedly processing all weights.

Earlier speed ranges (graph/liquid ~30–100 tokens/s; compact 20–50M model ~100–300 tokens/s; graph+liquid ~50–150 tokens/s, possibly 200+) were **unmeasured estimates**. They must not be cited as achieved performance. “Maximum intelligence per CPU cycle” is an optimization objective, not a benchmark result.

## Fair comparison

Use identical held-out scenarios for:
- small Transformer only;
- graph/rules only;
- retrieval only;
- Transformer + graph/rules;
- optionally a recurrent/SSM or event-triggered liquid component.

Measure unseen-entity and compositional accuracy, genuine multi-hop reasoning, exceptions and counterfactual updates, unknown/false/conflict distinctions, explanation validity, language robustness, multi-turn continuity, median/p95 latency, peak RAM, model/graph/index size, disk I/O, and CPU thread/power conditions.

## Selection rule

Do not select graph/hybrid only because it uses fewer operations or appears interpretable. It earns its place if it improves correctness, uncertainty handling, updateability, or resource use on held-out tests. Likewise, a pretrained Transformer should be judged by actual target-hardware quality and resource cost, not checkpoint availability alone.

Reject or redesign tests if labels leak the answer, a relation-kind shortcut solves a supposed multi-hop task, held-out cases duplicate training templates, explanations are written after the fact without tracing premises, or unknown is confused with false.