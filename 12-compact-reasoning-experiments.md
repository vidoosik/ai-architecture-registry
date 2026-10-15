# Compact reasoning experiments: results and open tests

This file records project-specific findings from the recent small-model and graph-world dialogues. These are narrow experiments, not proof of general intelligence.

## Tiny Transformer tests

A controlled experiment used vocabulary 36, 23,490 parameters, 2,800 training examples from 350 generated worlds, and 640 independent test examples from 80 held-out worlds. Early held-out-world accuracy was in the low-to-mid 60% range during training.

A later compact test initially had inverted labels; after correcting the labels, the model achieved 100% on 24 unseen-name cases with 19,874 parameters on CPU. This establishes success on that narrow test only—not broad language understanding or genuine multi-hop reasoning.

## Important unresolved issues

- A chain test was found to label examples by relation kind, risking a shortcut instead of requiring every relation in the chain.
- Unknown must be distinct from false; missing fact must not imply negation.
- Contradictory or unreliable sources must remain distinguishable.
- More entities, longer contexts, multiple causes, temporal changes, and novel combinations remain to be tested.
- Compare Transformer-only, graph/rules-only, retrieval-only, and hybrid on identical held-out scenarios.

## Graph-world exception tests

The graph project tests explicit entities, relations, defaults, conditions, and exceptions. A recent exception test passed checks that a default conclusion holds when no exception applies, a conditional exception suppresses it while active, and removing the exceptional fact restores the default. This is useful evidence for the tested default/exception behavior, not proof of robust natural-language reasoning.

## Required next exams

1. Genuine multi-hop questions where no single edge or relation-kind label reveals the answer.
2. Add/remove facts and verify dependent conclusions are recomputed without stale results.
3. Activate/deactivate exceptions and test their exact conditions.
4. Preserve unknown, false, inferred, asserted, and contradicted states separately.
5. Test competing causes; abstain when evidence cannot distinguish them.
6. Hold out entire worlds, names, paraphrases, and relation combinations.
7. Trace every conclusion to premises, rule, context, time, and exceptions.
8. Test natural-language usability, multi-turn continuity, and honest unknown answers.
9. Report accuracy, explanation validity, uncertainty calibration, median/p95 latency, peak RAM, graph size, and growth cost.

## Reporting discipline

Log script/commit, data generation, split, seed, corrected labels, baseline, raw output, and resource measurements. Separate unit-test pass rate, held-out compositional accuracy, natural-language parsing, explanation validity, and uncertainty calibration. Never generalize a 100% result on 24 cases to general reasoning ability.