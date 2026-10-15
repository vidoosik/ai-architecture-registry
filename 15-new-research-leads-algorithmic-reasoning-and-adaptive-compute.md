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



## 18. Hierarchical Reasoning Model (HRM) and Tiny Recursive Model (TRM)

**Core idea:** reuse a small neural network recurrently to refine a latent state and answer over multiple computation steps, rather than increasing model depth or generating a long natural-language chain of thought. HRM uses two interdependent recurrent modules operating at different timescales; TRM simplifies the design to one small two-layer network that repeatedly updates a latent state and candidate answer.

**Why it matters:** these are concrete recent examples of parameter-efficient learned iterative computation. They are more directly relevant to compact reasoning than a generic claim that “recurrence is efficient,” and worth testing on CPU as specialist reasoning modules.

**Reported results and essential caveats:**
- HRM reports 27M parameters and strong results on selected Sudoku, maze, and ARC tasks.
- TRM reports about 7M parameters, around 45% on ARC-AGI-1, around 8% on ARC-AGI-2, and strong results on Sudoku-Extreme in its evaluated setup.
- These are specialized visual/grid reasoning tasks, not general language dialogue. Training and augmentation costs can be substantial despite small parameter count.
- Test-time augmentation and majority voting materially affect some reported TRM scores; an independent analysis reports that task identity and test-time compute matter, and that effective recursion may be shallower than the architecture suggests. Do not interpret the benchmark headline as proof of general intelligence or CPU speed.

Sources:
- HRM paper: https://arxiv.org/abs/2506.21734
- TRM paper: https://arxiv.org/abs/2510.04871
- Official TRM code: https://github.com/SamsungSAILMontreal/TinyRecursiveModels
- Independent analysis of TRM: https://arxiv.org/abs/2512.11847
- ARC Prize 2025 results and methodology: https://arcprize.org/blog/arc-prize-2025-results-analysis

## 19. Symbol-Equivariant Recurrent Reasoning Models (SE-RRM)

**Core idea:** encode symbol/color permutation symmetry directly in the recurrent reasoning architecture, rather than relying only on data augmentation to teach the model that renaming symbols should not change the abstract solution.

**Why it matters:** our graph reasoning should be invariant to arbitrary entity names and symbols. The principle of building the right symmetry into the architecture could reduce training examples and prevent shortcut learning.

**Reported scope:** an ICML 2026 paper reports 2M-parameter models competitive on ARC-AGI-1/2 and better extrapolation across Sudoku sizes (from 9×9 to 4×4, 16×16, and 25×25) than prior recurrent reasoning models in its experiments.

**Limits:** this is evidence in structured symbol/visual puzzles. The transfer to natural-language entities, ambiguous references, or causal graph reasoning remains untested. It is worth borrowing the invariance principle before assuming the entire architecture is suitable.

Source:
- ICML 2026 paper: https://proceedings.mlr.press/v306/freinschlag26a.html



## 20. ArcMemo: lifelong concept-level memory for reasoning

**Core idea:** distill reusable, modular concepts from successful or attempted reasoning traces, store them in external memory, and selectively retrieve relevant concepts for later tasks. This is deliberately different from storing exact question-answer pairs or summaries tied to one original problem.

**Why it matters:** it is closely aligned with our requirement that the system learn from experience without updating a large model's weights. It suggests a practical test: after solving one graph scenario, store the generalizable procedure and see whether it helps with a new scenario that shares the structure but changes entities and surface wording.

**Reported scope and caveats:** the 2025 preprint reports a 7.5% relative gain over its no-memory baseline on ARC-AGI, with gains scaling with inference compute and dynamic memory updates outperforming an otherwise similar fixed-memory setup. The results are specific to its LLM and ARC pipeline; concept memories can also accumulate incorrect or overgeneralized lessons and need provenance, confidence, and contradiction checks.

Sources:
- Paper: https://arxiv.org/abs/2509.04439
- Code: https://github.com/matt-seb-ho/arc_memo
- ICLR 2026 presentation/page: https://lacuna.tiptreesystems.com/work/arcmemo-abstract-reasoning-composition-with-lifelong-llm-memory/wrk_fa6c0c436d3049bd55cb2f96d3f42e1b

## 21. SOAR: self-improving evolutionary program synthesis

**Core idea:** alternate between searching over candidate programs (sample, mutate/refine, evaluate) and learning from the search history. Successful attempts are converted into additional problem-solution training examples, improving the model's future proposal and refinement behavior.

**Why it matters:** this is a concrete self-improvement loop with external verification, not a claim that a model spontaneously becomes smarter. It could inspire an offline learning pipeline for our assistant: generate candidate rules/programs, execute them against tests, preserve verified solutions, and train or update the proposal mechanism.

**Caveats:** the published result is for ARC-AGI and relies on LLM sampling plus fine-tuning; the public headline of 52% is specific to the paper's benchmark/setup and is not a CPU-only result. Fine-tuning and evolutionary search can be expensive. For our system, start with deterministic procedure synthesis and test-driven memory updates before considering weight updates.

Sources:
- ICML 2025 paper: https://proceedings.mlr.press/v267/pourcel25a.html
- Code: https://github.com/flowersteam/SOAR

## 22. CompressARC: minimum-description-length search at test time

**Core idea:** solve a new puzzle by searching for a compact program/description that explains the puzzle's input-output examples, optimizing description length for that individual task instead of relying on conventional pretraining.

**Why it matters:** this tests the hypothesis that a reusable program language plus a simplicity prior can solve some unfamiliar tasks from their examples. The principle of choosing a compact explanation is relevant to avoiding ad hoc rules in our graph system.

**Caveats:** the reported model is tiny (76K parameters) and reports solving 20% of ARC-AGI-1 evaluation puzzles in its paper, but this is achieved through test-time optimization on each puzzle, not a free single-pass answer. Per-instance search cost, puzzle-specific bias, and broader transfer need scrutiny. It is not a general language assistant.

Source:
- Paper: https://arxiv.org/abs/2512.06104
- ARC Prize 2025 competition/results context: https://arcprize.org/competitions/2025

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


## 23. Symmetry and inductive bias: make irrelevant changes irrelevant

**Core idea:** design the representation or update rule so transformations that should preserve the answer (renaming symbols, permuting colors, reordering a set, relabeling graph nodes) do not require the model to relearn the same solution. Invariance means the output stays the same; equivariance means the output transforms predictably with the input.

**SE-RRM evidence:** the ICML 2026 paper encodes symbol/color permutation equivariance in recurrent reasoning layers. It reports competitive ARC-AGI-1/2 results with 2M parameters and extrapolation between Sudoku sizes (trained on 9×9, tested on 4×4, 16×16, and 25×25) in its experimental setting.

**Implication for our graph:** entity names should be identifiers, not reasoning features. Tests should rename every entity, permute relation order, reorder facts, and change arbitrary colors/labels while preserving graph structure. The conclusion and explanation structure should remain equivalent. Do not assume SE-RRM itself solves language understanding or causal reasoning; the transferable lesson is the architectural bias and test protocol.

**Risks:** only enforce a symmetry when it is valid for the task. Time order, direction of a relation, role labels, or different entity types may be semantically meaningful and must not be arbitrarily permuted.

Sources:
- SE-RRM paper (ICML 2026): https://proceedings.mlr.press/v306/freinschlag26a.html
- Official implementation: https://github.com/ml-jku/SE-RRM

## 24. Evolutionary program synthesis and verified self-improvement

**Core idea:** maintain a population or stream of candidate programs/rules; propose variants, execute them against an evaluator, discard failures, retain and recombine successful candidates, and optionally train the proposal mechanism on the search history. This is a process architecture, not a single neural-network layer.

**Distinct examples already found:**
- **DreamCoder / library learning:** search for programs and factor recurring subprograms into a reusable domain-specific library; a neural search policy and the library improve together. Useful for converting repeated successful reasoning into compact skills.
- **SOAR:** an ARC-focused loop combining candidate generation/refinement, evaluation, and learning from attempts. Its reported benchmark performance depends on the paper's LLM, search, and fine-tuning setup; it is not a CPU-only result.
- **AlphaEvolve:** an LLM-based evolutionary coding agent that proposes code changes and uses one or more evaluators to iteratively improve algorithms. The 2025 report and 2026 follow-up describe applications to algorithmic/scientific discovery and infrastructure optimization. This is evidence for the search-and-evaluate loop in selected domains, not evidence that an autonomous agent can safely invent arbitrary world rules.
- **CompressARC:** a related but distinct minimum-description-length search approach that optimizes a compact program for each ARC task at test time; small parameter count does not remove per-task search cost.

**What transfers to our project:** begin with offline rule/program candidates over a constrained DSL. Run every candidate against positive cases, counterexamples, exception tests, and held-out compositions. Promote a rule only when it passes, keep its evidence and provenance, and preserve rollback. Separate (a) discovering a candidate, (b) verifying it, and (c) storing it as reusable knowledge. Never allow a candidate to silently overwrite a previously trusted fact.

**Main costs and failure modes:** candidate search can be expensive; evaluators can be incomplete; overfitting to the test suite can masquerade as discovery; LLM-proposed code may be invalid or unsafe. Record search budget, number of candidates, verifier coverage, regressions, wall-clock time, and RAM. For our CPU-first project, this is primarily an offline learning pipeline, not something to run on every user query.

Sources:
- DreamCoder: https://doi.org/10.1145/3453483.3454080
- Bayesian Program Learning by Decompiling Amortized Knowledge (ICML 2024): https://proceedings.mlr.press/v235/palmarini24a.html
- SOAR (ICML 2025): https://proceedings.mlr.press/v267/pourcel25a.html
- AlphaEvolve report: https://arxiv.org/abs/2506.13131
- AlphaEvolve results repository: https://github.com/google-deepmind/alphaevolve_results
- CompressARC: https://arxiv.org/abs/2512.06104

## 25. Neural Cellular Automata (NCA) and decentralized self-organization

**Core idea:** a system is represented as many local cells, each holding a small state vector. At each step, a cell updates its state using a shared learned rule and information from a local neighborhood. Repeated local interactions can produce a global pattern or behavior without a single central controller directing every part.

**What is established:** NCA is an established research family for self-organizing pattern growth, regeneration-like dynamics, image synthesis, and distributed control. Reviews published in 2026 discuss applications across biology, robotics, and machine learning; recent work also explores reasoning-like puzzle tasks. A 2026 TMLR review provides a unified modular framework and reference implementation, NCAtorch.

**Why it is interesting here:** it is a different hypothesis from the explicit graph core. Local units could iteratively exchange compact messages to settle a state, propagate constraints, or coordinate specialist processes. This may offer a useful experimental model for distributed constraint propagation, but it is not yet evidence that NCA is a better general-purpose reasoning engine than a graph with explicit rules.

**Risks:** global behavior can be hard to interpret; convergence and robustness are not guaranteed; local rules may require many update steps; scaling and training costs vary by task. A grid-based NCA is not automatically appropriate for a semantic world graph. A graph-NCA variant would need to define neighborhoods, message/state semantics, stopping criteria, and how to preserve provenance and exceptions.

**Minimal experiment:** encode each node as a small state vector and let only neighboring nodes exchange bounded messages. Compare against deterministic graph propagation on the same synthetic worlds: rule-chain depth, conflicting causes, hot/cold exception, unseen node names, and node-order permutations. Measure accuracy, steps to convergence, oscillations, CPU latency, and peak RAM. Keep this as a research branch until it beats the deterministic baseline on a defined metric.

Sources:
- Hartl, Levin & Pio-Lopez, *Neural cellular automata: Applications to biology and beyond classical AI*, Physics of Life Reviews (2026): https://pubmed.ncbi.nlm.nih.gov/41365104/
- Spitznagel & Keuper, *A New Kind of Network? Review and Reference Implementation of Neural Cellular Automata*, TMLR (2026): https://openreview.net/pdf?id=NRwjj0ZLq0
- NCAtorch reference implementation: https://github.com/mspitzna/NCAtorch
- 2026 survey of NCA applications: https://arxiv.org/abs/2609.24595

## Updated relevance for the compact CPU-first assistant

1. **Apply now as test requirements:** name/symbol permutation, fact-order permutation, and graph-node relabeling tests. These are cheap and directly target shortcut learning.
2. **Most promising learning mechanism to study:** constrained program/rule synthesis with a verifier and a growing library of proven reusable procedures. Run it offline first.
3. **Exploratory architecture, not a replacement yet:** NCA-style local state updates on a graph. Compare with explicit graph propagation; do not presume that decentralized means faster or more intelligent.
4. **Keep evidence separated:** reported paper benchmark results are not measurements on our hardware or on our graph-world project. Every local claim needs a reproducible test.
