# 24 — Automatic procedure discovery, program synthesis, proof-state reuse, and safe recomputation

Research cycle: 2026-10-15

## Executive result

The strongest new design lesson is to separate **learning a reusable procedure** from **reusing a previous computation result**. The first can improve transfer and reduce search; the second can save runtime only when the current subproblem is equivalent to the cached one and its dependencies remain valid.

No single source found here solves all four goals at once: discover abstractions autonomously, compose them on unseen tasks, reuse intermediate results safely, and run cheaply on a small CPU-only system. Treat these as separable mechanisms to test.

## 1. DreamCoder: learn a library while solving tasks

**DreamCoder: Bootstrapping Inductive Program Synthesis with Wake-Sleep Library Learning** (PLDI 2021)  
https://doi.org/10.1145/3453483.3454080  
Paper: https://people.csail.mit.edu/asolar/papers/EllisWNSMHCST21.pdf

DreamCoder solves a corpus of synthesis problems, identifies recurring program fragments, abstracts them into a growing library, and trains a neural recognition/search policy to guide later synthesis. Its wake-sleep loop alternates solving tasks and improving the language/search process. The paper covers multiple domains including program synthesis, planning, inverse graphics, and equation discovery.

**Relevance:** a plausible route to an expanding set of explicit operations. New operations arise from recurring structure in successful solutions rather than being entirely hand-authored.

**Limits:** the system needs a formal problem representation, a DSL/primitives, search, and a training corpus. A useful abstraction for one DSL may not transfer to arbitrary everyday language. Learning the library is not free and does not automatically make each inference cheap.

Source: https://people.csail.mit.edu/asolar/papers/EllisWNSMHCST21.pdf

## 2. Stitch: reduce the cost of discovering abstractions

**Top-Down Synthesis for Library Learning**  
https://arxiv.org/abs/2211.16605

Stitch discovers abstractions shared across a corpus of programs in a DSL using top-down synthesis and syntactic pattern matching to prune the search. The authors report 3–4 orders of magnitude faster execution and about two orders of magnitude lower memory use than the compared deductive DreamCoder library-learning method, with comparable or better library quality by their compressivity measure.

**Interpretation:** the discovery algorithm itself matters enormously. Structural matching and search pruning can be more useful for a constrained system than applying a much larger neural model to every step.

**Limit:** the comparison concerns library-learning cost and a particular corpus/DSL; it is not a general 1,000–10,000x speedup for reasoning or inference. Syntactic commonality can miss semantically equivalent but differently written procedures.

## 3. LAPS and Bayesian program learning: use language to guide search

**Leveraging Language to Learn Program Abstractions and Search Heuristics** (ICML 2021)  
https://proceedings.mlr.press/v139/wong21a.html

LAPS combines natural-language annotations with learned program abstractions and a neural search heuristic. The reported gains cover library quality, search efficiency, and generalization on string editing, image composition, and abstract scene reasoning; language hints are not required at test time in the reported setup.

**Relevance:** language can help a system discover useful procedure names and guide synthesis, while the executable procedure library remains explicit and compositional.

**Limit:** the results are in bounded synthesis domains, not open-ended conversational reasoning.

## 4. SkillGen: derive and test agent skills from successes and failures

**SkillGen: Verified Inference-Time Agent Skill Synthesis** (Microsoft Research, May 2026)  
https://www.microsoft.com/en-us/research/publication/skillgen-verified-inference-time-agent-skill-synthesis/

SkillGen contrasts successful and failed agent trajectories to propose human-readable skills, then tests the intervention with and without the skill on the same instances. This explicitly measures both repairs (previous failures now succeed) and regressions (previous successes now fail). The paper reports improvements on held-out tasks and transfer across tested models and datasets.

**Relevance:** a candidate skill should not be accepted merely because it summarizes a successful trace. It should be tested as a change to system behavior, including regressions.

**Limit:** skills are behavioral guidance, not necessarily verified executable algorithms; validation on the paper's agent benchmarks does not guarantee correctness in other domains.

## 5. SkillRL: recursive, hierarchical skill-bank evolution

**SkillRL: Evolving Agents via Recursive Skill-Augmented Reinforcement Learning** (2026)  
Paper: https://arxiv.org/abs/2602.08234  
Code: https://github.com/aiming-lab/SkillRL

SkillRL distills successful and failed trajectories into hierarchical general and task-specific skills, retrieves them adaptively, and evolves the skill bank based on validation failures during reinforcement learning. The project reports a 10–20% token-footprint reduction compared with storing raw trajectories, plus benchmark improvements on ALFWorld, WebShop, and search-augmented tasks.

**Relevance:** a growing library needs feedback and revision, not just append-only memory. Failed attempts can indicate a missing precondition, an exception, or a skill that should be narrowed.

**Limits:** benchmark and token-footprint results are scoped to this LLM-agent setup; they do not establish CPU latency savings or low-RAM suitability. The training pipeline and base model costs still count.

## 6. Proof-state snapshots: reuse an expensive intermediate state

**Keep the Proof State Live: Snapshotting for Efficient Tactic Search in Lean 4** (2026)  
https://arxiv.org/abs/2605.25556

The authors identify repeated reconstruction of elaborated proof states as a major cost in branching tactic search. Their snapshotting method builds the state once and reuses it across branches. On 48 miniF2F-v2 problems, they report 5.6–50x wall-time speedups over the standard fallback (average 14x, median 9.7x). These results are for Lean 4 proof search, not general reasoning.

**Key lesson:** sometimes the most effective optimization is not to invent a faster reasoning algorithm but to preserve and reuse a valid expensive intermediate state instead of rebuilding it for every branch.

**Important boundary:** a snapshot is reusable within a compatible execution context. It should not be treated as a reusable answer after its assumptions, environment, definitions, or dependencies have changed.

## 7. LeanSearch v2 and LeanHammer: retrieve reusable proof knowledge

**LeanSearch v2: Global Premise Retrieval for Lean 4 Theorem Proving** (May 2026)  
https://arxiv.org/abs/2605.13137

LeanSearch v2 targets the set of lemmas needed for an entire theorem rather than only selecting one premise per tactic step. On a 69-query research-level Mathlib benchmark, its reasoning mode recovered 46.1% of ground-truth premise groups within 10 candidates, compared with 38.0% for the next-best reasoning retrieval system and 9.3% for premise-selection baselines in the reported comparison. In a fixed downstream prover loop, reported proof success was 20%, versus 16% for the next-best retriever and 4% without retrieval.

**Premise Selection for a Lean Hammer** (2025)  
https://arxiv.org/abs/2506.07477

LeanHammer combines neural premise selection with symbolic proof search and proof reconstruction. The authors report solving 21% more goals than prior premise selectors in their evaluation.

**Relevance:** reuse can mean finding an established lemma/procedure that applies, not copying a previous natural-language answer. A formal checker can validate the resulting proof.

**Limits:** theorem proving has explicit types, definitions, and proof obligations. Its correctness guarantees do not transfer automatically to informal commonsense reasoning. Retrieval alone is not proof; selected premises still need to be used correctly and checked.

## 8. Dependency-aware reuse: a design requirement, not a solved general AI architecture

A result cache should be indexed by a canonicalized subproblem and carry at least:

- the result or proof object;
- explicit preconditions and assumptions;
- dependencies on facts, rules, definitions, and environment versions;
- provenance and confidence/status;
- a validity check and invalidation path;
- a fallback to recomputation when equivalence is uncertain.

When a source fact changes, invalidate only cached results that depend on it, directly or transitively. Unaffected results can remain. This resembles incremental computation and build systems, but its use for open-ended reasoning requires a correct dependency model; it should not be presented as a new neural result.

A similarity match is only a retrieval hint, not proof that two subproblems are equivalent. In the graph-world setting, changing a context or adding an exception must invalidate only conclusions whose derivations rely on the changed condition. The system must not reuse a stale conclusion merely because the surface wording is similar.

## 9. Four distinct levels of reuse

1. **Fact/episode retrieval:** retrieve stored information; reasoning may still be repeated.
2. **Procedure/skill reuse:** invoke a learned rule, program, tactic, or policy.
3. **Intermediate-state/result reuse:** skip work by reusing a previous subgoal result or live state under matching dependencies.
4. **Whole-trace replay:** repeat a previous sequence only when its preconditions and current environment are still valid.

These levels have different correctness and performance requirements. Do not count skill retrieval as saved inference compute unless the actual execution cost is measured.

## 10. Training without a huge global backpropagation loop

The sources support several different routes, none universally best:

- **Symbolic library learning:** abstract repeated code/solution fragments and guide search; training is largely search, refactoring, and fitting a search policy.
- **Contrastive success/failure analysis:** propose a skill, then test its intervention and regressions (SkillGen).
- **RL-driven skill-bank evolution:** update skills and routing using outcomes and validation failures (SkillRL); training cost remains material.
- **Formal feedback:** use executable checks, type checking, proof checking, or tests to reject invalid synthesized procedures.
- **Cached intermediate states:** save repeated execution work without retraining the underlying model.

These can be combined, but must be evaluated separately. A no-global-backprop method can still require expensive search, repeated rollouts, or a large teacher model.

## 11. Compact CPU-first experiment proposal

Compare on the same held-out tasks:

A. baseline reasoning with no reuse;  
B. stored episodes/facts only;  
C. explicit reusable procedures with preconditions;  
D. automatic procedure discovery from solved traces;  
E. dependency-aware partial-result cache;  
F. combined procedure library + result cache.

Include paraphrases, unseen entities, novel combinations, changed conditions, contradictory facts, exceptions, and corrected memory. Measure:

- exact answer/derivation correctness and calibrated UNKNOWN;
- transfer to unseen compositions;
- false-reuse rate and stale-result rate;
- cache hit rate and invalidation correctness;
- wall-clock latency including lookup, validation, and recomputation;
- peak RAM, disk use, and growth of the procedure library;
- training/search cost separately from inference cost.

**Critical gate:** reject any method whose speedup comes from returning stale or overconfident results. A cache hit is useful only if it is valid and cheaper than recomputation.

## Sources

- DreamCoder (PLDI 2021): https://doi.org/10.1145/3453483.3454080
- Stitch, Top-Down Synthesis for Library Learning: https://arxiv.org/abs/2211.16605
- LAPS (ICML 2021): https://proceedings.mlr.press/v139/wong21a.html
- SkillGen (Microsoft Research, 2026): https://www.microsoft.com/en-us/research/publication/skillgen-verified-inference-time-agent-skill-synthesis/
- SkillRL (2026): https://arxiv.org/abs/2602.08234
- Proof-state snapshotting in Lean 4 (2026): https://arxiv.org/abs/2605.25556
- LeanSearch v2 (2026): https://arxiv.org/abs/2605.13137
- LeanHammer premise selection (2025): https://arxiv.org/abs/2506.07477
