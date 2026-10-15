# Overlooked architectures and research leads

This file preserves the less mainstream ideas discussed during the research pass. Inclusion means “worth tracking,” not “recommended” or “proven to fit the target hardware.”

## Test-time memory and associative sequence models

### Test-time regression as a unifying view
A useful research lens treats attention, linear attention, state-space recurrence, and fast weights as different ways to store and retrieve associations from a sequence. The practical question is not simply which is “best,” but what memory update rule is learned, what information can be overwritten, how reads are computed, and how much state is carried at inference.

### Gated DeltaNet / Delta-rule fast weights
Delta-rule updates can correct an existing association instead of only accumulating new information. This is relevant to memory that must revise a stale association. Measure retrieval after interference, update stability, and CPU decode cost.

### DeltaStack
A stack-augmented linear-attention direction that adds push/pop-like structure. Potentially interesting for nested or hierarchical sequences. It is a research lead, not evidence that a general assistant can reason with a stack robustly.

### Titans and MIRAS
Titans (“Learning to Memorize at Test Time”) introduces a neural memory trained to update during inference; MIRAS provides a framework for analyzing memory design and update rules. Evaluate actual benchmark settings, model sizes, and inference requirements before extrapolating to CPU use.
- https://arxiv.org/abs/2501.00663
- https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/

### ATLAS and TNT
ATLAS studies how to optimally memorize context at test time. TNT studies chunkwise training for test-time memorization and reports up to 17× training acceleration against its most accurate baseline configuration in the reported setting. That figure is a **training** result, not a claim of 17× faster inference.
- https://proceedings.mlr.press/v306/behrouz26b.html
- https://proceedings.iclr.cc/paper_files/paper/2026/hash/bdb30687f1c2255c29b11b0b45204ebe-Abstract-Conference.html

### Memorizing Transformer and neural cache
Use a non-parametric key-value store over hidden representations to retrieve earlier examples or facts. Potentially helpful for facts/functions first encountered at inference time, but retrieval quality, index memory, false matches, and CPU lookup cost must be tested.
- https://arxiv.org/abs/2203.08913

## Hyperdimensional and vector-symbolic computation

### Hyperdimensional computing / Vector Symbolic Architectures (VSA)
Families include Holographic Reduced Representations (HRR), Binary Spatter Codes, MAP (Multiply-Add-Permute), SBDR, Tensor Product Representations (TPR), and Semantic Pointer Architecture. Typical operations include binding roles to fillers, bundling multiple items, and permutation/sequence encoding.

**Why investigate:** symbol-like operations can be compact and compositional, and some operations are simple vector arithmetic or bit operations.

**Risks:** approximate decoding, capacity/interference limits, and weak language grounding. Do not assume high-dimensional vectors automatically deliver exact logic. Test variable binding, role-filler retrieval, nested structures, and systematic generalization.

## Predictive coding and inference learning

Predictive coding networks represent predictions and residual errors and update internal states to reduce error. Inference-learning approaches explore local or iterative learning dynamics. These may offer an alternative perspective on inference and credit assignment, but iteration count, convergence, stability, and CPU cost are central.

A 2026 ACM Computing Surveys tutorial/survey is a useful entry point:
https://doi.org/10.1145/3797870

Related methods to track:
- **Forward-Forward:** layer-wise goodness objectives without ordinary end-to-end backpropagation.
- **Equilibrium propagation:** learning signals from differences between free and perturbed equilibria.
- **Active inference:** agents select actions partly to reduce expected uncertainty/free energy; distinguish formal theory from successful task-level implementations.
- **Predictive coding + spiking models:** a possible combination, not automatically efficient on ordinary CPU hardware.

## Continuous-time and liquid networks

- **Liquid Time-Constant Networks (LTC):** continuous-time recurrent dynamics with input-dependent effective time constants.
- **Neural Circuit Policies (NCP):** compact, structured recurrent policies inspired by nervous-system wiring.
- **Neural ODE/CDE:** continuous-depth/continuous-time dynamics. Adaptive numerical solvers can introduce variable runtime and solver overhead.
- **Spiking Neural Networks (SNNs):** event-driven units may fit neuromorphic hardware; software simulation on a conventional CPU may be slower than dense vectorized computation.

Best-fit research questions are temporal control, streaming sensor inputs, and adaptation to changing dynamics, rather than assuming they are drop-in language models.

## Equilibrium and self-organizing systems

- **Deep Equilibrium Models (DEQ):** define a block through a fixed point and solve for equilibrium rather than stacking a fixed number of distinct layers. Solver iterations and convergence determine runtime; fewer explicit layers do not guarantee lower latency.
- **Neural Cellular Automata (NCA):** local learned updates can generate, repair, or maintain patterns. A candidate for local state dynamics and self-organization, not a ready-made general reasoning system.
- **Neural GPU:** convolutional/recurrent-style architectures studied for algorithmic tasks such as generalizing computations to longer sequences. Treat as a lead for algorithmic generalization tests; verify original papers and modern relevance before prioritizing.

## KAN and learned transformations

- **KAN / FastKAN:** use learned spline-like functions or approximations in place of fixed scalar activation functions in parts of the network. Parameter efficiency claims are task- and implementation-dependent. Benchmark wall-clock CPU time and held-out compositional generalization, not parameter count alone.

## Tensor networks and structured matrices

### Tensor networks
Tensor Train, Matrix Product States (MPS), Matrix Product Operators (MPO), Tensor Ring, and Tucker decompositions represent high-order tensors through structured low-rank factors. They may reduce storage or compute, but rank controls the expressivity/cost trade-off and factorized operations can have overhead.

### Structured matrix families
Monarch, Butterfly, Toeplitz, Circulant, Kronecker products, and low-rank matrices constrain linear transformations to reduce parameters or operation count. They are potentially useful in compact layers or adapters; validate end-to-end quality, memory layout, and CPU kernel performance.

## Temporal knowledge graphs

Represent a time-sensitive fact as a relation with validity time, for example (subject, relation, object, valid time), and preferably also source, confidence, and context. This allows the system to distinguish “was true,” “is true,” and “unknown at that time.” Temporal graph embeddings predict likely links, but prediction scores should not be treated as explicit evidence.

Research leads:
- https://doi.org/10.1109/DSC67331.2025.00019
- https://doi.org/10.1016/j.knosys.2024.112454

## Learned index structures

A learned index predicts where a record lies in an ordered data structure. The original work reports large gains on certain datasets (including up to 70% faster operations and order-of-magnitude memory reduction in specific settings). This is an indexing technique, not semantic reasoning; it may help a large external fact store only if tested against a conventional index on the actual data.
- https://research.google/pubs/the-case-for-learned-index-structures/

## Continual and lifelong learning

The central problem is learning new information without erasing previously learned capabilities. Families include rehearsal/replay, regularization, parameter isolation, distillation, adapters, and memory-based approaches. There is no universally best method: performance depends on task sequence, storage budget, and what “forgetting” means.

Surveys:
- https://doi.org/10.1145/3716629
- https://doi.org/10.1109/TPAMI.2024.3367329
- https://www.sciencedirect.com/science/article/pii/S0893608025011074

## Other families to keep in the taxonomy

- **Conditional computation:** Switch-style and hierarchical Mixture of Experts (MoE), token-choice/expert-choice/top-k routing, adaptive computation time, early exit, layer skipping, specialist modules.
- **Convolutional sequence models:** WaveNet, TCN, ByteNet, depthwise/separable convolutions, multiresolution and wavelet mixing.
- **Equivariant networks:** preserve known transformation symmetries (including SE(3) for 3D geometry); useful when invariances are known.
- **Neural operators:** learn mappings between functions/fields, e.g. Fourier Neural Operator; aimed at scientific/physical systems rather than generic dialogue.
- **Capsule networks:** represent part-whole relationships and pose-like information; empirically compare maturity and cost with standard baselines.
- **Neuroevolution:** evolve weights or architectures; can optimize black-box objectives but search cost is often high.
- **Implicit neural representations:** represent signals/fields as coordinate-conditioned functions.
- **Neural cellular automata:** local rule-based learned dynamics (also discussed above).

## Source verification status

Some items above are taxonomy-level summaries. Before using any reported number in a design decision, open the original paper or official project page and record the exact task, dataset, hardware, parameter count, baseline, and whether the result concerns training, inference, memory, or accuracy.