# Further research pass: equilibrium computation, associative memory, vector-symbolic algebra, and active inference

This pass follows the broad architecture survey rather than selecting a project direction. Several topics already appear in the taxonomy; this file records sharper distinctions and important 2025–2026 results, not a claim that every underlying family is new.

## 1. Deep Equilibrium Models (DEQs): depth represented by a fixed point

**Core idea:** instead of evaluating a fixed stack of layers, define a transformation whose hidden state is repeatedly updated until it approaches a fixed point:
\[
z^* = f_\theta(z^*, x).
\]
The model behaves like an implicitly deep network. Training can use implicit differentiation rather than storing every intermediate layer activation, so memory can be approximately independent of effective depth. The cost is solving the fixed-point problem, which may take many iterations or fail to converge.

Two 2026 directions address this cost:
- **Lipschitz Multiscale DEQ** structures the transformation to guarantee fixed-point convergence under chosen hyperparameters; the paper reports up to 4.75× speedup on CIFAR-10 with a minor accuracy reduction in its tested setup.
- **Consistency DEQ (C-DEQ)** distills the iterative trajectory so intermediate states can map to the fixed point in fewer steps; the paper reports better results than implicit DEQ under matched few-step inference budgets across its tested domains.

**What is new here:** not “infinite depth is free,” but explicit work on making the equilibrium solve converge faster and allowing a trade-off between iterations and quality.

**Limitations:** results are not language-model or old-CPU benchmarks. The fixed-point solver, vectorization, and convergence behavior determine real latency. Compare number of iterations, wall-clock time, memory, stability, and task quality.

Sources:
- Lipschitz Multiscale Deep Equilibrium Models (AISTATS/PMLR 2026): https://proceedings.mlr.press/v300/sato26a.html
- Consistency Deep Equilibrium Models (ICML/PMLR 2026): https://proceedings.mlr.press/v306/lin26w.html
- Permutation-equivariant DEQ for real-time resource dispatch (2026 domain-specific study): https://link.springer.com/article/10.1186/s44147-026-01192-3

## 2. Energy-based models (EBMs): score configurations instead of directly emitting answers

An energy-based model assigns a scalar energy \(E_\theta(x,y)\) to a configuration. In inference, it can rank candidates or search for low-energy configurations; in generative settings it may use sampling methods such as Langevin dynamics or Markov-chain Monte Carlo. This differs from an autoregressive model that directly parameterizes a next-token distribution, though energy-based components can be combined with other architectures.

A 2026 preprint, **Equilibrium Training of Energy-Based Models with Parallel Trajectory Tempering**, proposes a training procedure intended to improve sampling across multimodal energy landscapes. Its reported results are on Restricted Boltzmann Machines and scientific-data settings, not open-ended language reasoning.

**Why keep it distinct:** energy-based inference is a general modeling paradigm; DEQ is a fixed-point architecture pattern. A DEQ need not be an EBM, and an EBM need not use a DEQ.

**Main risks:** inference may require expensive iterative sampling, mixing may be poor, and energy values do not automatically provide calibrated probabilities. Measure samples/iterations per answer and total latency, not just parameter count.

Sources:
- Energy-Based Models: Representation, Inference, and Composition (research collection): https://energy-based-model.github.io/
- Equilibrium Training of Energy-Based Models with Parallel Trajectory Tempering (2026 preprint): https://arxiv.org/abs/2607.27077

## 3. Modern Hopfield memory: associative retrieval as energy minimization

Modern Hopfield networks extend classical associative memory to continuous-valued patterns and connect to attention-like retrieval. Given a query and stored patterns, the update retrieves or combines patterns according to an energy/scoring function. This is different from a generic external vector database: retrieval dynamics are part of the model and may be differentiable.

Recent directions:
- **Hopfield–Fenchel–Young Networks (JMLR 2025)** unify families of associative retrieval rules through generalized entropy and Fenchel–Young losses; variants can yield sparse retrieval and structured associations rather than only a single recalled pattern.
- **Hyperbolic Associative Memory Networks (ICML 2026)** move associative retrieval into negatively curved geometry, motivated by hierarchical data. The authors report hierarchy-dependent gains in their tested tasks, not universal superiority over Euclidean memory.
- A **Voronoi-based energy function (2026)** targets more stable recall across different stored-pattern distributions.

**Questions for later mechanics analysis:** what exactly is stored, how is the query matched, how does the update converge, how do interference and capacity scale, and what is the cost of updating memory? These mechanisms must be compared with kNN, fast weights, sparse distributed memory, and ordinary learned attention on the same recall tasks.

Sources:
- Hopfield-Fenchel-Young Networks, JMLR 2025: https://www.jmlr.org/papers/v26/24-1961.html
- Hyperbolic Associative Memory Networks, ICML 2026: https://proceedings.mlr.press/v306/hao26d.html
- Voronoi-based energy for stable associative memory (2026): https://www.jstage.jst.go.jp/article/nolta/17/2/17_571/_article/-char/en
- Uniform Memory Retrieval with Larger Capacity for Modern Hopfield Models, ICML 2024: https://proceedings.mlr.press/v235/wu24i.html

## 4. Vector Symbolic Architectures (VSA) / Hyperdimensional Computing (HDC): algebra over distributed high-dimensional symbols

VSA/HDC represents symbols, roles, relations, sequences, and structured objects with high-dimensional vectors and operations such as **binding**, **bundling**, **permutation**, and **similarity**. Different VSA algebras (e.g. Holographic Reduced Representations, Binary Spatter Codes, Multiply-Add-Permute) implement these operations differently. It is not a single neural-network architecture and should not be collapsed into “embeddings” or “graph vectors.”

A new 2026 paper applies VSA to ARC-style abstract reasoning through object-centric program synthesis:
- The published *Vector Symbolic Algebras for the Abstraction and Reasoning Corpus* reports 10.8% on ARC-AGI-1 train and 3.0% on ARC-AGI-1 evaluation, with much higher scores on simpler Sort-of-ARC and 1D-ARC tasks.
- A related 2026 conference version reports 13.5% train and 3.5% evaluation on ARC-AGI-1. These are distinct versions/setup results; do not merge the scores into one result.
- The system combines VSA object representations, a domain-specific program language, heuristic proposal, and explicit search. The result therefore tests a hybrid method, not pure VSA alone.

**Why this matters:** VSA offers an alternative representational/computational algebra that may encode structured relations compactly and tolerate noise. But high-dimensional vectors are not automatically small in RAM: dimensionality, numeric precision, number of stored items, and retrieval procedure matter. ARC evaluation remains limited; it is not evidence of general language competence.

Sources:
- Joffe & Eliasmith, *Vector Symbolic Algebras for the Abstraction and Reasoning Corpus*, npj Unconventional Computing (2026): https://www.nature.com/articles/s44335-026-00100-5
- Official implementation: https://github.com/ijoffe/ARC-VSA-2025
- 2026 NeSy conference version and reported setup: https://compneuro.uwaterloo.ca/publications/joffe2026a.html
- The blessing of dimensionality: VSA reasoning and learning (2025): https://doi.org/10.3233/NAI-240675
- Comprehensive HDC architectures survey (2026): https://doi.org/10.1016/j.rineng.2026.109589

## 5. Predictive coding and inference learning: inference dynamics may also be the learning algorithm

Predictive coding (PC) models use hierarchical predictions and prediction errors; internal states are iteratively adjusted to reduce discrepancies. Some predictive-coding networks propose local inference/learning rules as alternatives to ordinary end-to-end backpropagation. This must be separated from **Predictive State Representations**, which represent dynamical systems by predictions of future tests, and from **active inference**, which additionally addresses action and information-seeking.

Two 2026 surveys map the field:
- *Predictive Coding Networks and Inference Learning* describes predictive coding as hierarchical inference with feedback and reviews the relationship between inference learning and backpropagation.
- *A Survey on Neuro-mimetic Deep Learning via Predictive Coding* reviews algorithms, asynchronous computation, control/robotics applications, and open questions.

These surveys are useful taxonomic sources, not proof that predictive coding trains arbitrary deep language models as effectively or efficiently as backpropagation. The empirical status of predictive coding and active inference remains contested, with prior reviews finding only modest support for some broad neuroscience claims.

Sources:
- Predictive Coding Networks and Inference Learning: Tutorial and Survey (ACM Computing Surveys, 2026): https://doi.org/10.1145/3797870
- A Survey on Neuro-mimetic Deep Learning via Predictive Coding (Neural Networks, 2026): https://doi.org/10.1016/j.neunet.2025.108161
- Empirical status of predictive coding and active inference (2024 review): https://doi.org/10.1016/j.neubiorev.2023.105473

## 6. Active inference: learning a world model and selecting information-gathering actions

Active inference is a framework for agents that infer hidden states and model parameters, learn aspects of model structure, and choose policies partly according to expected free energy. In practical terms, an agent can choose an action not only for an immediate reward but also because the resulting observation may resolve uncertainty about how the world works.

A September 2026 *Nature Communications* paper, **Active inference and artificial reasoning**, formalizes information-seeking actions for structure/rule discovery in partially observed discrete models. It illustrates how a model can choose observations that distinguish competing hypotheses.

**What is distinct:** this is not simply predictive coding and not simply model-based reinforcement learning. It explicitly ties belief updating, learning about model structure, action selection, and expected information gain into one framework.

**Limits:** the paper's demonstrations use constrained discrete models; this is not a validated open-domain language architecture. Active inference introduces modeling and inference costs, and its empirical status should be compared with Bayesian experimental design and model-based RL on matched tasks.

Sources:
- Friston et al., *Active inference and artificial reasoning*, Nature Communications (8 September 2026): https://www.nature.com/articles/s41467-026-77209-5
- Reviewing Structure Learning in and Out of the Active Inference Framework (2026): https://link.springer.com/article/10.1007/s11023-026-09787-8
- Empirical status of predictive coding and active inference (2024 review): https://doi.org/10.1016/j.neubiorev.2023.105473

## 7. Neuro-symbolic integration is a design space, not one architecture

A 2026 systematic survey reviews 178 neuro-symbolic agentic AI papers from 2020 through November 2025, organizing integration patterns across knowledge representation, learning/inference, logic, explainability/trust, and metacognition. This is valuable as a map of integration choices, not a new computational primitive by itself.

For the taxonomy, keep separate:
- **representation:** symbols, graphs, VSA vectors, latent states, programs;
- **reasoning/inference:** deduction, search, probabilistic inference, energy minimization, fixed-point updates;
- **learning:** gradient descent, local inference learning, online adaptation, program/library learning, evolution;
- **control:** routing, planning, active information gathering, verification and stopping.

A hybrid can combine choices from these dimensions, but combining them also creates interface and debugging costs.

Source:
- Neuro-symbolic agentic AI: Architectures, integration patterns, applications, open challenges and future research directions (Computer Science Review, 2026): https://doi.org/10.1016/j.cosrev.2026.100902

## Updated coverage assessment

This pass strengthens the registry in four ways:
1. It distinguishes **fixed-point equilibrium networks** from **energy-based modeling**.
2. It distinguishes **associative memory retrieval** from ordinary external memory and from fast-weight sequence models.
3. It records recent VSA/HDC reasoning evidence while preserving the benchmark's limitations.
4. It separates **predictive coding**, **predictive state representations**, and **active inference** instead of treating them as synonyms.

The survey is still not complete. Next passes should target formal theorem-proving and differentiable logic systems, hypernetworks and parameter-generation, meta-learning families beyond the current examples, sparse distributed memory, local learning rules, and alternative sequence/generation models. No architecture is selected or preferred in this file.
