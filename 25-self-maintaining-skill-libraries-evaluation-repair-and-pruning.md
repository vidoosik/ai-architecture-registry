# 25 — Self-maintaining skill libraries: evaluation, repair, and pruning

Research cycle: 2026-10-15

## Executive result

A growing procedure library needs a maintenance loop, not just a discovery loop. A skill can solve new tasks yet also cause regressions, increase cost, conflict with other skills, or become stale when dependencies change. Evaluate each skill against a baseline, track its preconditions and dependencies, and promote or restrict it only after held-out tests.

## 1. Differential evaluation of skills

**Agent Skills Can Be Harmful: An Empirical Study of Skill-Induced Failures in LLM Agents** (Microsoft Research, August 2026)  
https://www.microsoft.com/en-us/research/publication/agent-skills-can-be-harmful-an-empirical-study-of-skill-induced-failures-in-llm-agents/

This paper studies cases where loaded skills improve task completion, have no effect, increase execution cost, or reduce success. Its differential method compares a skill-guided run with a no-skill or semantically matched reference on the same task.

**Design lesson:** evaluate a skill by its marginal contribution, not by the success of the trace that inspired it. Measure correctness, regressions, latency/token cost, and whether the skill was actually relevant. The evidence is from LLM-agent tasks, not a proof that every symbolic reasoning system behaves identically.

## 2. SkillOps: library-level maintenance

**SkillOps: Managing LLM Agent Skill Libraries as Self-Maintaining Software Ecosystems** (2026)  
https://arxiv.org/abs/2605.13716

SkillOps highlights library-level defects: near-duplicate skills, stale interfaces, broken dependency chains, missing validators, and harmful combinations. It proposes typed skill contracts and a graph of relationships between skills, with checks for utility, compatibility, risk, and validation.

**Design lesson:** a procedure should carry explicit preconditions, expected outputs, assumptions, validation rules, failure conditions, dependencies, and version/status. Inspect interactions across the library as well as individual entries. This is a framework proposal, not a guarantee of autonomous maintenance or formal correctness.

## 3. Skill evolution survey

**Agent Skill Evaluation and Evolution: Frameworks and Benchmarks** (June 2026)  
https://arxiv.org/abs/2606.11435  
Project: https://github.com/Cassie07/AgentSkill_Survey

The survey distinguishes execution feedback, trajectory distillation, compression, and reinforcement learning as different ways to evolve skills. They should not be collapsed into one technique: they have different compute costs and evidence requirements.

## 4. ReGAL: refactor programs without updating model weights

**ReGAL: Refactoring Programs to Discover Generalizable Abstractions** (ICML 2024)  
https://proceedings.mlr.press/v235/stengel-eskin24a.html

ReGAL builds a reusable function library by refactoring programs while preserving execution output, then verifies and refines abstractions. The paper reports model-specific gains across five program-generation/reasoning datasets, including a 26.1-point absolute accuracy increase on its date-reasoning benchmark for CodeLlama-13B.

**Design lesson:** a procedure library can improve through structure-preserving refactoring rather than weight updates. Tests can detect behavior changes, though finite examples do not prove equivalence for all inputs.

## 5. PAE: ground discovery in achievable tasks

**Proposer-Agent-Evaluator (PAE): Autonomous Skill Discovery For Foundation Model Internet Agents** (ICML 2025)  
https://proceedings.mlr.press/v267/zhou25ah.html

PAE proposes context-grounded tasks, attempts them with an agent, evaluates outcomes, and uses feedback to refine the policy. The paper reports around 50% relative improvement in zero-shot generalization to unseen tasks and websites in its web-navigation settings.

**Design lesson:** the discovery loop needs grounded tasks and observed outcomes. The evaluator itself must be tested; otherwise the system can reinforce mistaken judgments.

## 6. Agentic Skill Discovery

**Agentic Skill Discovery** (Robotics and Autonomous Systems, February 2026)  
https://www.sciencedirect.com/science/article/pii/S0921889025003458  
Code: https://github.com/xf-zhao/Agentic-Skill-Discovery

This work proposes tasks from scene descriptions and robot configurations, learns behaviors through reinforcement learning, and uses an independent vision-language model to assess them. The authors describe a skill library growing from an initially empty set toward more capable behaviors.

**Design lesson:** when practical, validate a proposed skill with a checker independent of the component that proposed it. This is a robotics result, not a direct recipe for language-level reasoning.

## 7. Skill lifecycle

1. **Proposed:** extracted from repeated traces or suggested to solve a gap.
2. **Sandboxed:** tested on motivating examples and counterexamples.
3. **Validated:** passes its contract and held-out regression suite.
4. **Active:** available to the router within its tested scope.
5. **Restricted:** invoked only in a narrower context after regressions.
6. **Merged:** factored with duplicates into shared primitives.
7. **Deprecated:** disabled when low-value, stale, or consistently harmful.

Keep provenance and rollback history. A failed procedure may reveal a missing precondition or exception rather than being useless.

## 8. Metrics for each procedure

- Marginal correctness gain against a no-skill baseline.
- Regression rate on previously solved cases.
- Applicability precision: how often preconditions truly hold.
- Coverage gain on held-out tasks and novel compositions.
- Wall-clock latency, memory, storage, and search cost.
- Conflicts with other skills, staleness, and dependency changes.
- Evidence diversity: paraphrases, exceptions, changed assumptions, contradictions, and corrected facts.
- Maintenance cost, including test generation and validation.

A skill must not be evaluated only on the examples used to create it.

## 9. Compact CPU-first experiment

Compare append-only memory, manual cleanup, per-skill contracts and regression tests, differential evaluation, duplicate merging, dependency-aware invalidation/rollback, and a combined maintenance loop. Evaluate new entities, paraphrases, unseen combinations, exceptions, contradictory facts, and memory corrections.

**Critical gate:** do not enable a new skill globally based only on positive examples. Require held-out benefit, bounded regression, and rollback. If no reliable test oracle exists, keep the skill restricted or explicitly uncertain.

## 10. Implication for architecture research

This maintenance layer can sit above symbolic rules, synthesized programs, modular neural policies, or hybrid systems. It does not select a core architecture. The general cycle is: propose a procedure, specify when it applies, compare against the previous system, record provenance/dependencies, promote or restrict based on evidence, and preserve versions for rollback.

The lifecycle is not free: test generation, execution, and evaluation can dominate cost. Measure them separately from ordinary inference.

## Sources

- Agent Skills Can Be Harmful (Microsoft Research, 2026): https://www.microsoft.com/en-us/research/publication/agent-skills-can-be-harmful-an-empirical-study-of-skill-induced-failures-in-llm-agents/
- SkillOps (2026): https://arxiv.org/abs/2605.13716
- Agent Skill Evaluation and Evolution survey (2026): https://arxiv.org/abs/2606.11435
- ReGAL (ICML 2024): https://proceedings.mlr.press/v235/stengel-eskin24a.html
- PAE (ICML 2025): https://proceedings.mlr.press/v267/zhou25ah.html
- Agentic Skill Discovery (2026): https://www.sciencedirect.com/science/article/pii/S0921889025003458
