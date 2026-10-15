# Newly surfaced research directions: algorithmic reasoning, adaptive depth, and learning-to-learn

This pass focuses on ideas that are more than another model name: they offer distinct computational mechanisms or useful evaluation methods. Some are related to existing entries, but were not clearly separated in the earlier taxonomy. Inclusion is not a recommendation to implement them.

## 1. Neural Algorithmic Reasoning (NAR)

**Core idea:** train neural networks to execute the intermediate steps of classical algorithms, rather than only map inputs to final answers. Graph/message-passing networks are often used to represent algorithmic state and update it step by step.

**Why it matters here:** this is directly relevant to the goal of generalizing a reasoning procedure to new entities and larger or differently structured problems. It may help distinguish learning a reusable procedure from memorizing answer patterns.

**Important distinction:** Neural GPU is one historical lead for algorithmic generalization; Neural Algorithmic Reasoning is a broader research programme, not a synonym for Neural GPU.

**Evaluation lead:** the CLRS Algorithmic Reasoning Benchmark covers sorting, searching, dynamic programming, graph, string, and geometric algorithms, with intermediate algorithmic probes. CLRS-Text is a related language-oriented benchmark. These are possible external tests, not proof that a model will reason well in everyday dialogue.

Sources:
- Neural Algorithmic Reasoning: https://arxiv.org/abs/2105.02761
- CLRS benchmark: https://proceedings.mlr.press/v162/velickovic22a.html
- Official implementation: https://github.com/google-deepmind/clrs
- CLRS-Text: https://arxiv.org/abs/2406.04229

## 2. Universal Transformer, recurrent depth, and adaptive computation

**Core idea:** reuse a shared transformation block across multiple computation steps. Instead of always passing through a fixed stack of different layers, the model repeatedly refines its internal state; adaptive halting methods can let easier inputs stop earlier.

Related methods include Adaptive Computation Time (ACT) and PonderNet. Recent work continues to investigate the trade-off between number of reasoning steps, scratchpad/memory capacity, stability, and compute.

**Why it matters:** a system might spend more computation on ambiguous or multi-step questions and less on easy ones. Shared weights may reduce parameter storage compared with a stack of distinct layers.

**Risks:** recurrent depth still costs compute per step; halting can be difficult to train; shared weights do not guarantee quality or faster CPU inference. A recent 2026 preprint reports results on Sudoku-Extreme for one specific setup, not a general-purpose assistant.

Sources:
- Universal Transformers: https://arxiv.org/abs/1807.03819
- PonderNet: https://mlanthology.org/icmlw/2021/banino2021icmlw-pondernet/
- 2026 preprint, Universal Transformers Need Memory: https://arxiv.org/abs/2604.21999

## 3. Algorithm Distillation / learning an update procedure from histories

**Core idea:** train a sequence model on trajectories or learning histories produced by an algorithm, so that it can continue improving its behavior in context without updating its parameters at deployment time.

The published Algorithm Distillation work is specifically about in-context reinforcement learning; it should not be generalized into a claim that any model can learn any algorithm from dialogue history.

**Why it matters:** it explores whether reusable adaptation behavior can be encoded in a model, rather than storing only a final policy or repeatedly fine-tuning weights.

**Risks:** it is an RL-focused method, relies on generated training histories, and does not directly solve persistent factual memory, exact logic, or natural-language grounding.

Source:
- https://arxiv.org/abs/2210.14215

## 4. Inductive reasoning and out-of-distribution generalization as explicit research targets

**Core idea:** separate deduction (derive a consequence from stated premises) from induction (infer a possible general rule from examples), and test whether a method transfers when names, wording, environments, or rule combinations change.

A 2026 ACL survey groups approaches to improving inductive reasoning into post-training, test-time exploration, and data augmentation, and discusses sandbox-based evaluation. This is a research/evaluation framework, not a new neural architecture by itself.

**Why it matters:** our graph tests mostly exercise explicit facts and rules. The assistant will also need to propose hypotheses from observations while keeping them provisional and distinguish correlation from sufficient evidence.

**Test requirement:** score the proposed rule, counterexamples, uncertainty, and ability to revise it when a new example contradicts the hypothesis.

Sources:
- ACL 2026 survey: https://aclanthology.org/2026.acl-long.1447/
- Generalization survey: https://doi.org/10.1016/j.neucom.2024.128701
- Reasoning-generalization paper collection (use as a discovery index, verify papers individually): https://github.com/tue09/awesome-reasoning-generalization

## 5. Predictive State Representations (PSRs)

**Core idea:** represent a partially observed dynamical system by predictions about future action-observation tests, rather than only by a hidden latent state or a hand-written symbolic state.

**Why it may matter:** this is a distinct alternative for tracking uncertain, changing environments and evaluating what is likely to happen next. It is more naturally connected to sequential decision-making and partially observed systems than to general text generation.

**Risks:** classical PSRs are not a ready-made language model; learning and scaling them to open-ended, high-dimensional settings is a separate challenge. Keep separate from predictive coding, which concerns prediction-error-driven state updates and learning.

Entry point:
- Survey on predictive state representations: https://gxbwk.njournal.sdu.edu.cn/CN/Y2010/V40/I4/23

## 6. Test-time search, verification, and repair as a separate axis

This is not one architecture family, but it deserves explicit tracking alongside model design: candidate generation, search/decomposition, a verifier or executable test, repair, and a stopping/abstention rule.

For our system, this can mean deriving a graph conclusion with a trace, checking whether every premise exists, trying alternative hypotheses when evidence conflicts, and refusing to conclude when no candidate is sufficiently supported. For code, it means run → test → inspect failure → revise.

**Why it matters:** better control flow can improve reliability without requiring all reasoning capability to be embedded in one larger model.

**Risk:** extra search can multiply latency and a weak verifier can endorse wrong answers. Measure accuracy, false confidence, number of attempts, and end-to-end CPU time together.

Research discovery index:
- Reasoning generalization survey and taxonomy: https://github.com/tue09/awesome-reasoning-generalization



## 7. Reusable neural modules and compositional skill routing

**Core idea:** split behavior into reusable modules (for example, parse a subgoal, locate an object, move to a landmark, verify a result) and learn how to select and compose them for a new task. This is distinct from a conventional MoE that routes tokens to parameter experts: modules may represent explicit skills or stages of a procedure.

**Why it matters:** the compact assistant could reuse small, testable procedures rather than solve every task from scratch. The graph can store facts and preconditions; a learned router or language model can choose which procedure to call; each procedure can return a result and evidence.

**Evidence and caveats:** published work on modular instruction following reports better generalization to novel subgoal combinations in its ALFRED setting. A 2026 preprint proposes that reusable latent skills and routing mechanisms help explain compositional generalization in language-model reasoning. These results motivate experiments, but do not prove that manually modularizing our assistant will improve open-domain reasoning.

Sources:
- Modular Networks for Compositional Instruction Following: https://aclanthology.org/2021.naacl-main.81/
- From Reasoning Traces to Reusable Modules (2026 preprint): https://arxiv.org/abs/2606.18089
- Modular continual learning (2026): https://doi.org/10.1016/j.neucom.2026.135079

## 8. Object-centric representation: evidence and limits

Object-centric representations are already listed in the world-model section; the newer lead is the explicit empirical question of whether representing scenes as objects and relations actually improves compositional generalization under limited data and compute.

A 2026 ICML paper reports that object-centric approaches perform better in harder unseen combinations of visual object properties in its tested visual QA settings and are more sample-efficient, while dense representations can catch up or win on easier settings or with more data. This is useful evidence for the general principle of explicit entity/property structure, but it is **visual-domain evidence**, not a direct result about text-only graph reasoning.

Sources:
- Are Object-Centric Representations Better at Compositional Generalization? (ICML 2026): https://proceedings.mlr.press/v306/kapl26b.html
- Visual reasoning in object-centric deep neural networks (2025): https://doi.org/10.1016/j.neunet.2025.107582



## 9. Classical integrated cognitive architectures: ACT-R, Soar, CLARION, LIDA, ICARUS, NARS

These are not neural-network layer families; they are **whole-system designs** for coordinating working memory, long-term memory, goals, rules/procedures, learning, attention, planning, and action. Examples include ACT-R, Soar, CLARION, LIDA, ICARUS, and NARS; BDI and subsumption architectures are related approaches for agents.

**Why they matter:** our project is ultimately a complete intellectual assistant, not merely a new sequence model. These architectures may offer tested design patterns for the control cycle, working vs episodic/semantic memory, goal management, and learning from experience.

**Risks:** many are older research systems with domain-specific assumptions, and their inclusion does not imply modern language-model quality. Study the mechanisms and reusable principles rather than assuming the full framework should be adopted wholesale.

Sources:
- Integrated cognitive architectures survey: https://doi.org/10.1007/S10462-009-9094-9
- ACT-R vs Soar analysis: https://arxiv.org/abs/2201.09305
- Autonomous learning in cognitive architectures: https://doi.org/10.1016/j.newideapsych.2014.03.002

## 10. Neural Language Interpreter (NLI): learning a small executable language

**Core idea:** the ICLR 2026 paper *Gradient-Based Program Synthesis with Neurally Interpreted Languages* proposes learning a discrete, symbolic-like vocabulary of primitive operations and a differentiable neural executor that interprets variable-length sequences of those operations.

**Why it matters:** this directly targets the tension between symbolic program synthesis (compositional, but often requires a designed DSL) and neural models (flexible, but can generalize poorly). A learned compact operation language is conceptually relevant to reusable procedures in the assistant.

**Risks:** this is a specific research result, not evidence that a tiny learned interpreter can replace a language model. Check the task scope, training setup, code, generalization tests, and runtime before considering an implementation.

Source:
- ICLR 2026 paper: https://proceedings.iclr.cc/paper_files/paper/2026/hash/c9cde817d04811ba28e44071bd9f76a5-Abstract-Conference.html

## 11. Conditional Neural Processes (CNPs) and fast function adaptation

**Core idea:** a model learns to infer a predictive function from a small set of observed examples at inference time. CNPs were developed partly as a scalable alternative to Gaussian-process-style few-shot prediction.

**Why it matters:** as a possible mechanism for quickly adapting a predictor to a new task or small dataset without retraining a large model. Evidential variants also explicitly investigate separating sources of predictive uncertainty.

**Risks:** the canonical work focuses on regression, classification, and related function-prediction tasks—not open-domain language reasoning. Treat it as a specialist adaptation/uncertainty family, not a replacement language core.

Sources:
- Conditional Neural Processes: https://proceedings.mlr.press/v80/garnelo18a.html
- Evidential Conditional Neural Processes: https://doi.org/10.1609/aaai.v37i8.26125



## 12. Global Workspace / blackboard coordination

**Core idea:** many specialized processes operate in parallel or semi-independently; a limited shared workspace selects salient information and broadcasts it so other processes can use it. Blackboard architectures are a related classic pattern for integrating partial hypotheses from different specialist modules. LIDA and IDA are computational architectures inspired by Global Workspace Theory.

**Why it matters:** this offers a possible control design for a hybrid assistant: graph inference, language interpretation, memory retrieval, planning, and verification can propose results, while a workspace selects what deserves attention and what should be broadcast to the rest of the system.

**Risks:** this is a cognitive/control architecture, not a demonstrated recipe for machine consciousness or a proven route to better reasoning. A workspace can become a bottleneck or a noisy message bus unless selection, priority, and stopping rules are explicit.

Sources:
- Global Workspace Theory and IDA: https://doi.org/10.1016/j.neunet.2007.09.013
- Global Workspace Theory and LIDA: https://doi.org/10.1016/j.bica.2012.04.001

## 13. Bayesian program learning and compositional one-shot concept learning

**Core idea:** infer a compact generative program or causal procedure that explains an example, then use that program to recognize or generate new examples. This differs from ordinary nearest-neighbor retrieval: the target is a reusable explanation/program that can compose known primitives.

**Why it matters:** it is a useful research lens for learning a new concept from very few examples and generating valid new instances, potentially relevant to analogy and concept formation.

**Risks:** classic demonstrations focused on constrained domains such as simple visual concepts; hand-designed priors and program spaces matter greatly. It is not a general open-domain language architecture and should be compared against simple baselines on held-out compositions.

Source:
- One-shot learning by inverting a compositional causal process: https://proceedings.neurips.cc/paper/2013/hash/52292e0c763fd027c6eba6b8f494d2eb-Abstract.html



## 14. Neural Programmer-Interpreter (NPI): learn to call reusable subprograms

**Core idea:** one recurrent controller learns to select a program, pass arguments, inspect the current environment, and either call a lower-level subprogram or terminate. A persistent key-value memory stores program identities; execution traces supervise the sequence of calls.

**Why it matters:** this is a concrete architecture for composing small procedures into larger ones. It is closer to a learned procedure controller than a standard language model and provides a testable way to reuse a skill across tasks.

**Evidence and limits:** the original work demonstrated one model learning multiple compositional programs such as addition, sorting, and trajectory planning from pixels, with execution-trace supervision. This is a constrained task family, not proof of open-domain reasoning or easy unsupervised learning.

Sources:
- Paper: https://arxiv.org/abs/1511.06279
- Reference implementation: https://github.com/siddk/npi

## 15. DreamCoder and library learning: discover reusable abstractions

DreamCoder combines program search with a learned neural search policy and a growing library of abstractions. In a wake phase it solves tasks; in a sleep phase it trains on imagined/replayed problems. Repeated structure in successful programs can be factored into reusable library functions, allowing later solutions to be expressed and searched for more efficiently.

A 2024 follow-up, **Bayesian Program Learning by Decompiling Amortized Knowledge**, uses the neural search policy's learned biases to help discover useful library components, improving search efficiency and generalization in the evaluated synthesis domains.

**Why it matters:** this is one of the most direct research leads for a system that gradually turns repeated successful reasoning patterns into compact reusable procedures instead of accumulating only examples.

**Risks:** DreamCoder searches over programs in a chosen domain-specific language and is evaluated on constrained synthesis tasks. Library growth can also produce brittle abstractions if the domain grammar or examples are poor. It does not automatically learn unrestricted natural-language concepts.

Sources:
- DreamCoder (PLDI 2021): https://doi.org/10.1145/3453483.3454080
- Overview: https://arxiv.org/abs/2006.08381
- 2024 library-learning follow-up: https://proceedings.mlr.press/v235/palmarini24a.html

## 16. BABBLE / library learning modulo theory: learn abstractions beyond surface syntax

**Core idea:** extract reusable functions from a corpus of programs using e-graphs, equality saturation, anti-unification, and a domain-specific equational theory. Unlike purely syntactic pattern matching, this can discover shared abstractions among programs that look different but are equivalent under known rules.

**Why it matters:** if our system learns or accumulates procedures, equivalent formulations should ideally converge on one reusable skill rather than create duplicate variants. This could also support simplifying and consolidating symbolic rules.

**Important limitation:** BABBLE does not discover the truth of arbitrary real-world facts. It depends on a supplied equational theory and a corpus of candidate programs. Its published speedup concerns library-learning/compression in evaluated program domains, not language-model inference speed.

Sources:
- Paper: https://arxiv.org/abs/2212.04596
- POPL 2023 publication and materials: https://ztatlock.net/pubs/2023-popl-babble/
- Follow-up on decompiling amortized knowledge: https://proceedings.mlr.press/v235/palmarini24a.html

## 17. A concrete benchmark axis: compositional program synthesis

Beyond naming architectures, evaluate whether a learner can combine known subroutines into combinations never seen during training, generalize to longer programs, and transfer across input representations. The SCAN/RobustFill-based benchmark work explicitly separates these axes and finds that compositional generalization remains difficult even when a model improves over its baseline.

For our project, create matched tests where a graph rule or learned skill works alone, then compose several known skills in a new order, with new entity names and distractor facts. Measure success, explanation trace, abstention when a precondition fails, and CPU cost.

Source:
- Compositional Generalization and Decomposition in Neural Program Synthesis: https://arxiv.org/abs/2204.03758

## Fit to the compact CPU-first project

Initial relevance ranking for investigation, not a final model ranking:

1. **Highest direct relevance:** neural algorithmic reasoning as a way to study reusable procedures; adaptive/recurrent depth as a possible way to allocate computation; explicit search/verification as a reliability layer.
2. **Useful for research methodology:** inductive-reasoning and out-of-distribution benchmarks.
3. **More specialized:** Algorithm Distillation and Predictive State Representations, especially if the project expands into agents, control, or learning from interaction.

Do not infer that any of these is smaller or faster than a standard small Transformer. The next step, if one is selected, is to inspect the primary paper and implementation, then compare on matched tasks, CPU latency, peak RAM, and held-out compositions.

## Relationship to existing archive entries

- Neural GPU was already listed; this file adds the broader Neural Algorithmic Reasoning programme and CLRS/CLRS-Text as evaluation leads.
- Meta-learning and agents were already listed; Algorithm Distillation is a concrete, narrower method with a specific RL setting.
- Conditional computation was already listed; Universal Transformer/ACT/PonderNet make shared recurrent depth and learned halting more explicit.
- Predictive coding was already listed; Predictive State Representations are a distinct formalism and must not be merged with it.
- Generalization was already a project-wide criterion; inductive reasoning is added as a separate capability to test.
