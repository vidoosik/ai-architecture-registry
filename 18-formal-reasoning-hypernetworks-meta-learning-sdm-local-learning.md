# Further research pass: formal reasoning, hypernetworks, meta-learning, sparse memory, and local learning

This pass expands the taxonomy across five mechanisms. It deliberately separates **reasoning/inference**, **parameter generation**, **adaptation**, **memory**, and **learning rules**. These are complementary design dimensions, not mutually exclusive model families. No candidate is selected.

## 1. Formal reasoning and differentiable logic

### 1.1 Three distinct families
- **Proof-theoretic systems** represent reasoning as proof construction or rule application. They can make derivations inspectable, but depend on the language-to-logic translation, rule coverage, and search control.
- **Model-theoretic systems** encode constraints and evaluate whether interpretations satisfy them. They can enforce global consistency constraints, but inference and optimization can become expensive.
- **Probabilistic / fuzzy neuro-symbolic systems** attach probabilities or graded truth values to facts and rules. They can integrate uncertain evidence, but a soft truth score is not the same as a formally verified proof.

A 2026 benchmarking paper groups neuro-symbolic systems into model-theoretic, proof-theoretic fuzzy, and proof-theoretic probabilistic categories, emphasizing that they have different strengths and benchmark assumptions. This is a useful taxonomy distinction, not a new single architecture.

### 1.2 Differentiable logic and constraint-guided learning
Differentiable logics replace Boolean operations or logical satisfaction with differentiable relaxations, allowing logical constraints to influence neural training. A 2025 comparison evaluates several differentiable logics and discusses how well they support learning constraints and formal guarantees. Important caveat: optimizing a soft surrogate does not automatically establish that the final system satisfies the original hard constraint; a separate verifier may still be needed.

### 1.3 LLM proposals plus formal proof checking
A 2026 IJCAI paper, *Large Lemma Miners: Can LLMs Do Induction Proofs for Hardware?*, combines language-model-generated candidate invariants with a symbolic tool that checks the inductive arguments. The authors report at least one successful provably correct inductive argument for 90% of their problem set after sufficient reprompting. This is a scoped hardware-verification result, not evidence that an LLM's unverified reasoning is generally correct.

**Design distinction:** a learned model can propose candidates; a symbolic engine can verify them. The generator and verifier have different roles and failure modes. For an assistant, this pattern generalizes conceptually to code, arithmetic, constraints, and structured plans, but each domain needs a suitable formal checker.

Sources:
- Benchmarking in Neuro-Symbolic AI (IJCLR/ILP proceedings, 2026): https://doi.org/10.1007/978-3-032-09087-4_17
- Comparing differentiable logics for learning with logical constraints (2025): https://doi.org/10.1016/j.scico.2025.103280
- Large Lemma Miners: Can LLMs Do Induction Proofs for Hardware? (IJCAI 2026): https://www.ijcai.org/proceedings/2026/444

## 2. Hypernetworks: generate parameters instead of storing one fixed parameter set

A hypernetwork \(H_\phi(c)\) generates some or all parameters \(\theta\) of a target model, conditioned on context/task/user/metadata \(c\). The target then computes \(f_{H_\phi(c)}(x)\). The hypernetwork and target model are trained jointly in the standard differentiable formulation.

Potential roles:
- Generate task-specific adapters or small parameter deltas rather than a complete model.
- Share a common parameter generator across related tasks, users, or environments.
- Produce a model conditioned on a continuous system parameter or context.
- Explore conditional computation and parameter sharing without maintaining a separately trained full model for every task.

A 2024 open review organizes hypernetworks by input/output characteristics, variability, and architecture. A 2026 study of parametric dynamical systems reports a hypernetwork-based model that generates weights from learned parameter embeddings and compares favorably with state augmentation in its tested scientific forecasting tasks.

**Limits and costs:** generating a large weight tensor may itself be expensive and memory-intensive; a hypernetwork can add compute rather than save it. Small generated adapters, low-rank deltas, or parameter subsets are different cost regimes from generating an entire network. Evidence from forecasting or personalized federated learning does not directly establish benefits for language reasoning.

Sources:
- A brief review of hypernetworks in deep learning (Artificial Intelligence Review, 2024): https://doi.org/10.1007/s10462-024-10862-8
- Beyond static models: Hypernetworks for adaptive and generalizable forecasting in complex parametric dynamical systems (2026): https://doi.org/10.1016/j.ymssp.2026.114635
- HierFedEHN: hierarchical training for hypernetwork-based personalized federated learning (2026): https://doi.org/10.1016/j.comnet.2026.112379

## 3. Meta-learning: learn how to adapt, not just one task

Meta-learning trains over a distribution of tasks so that a learner can adapt to a new task from a small amount of evidence. It is an umbrella family, not a single architecture.

Key mechanisms to keep distinct:
- **Optimization-based:** learn an initialization or update process that can be adapted with a few gradient steps (MAML-style methods).
- **Metric / embedding-based:** learn representations or similarity rules that make few-shot classification or retrieval possible.
- **Memory / recurrent meta-learners:** learn an internal update procedure that uses the sequence of examples as context.
- **Amortized or learned optimizers:** train a model to predict useful updates or solutions.
- **In-context adaptation:** the model adapts behavior from examples in its prompt without necessarily changing persistent parameters. This is related in purpose but is not identical to gradient-based meta-learning.
- **Task-structure-aware methods:** infer relations between tasks and accumulate reusable knowledge instead of treating every task as isolated.

A 2026 CVPR Findings paper, AdaMeta, models evolving relations between few-shot tasks and separates persistent meta-knowledge from transient task-specific signals. Its reported results are in few-shot classification and cross-domain benchmarks; they should not be generalized to arbitrary open-ended reasoning without tests.

**Critical evaluation:** distinguish (a) adaptation by updating weights, (b) adaptation through temporary context or memory, and (c) selecting/recombining existing skills. Measure examples and compute required to adapt, retained performance on earlier tasks, forgetting, and transfer to genuinely new task compositions.

Sources:
- A Comprehensive Review of Meta-Learning: From Theoretical Foundations to Practical Application (2026 preprint/review): https://papers.ssrn.com/sol3/Delivery.cfm/6290299.pdf?abstractid=6290299&mirid=1&type=2
- AdaMeta: Adaptive Meta-Learning with Dynamic Task Relational Inference for Few-shot Learning (CVPR Findings 2026): https://openaccess.thecvf.com/content/CVPR2026F/html/Yang_AdaMeta_Adaptive_Meta-Learning_with_Dynamic_Task_Relational_Inference_for_Few-shot_CVPRF_2026_paper.html
- Few-shot learning systematic review (Artificial Intelligence Review, 2026): https://doi.org/10.1007/s10462-026-11584-9

## 4. Sparse Distributed Memory (SDM): content-addressable recall in a large binary space

Kanerva-style SDM uses high-dimensional binary addresses and a set of distributed storage locations. A query activates locations within a Hamming-distance neighborhood; writes and reads are spread across those locations. The system can retrieve approximate contents from noisy or incomplete cues, with behavior shaped by address density, neighborhood radius, and interference.

Distinctive properties:
- Content-addressable and distributed rather than requiring an exact conventional address.
- Approximate recall can be robust to some noise.
- Capacity and fidelity depend on dimension, number of locations, threshold/radius, and stored pattern distribution.
- Interference and saturation are central concerns; the system does not provide unlimited reliable storage.

SDM overlaps with hyperdimensional computing/VSA but is not synonymous with it: VSA is a broader family of vector-symbolic representations and operations; SDM is a particular distributed memory organization and retrieval mechanism. It also differs from modern Hopfield networks, which use an energy/update rule for associative retrieval, and from an ordinary nearest-neighbor index, which typically returns stored records by explicit similarity search.

A 2025 CALM paper proposes a continual associative-learning framework combining SDM with lightweight dual-transformer modules and background consolidation. Treat this as a proposed integration architecture, not proof that SDM alone enables lifelong language learning.

**Potential experiments:** measure noisy-cue recall, false retrieval, capacity as memory fills, overwrite/forgetting, write cost, read latency, and RAM per stored association. Compare SDM, exact key-value memory, kNN, VSA/HDC, and Hopfield-style retrieval on the same tasks.

Sources:
- Kanerva-style SDM framework and resources: https://github.com/SparseDistributedMemory/SparseDistributedMemory
- Integer sparse distributed memory: Analysis and results: https://www.sciencedirect.com/science/article/abs/pii/S0893608013001354
- CALM: Continual Associative Learning Model via Sparse Distributed Memory (2025): https://www.mdpi.com/2227-7080/13/12/587
- Sparse distributed memory using N-of-M codes: https://www.sciencedirect.com/science/article/pii/S0893608004001443

## 5. Learning without standard global backpropagation

“Backpropagation-free” is not one method. The following approaches differ in what information is propagated, how local targets are formed, and what guarantees or empirical evidence exist.

### 5.1 Forward Projection (2026)
*Closed-form feedback-free learning with forward projection* proposes randomized nonlinear projections that set target pre-activation values from inputs and labels. Local losses are fit by closed-form regression in a single forward pass over the dataset, without downstream-to-upstream error communication. The paper reports results comparable to gradient-descent-based local-learning methods on several biomedical datasets and favorable few-shot results in its tested settings.

**Caveat:** the evidence is task- and architecture-specific. A single-pass training procedure does not imply cheap inference, and it is not established as a replacement for large-scale language-model training.

### 5.2 Equilibrium propagation
A dynamical network relaxes toward a free equilibrium; a small target-related perturbation (“nudging”) changes the equilibrium, and the difference can estimate parameter gradients using local quantities. The 2026 study *Equilibrium Propagation for Dissipative Dynamics* derives local rules for damped physical systems such as mechanical structures and RLC circuits.

**Caveat:** physical local learnability and efficient digital simulation are different claims. If the system must numerically solve two equilibria for every example, compute may outweigh the benefit.

### 5.3 Target propagation, feedback alignment, and predictive alignment
- **Target propagation** constructs target activations for earlier layers, often using approximate inverses or learned feedback mappings.
- **Feedback alignment** uses fixed or separately learned feedback weights rather than exact transposed forward weights.
- **Predictive alignment** uses local predictions and feedback to align recurrent dynamics.
- **Forward-Forward / PEPITA-like methods** use their own local objectives or altered forward passes; they should not be treated as equivalent to the methods above.

A 2025 paper studies predictive alignment for recurrent networks, while 2026 work explores target-based local learning for spiking recurrent networks. Such studies establish active research and scoped results, not general parity with backpropagation across arbitrary models.

Sources:
- Closed-form feedback-free learning with forward projection (Nature Communications, 2026): https://www.nature.com/articles/s41467-026-69161-1
- Equilibrium Propagation for Dissipative Dynamics (Advanced Intelligent Systems, 2026): https://doi.org/10.1002/aisy.202501310
- Directed Equilibrium Propagation Revisited (2025): https://www.mdpi.com/2227-7390/13/11/1866
- Taming the chaos gently: a predictive alignment learning rule in recurrent neural networks (2025): https://pmc.ncbi.nlm.nih.gov/articles/PMC12287340/
- Asymmetric Target Backpropagation Learning Algorithm in Deep Recurrent Spiking Neural Networks for Speech Recognition (IEEE, 2026): https://doi.org/10.1109/TCDS.2026.3732328

## 6. Cross-cutting comparison: keep the axes separate

| Mechanism | Primary question | Typical strength | Main unresolved cost/risk |
|---|---|---|---|
| Formal proof / symbolic solver | Is this derivation or candidate valid under explicit rules? | Checkable conclusions in a formal domain | Search and translation from natural language |
| Differentiable logic | Can constraints shape learning continuously? | Constraint-aware training | Soft satisfaction may not equal hard validity |
| Hypernetwork | Can parameters be generated from context? | Conditional specialization and sharing | Parameter-generation overhead |
| Meta-learning | Can a learner adapt quickly to new tasks? | Few-shot transfer | Adaptation cost, forgetting, task-distribution mismatch |
| Sparse Distributed Memory | Can a noisy cue retrieve a stored association? | Approximate content-addressable recall | Interference, capacity, threshold tuning |
| Local learning rules | Can parameters learn without conventional global gradients? | Local updates or physical learning | Scale, stability, and task-specific evidence |

These mechanisms may be combined, but each added interface creates costs. For example, a learned model could propose a formal candidate, a memory could retrieve relevant cases, and a verifier could check the candidate. That is a composition hypothesis to test, not a recommendation or established superior architecture.

## Updated coverage assessment

This pass adds source-backed distinctions and recent leads across formal verification, hypernetwork-generated parameters, task-level adaptation, SDM, and local learning. It also reinforces the need to separate model architecture from memory mechanism, training algorithm, and verification/control layer.

No architecture is selected or eliminated. Further broad search should continue into additional sequence/generation families, differentiable programming and theorem proving, continual learning and memory consolidation, and alternative optimization/inference methods. Once repeated passes stop yielding materially new mechanisms, the registry can move to detailed mathematical reconstruction and controlled comparisons.
