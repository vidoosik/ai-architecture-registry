# Further research pass: continual learning, adaptive memory, test-time training, architecture evolution, and continuous-depth models

This pass focuses on how systems change after initial training, how they preserve accumulated knowledge, and how architectures can be altered or accelerated. It distinguishes persistent memory updates from parameter updates and from changing the network structure. No architecture is selected.

## 1. Continual learning: learn new tasks without erasing old ones

Continual learning studies systems that receive a changing stream of data or tasks and must retain useful prior knowledge. Catastrophic forgetting is the central failure mode, but not the only one: a method may avoid forgetting by refusing to learn, or preserve old benchmark scores while failing to transfer knowledge to new compositions.

A 2026 systematic review covers continual-learning research from 2010–2025 and proposes a refined taxonomy of settings and methods. A 2025 survey organizes methods and benchmarks, while a 2025–2026 line of work studies memory consolidation and metaplasticity.

Keep the mechanisms separate:
- **Replay / rehearsal:** retain examples or summaries and revisit them.
- **Regularization / parameter protection:** reduce changes to parameters judged important for earlier tasks.
- **Distillation:** preserve prior model behavior as a training target.
- **Parameter isolation / expansion:** allocate different parameters or modules to different tasks.
- **Memory consolidation:** filter, compress, or selectively stabilize experience before it becomes long-term knowledge.
- **Architectural adaptation:** alter the network or route to different modules as the task distribution changes.

A 2026 C3GAN paper proposes a class-incremental learning scheme inspired by memory consolidation without retaining exemplars. A 2025 metaplasticity paper proposes frequency-dependent consolidation to filter erroneous memories. These are specific experimental proposals, not evidence that biological analogies automatically produce robust lifelong learning.

**Evaluation requirement:** measure old-task retention, new-task acquisition, transfer to unseen combinations, memory growth, compute per update, and recovery after a wrong update. A system that merely stores every transcript is not necessarily learning; a system that rewrites memory without provenance risks stabilizing its own mistakes.

Sources:
- Continual learning: A systematic literature review (Neural Networks, March 2026): https://doi.org/10.1016/j.neunet.2025.108226
- Advances in continual learning: A comprehensive review (2025): https://doi.org/10.1016/j.eswa.2025.128739
- C3GAN: A brain-inspired memory consolidation for class-incremental learning (Neural Networks, 2026): https://doi.org/10.1016/j.neunet.2025.107952
- Neuromimetic metaplasticity for adaptive continual learning without catastrophic forgetting (Neural Networks, 2025): https://doi.org/10.1016/j.neunet.2025.107762
- Continual Learning With Knowledge Distillation: A Survey (IEEE TNNLS, 2025): https://doi.org/10.1109/TNNLS.2024.3476068

## 2. Adaptive test-time memory: retain strategies without changing the base model

Two 2026 language-agent papers highlight a different axis from online gradient updates.

### 2.1 Dynamic Cheatsheet (EACL 2026)
The framework gives a black-box language model a persistent, evolving memory of strategies, code snippets, insights, and previous mistakes. It does not require explicit ground-truth labels or human feedback for each new item. The paper reports that Claude 3.5 Sonnet more than doubled its accuracy on AIME math exams after retaining algebraic insights across questions. This is a result for a specific model, task, and setup—not a universal gain for all models.

### 2.2 AdaMEM (ICML 2026)
AdaMEM maintains long-term trajectory memory from prior experience and synthesizes short-term strategy memory dynamically as a task unfolds. It does not update the base model parameters online. The paper reports gains over static-memory baselines on ALFWorld, WebShop, and HotpotQA agentic search.

**Why the distinction matters:** external strategy memory can be easier to inspect, edit, and reset than learned weights, but it can still accumulate bad strategies, retrieve irrelevant episodes, or grow expensive. Memory quality depends on selection, abstraction, conflict resolution, retrieval, and consolidation—not only storage capacity.

Compare at least:
1. transcript-only history;
2. searchable episodic memory;
3. distilled reusable strategies;
4. explicit fact/rule memory with provenance and exceptions;
5. test-time parameter updates.

Measure accuracy, retrieval precision, token/latency overhead, memory growth, stale-strategy suppression, and performance after deliberately inserting a false lesson.

Sources:
- Dynamic Cheatsheet: Test-Time Learning with Adaptive Memory (EACL 2026): https://aclanthology.org/2026.eacl-long.333/
- AdaMEM: Test-Time Adaptive Memory for Language Agents (ICML 2026): https://proceedings.mlr.press/v306/zhang26cn.html

## 3. Test-time training: temporarily update parameters on the current task

Test-time training (TTT) updates model parameters using information available at inference time. It is not the same as in-context learning, which conditions on examples without necessarily updating weights; it is also not the same as external-memory retrieval.

### 3.1 Test-time training for few-shot reasoning
A 2025 ICML study investigates temporary parameter updates from in-context examples. On ARC, the paper reports up to six times the accuracy of its fine-tuned baselines, reaching 53.0% on the public validation set with an 8B model and 61.9% when ensembled with program synthesis. On BIG-Bench Hard, its 10-shot result increases from 50.5% to 57.8%. These are benchmark- and setup-specific results and use a model far larger than the target compact CPU envelope.

### 3.2 Test-time learning on unlabeled streams
A separate 2025 ICML paper formulates test-time learning as minimizing input perplexity and uses LoRA updates to limit the number of trainable parameters and reduce forgetting. Its AdaptEval experiments report improvements of at least 20% on its domain-adaptation settings. The metric and task setup matter: reducing perplexity does not guarantee that every downstream answer becomes more correct.

### 3.3 Selective adaptation and future gain
A 2026 ICML paper proposes Future-Gain Guided Test-Time Learning: rather than blindly updating on uncertain generated tokens, it prioritizes tokens predicted to reduce uncertainty in subsequent generation and scales updates according to risk. This addresses a key danger of self-training: a mistaken prediction can become its own training signal and amplify an error.

**Critical costs:** gradient computation, optimizer state, mutable parameters, checkpoints/rollback, and possible interference with prior behavior. A fair comparison should include total wall-clock cost and peak RAM—not just post-adaptation accuracy. For small CPU systems, retrieval or distilled strategy memory may be cheaper, but that is an empirical question.

Sources:
- The Surprising Effectiveness of Test-Time Training for Few-Shot Learning (ICML 2025): https://proceedings.mlr.press/v267/akyurek25a.html
- Test-Time Learning for Large Language Models (ICML 2025): https://proceedings.mlr.press/v267/hu25z.html
- Test-Time Training Provably Improves Transformers as In-context Learners (ICML 2025): https://proceedings.mlr.press/v267/gozeten25a.html
- Future-Gain Guided Test-Time Learning for Large Language Models (ICML 2026): https://proceedings.mlr.press/v306/bian26b.html

## 4. Neural architecture search and evolutionary structure discovery

Neural architecture search (NAS) explores model structure rather than only training the parameters of a fixed structure. Methods include discrete mutation/crossover, differentiable relaxation of architecture choices, Bayesian optimization, performance predictors, and training-free structural filters.

Recent examples:
- **Graph evolutionary NAS (2026):** encodes candidate architectures as attributed graphs and uses a graph neural network with Bayesian optimization to represent and evolve candidates.
- **DAG-based structural screening (2026):** uses connectivity depth and breadth as training-free indicators; the paper reports reducing search cost by up to 23.6% while maintaining or improving accuracy in its tested settings.
- **Population-guided evolutionary NAS (2025):** uses selection, crossover, and mutation to explore architecture candidates while balancing exploitation and diversity.
- **Differentiable synthesis of behavior trees (2025):** searches over structured behavior-tree architectures and execution nodes using a continuous relaxation of a grammar-defined space. This is a policy/control representation, not a general language model.

**Main limitation:** NAS can be more expensive than training the final model, and search results depend heavily on the candidate space, proxy metric, hardware, and evaluation budget. A training-free proxy can discard promising candidates if its assumptions do not hold. Search should optimize the actual deployment objective—quality under CPU/RAM/latency constraints—not benchmark accuracy alone.

Sources:
- Neural architecture search with graph evolutionary (Neurocomputing, online 18 September 2026): https://doi.org/10.1016/j.neucom.2026.135113
- From complexity to simplicity: Topological insights for neural architecture search (Applied Soft Computing, September 2026): https://doi.org/10.1016/j.asoc.2026.116389
- Population-based guiding for evolutionary neural architecture search (Scientific Reports, 2025): https://www.nature.com/articles/s41598-025-25840-5
- Differentiable Synthesis of Behavior Tree Architectures and Execution Nodes (NeurIPS? PMLR NeSy 2025): https://proceedings.mlr.press/v288/huang25a.html

## 5. Continuous-time / continuous-depth recurrent models and their acceleration

Liquid Time-Constant networks (LTCs) model hidden-state dynamics with input-dependent time constants. Neural Circuit Policies (NCPs) use sparse recurrent wiring inspired by small nervous systems. These are recurrent dynamical systems, not replacements for language Transformers by definition; much of their evidence concerns control, time series, and irregularly sampled signals.

A key 2026 result, **CoDA (Continuous Depth Acceleration)**, addresses a major practical issue: continuous-depth networks and LTC-NNs may require many numerical ODE solves. CoDA replaces the continuous-depth computation with a compact GRU module, a low-dimensional linear ODE, and a dense layer. The authors report 6.7× training speedup and 1.8× inference speedup on the Liquid Foundation Model LFM-1.2B without loss of accuracy in the tested setting, plus results across six LTC applications.

This is a useful example of **architecture-preserving or architecture-approximating acceleration**: a more expensive dynamical computation is replaced by a cheaper surrogate designed to retain its behavior. It does not imply that every continuous-time model is faster, or that the gains transfer to old CPUs or arbitrary language tasks.

Sources:
- Efficient Continuous-Depth Modeling with GRU Equivalents (ICML 2026): https://proceedings.mlr.press/v306/banerjee26b.html
- Liquid Time-constant Networks (AAAI 2021 foundational paper): https://ojs.aaai.org/index.php/AAAI/article/view/16936
- Neural Circuit Policies for Virtual Character Control (2024): https://doi.org/10.1007/s11063-024-11640-x
- A liquid neural network with physical evolution for variable continuous time series prediction (2026): https://doi.org/10.1016/j.jocs.2025.102757

## 6. Mechanism map: four kinds of change

| What changes? | Representative methods | What must be preserved or checked? |
|---|---|---|
| External memory | Dynamic Cheatsheet, AdaMEM, SDM, episodic/rule memory | Provenance, retrieval relevance, conflicts, stale or false entries |
| Model parameters at test time | TTT, LoRA adaptation, gradient-based test-time learning | Rollback, forgetting, optimizer/RAM cost, self-reinforcing errors |
| Learned knowledge over a task stream | Continual learning, distillation, metaplasticity, consolidation | Old-task retention and genuine transfer to new compositions |
| Model structure | NAS, graph evolution, differentiable behavior-tree synthesis | Search budget, deployment latency, proxy bias, reproducibility |
| State dynamics / computation | LTC, NCP, continuous-depth models and CoDA | Solver cost, numerical stability, measured inference time |

These axes can be combined, but each added subsystem introduces overhead and failure modes. A system may, for example, retain a successful strategy in external memory, selectively adapt a small parameter adapter, and use a verifier to detect bad updates. This is a testable composition hypothesis, not a proven best design.

## Updated coverage assessment

This pass expands the registry on continual learning, memory consolidation, adaptive agent memory, test-time parameter updates, architecture evolution, and accelerated continuous-depth computation. It reinforces that “learning while deployed” can mean at least three different things: storing experience externally, changing model parameters, or changing the architecture.

No architecture is selected or ruled out. Continue broad search into remaining sequence/generation families, memory organization and consolidation, differentiable program execution, and compact learning algorithms. When repeated cycles stop adding materially new mechanisms, switch from taxonomy expansion to detailed equations, assumptions, complexity, and matched experiments.
