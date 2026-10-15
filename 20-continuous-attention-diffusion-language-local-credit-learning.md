# Further research pass: continuous-time attention, diffusion language generation, and local-credit learning

This pass adds three mechanisms that are not interchangeable with the architecture families already catalogued: continuous-time sparse attention, structured dependencies in discrete diffusion generation, and learning algorithms that change how credit is assigned across a network. The registry remains exploratory; none is selected as the preferred design.

## 1. Neuronal Attention Circuit (NAC): attention as adaptive continuous-time dynamics

The 2025 preprint / 2026 publication *Neuronal Attention Circuit for Representation Learning* reformulates attention-logit computation as a first-order linear ODE with nonlinear, interlinked gates. It uses sparse sensory gates for query/key projections and a sparse backbone with content-target and learnable time-constant gates. A Top-K pairwise interaction mechanism selects which query-key interactions to compute.

The authors report stability and bounded-approximation guarantees, with experiments on irregular time series, long-range forecasting, autonomous-vehicle lane keeping, and industrial prognostics.

**What is actually new:** attention scores are not simply a static dense dot product; they are produced by a gated dynamical system with learnable time constants and sparse connectivity.

**Limits:** the evidence is primarily for time-series/control tasks, not open-ended language generation or compositional symbolic reasoning. ODE integration, Top-K selection, and sparse operations can add overhead; theoretical sparsity does not guarantee faster CPU execution. A language-model claim would require a separate implementation and benchmark.

Source:
- Neuronal Attention Circuit for Representation Learning: https://arxiv.org/abs/2512.10282
- Published record / abstract: https://pubmed.ncbi.nlm.nih.gov/42600494/

## 2. Discrete diffusion language models: reducing refinement steps and restoring token dependencies

Autoregressive language models generate tokens sequentially. Discrete diffusion language models can refine many masked/corrupted tokens in parallel, but they often require many refinement steps and can lose coherence when simultaneous token predictions are treated as independent.

### 2.1 Consistent Diffusion Language Models (CDLM / MPDC, ICML 2026)

Multi-Path Discrete Consistency trains a denoiser to be approximately path-invariant in expectation across exact posterior bridges between noise levels. The proposed single-stage framework does not require an already trained teacher. Its goal is to make discrete diffusion generation more consistent across refinement paths.

### 2.2 Coupled Discrete Diffusion (CoDD, ICML 2026)

CoDD targets the diffusion “factorization barrier”: if all tokens predicted in parallel are modeled independently, dependencies between them are weakened; explicitly predicting a full joint distribution is prohibitively expensive. CoDD adds a lightweight tractable probabilistic inference layer to represent dependencies without requiring the backbone to output an enormous joint distribution.

### 2.3 Product-of-Experts Bridge (PoE-Bridge, ICML 2026)

PoE-Bridge combines a diffusion model's parallel drafts with an autoregressive target distribution through an intermediate product-of-experts distribution and rejection sampling. It is a hybrid decoding strategy, not a new standalone backbone.

**Comparison questions:** count actual refinement passes, wall-clock latency, memory, and quality at the same hardware and output length. Distinguish parallel proposal generation from the cost of correction, sampling, or verification. Parallel token generation is not automatically faster end-to-end.

Sources:
- Consistent Diffusion Language Models: https://proceedings.mlr.press/v306/amin26a.html
- Breaking the Factorization Barrier in Diffusion Language Models (CoDD): https://proceedings.mlr.press/v306/li26fd.html
- Diffusion Language Model Parallel Decoding via Product-of-Experts Bridge: https://proceedings.mlr.press/v306/shi26v.html

## 3. Local-credit learning: NoProp and improved Forward-Forward methods

Standard backpropagation transports error information through the network and usually requires storing intermediate activations. Several alternatives change the credit-assignment mechanism; they should not be grouped as if they were one algorithm.

### 3.1 NoProp (CoLLAs 2026)

NoProp trains blocks without forward or backward propagation across the entire network. Each block independently learns to denoise a noisy version of a fixed target representation, using local backpropagation within that block. The paper reports experiments on MNIST, CIFAR-10, and CIFAR-100.

The distinctive feature is not merely “no backprop”: it removes inter-block forward/backward credit assignment during training and instead supplies fixed noisy target representations to local denoisers.

**Major caveat:** fixing target representations beforehand is a strong design assumption. Reported image-classification results do not establish a route to language understanding, generative dialogue, or reasoning.

### 3.2 Distance-Forward (DF, Neural Networks 2026)

DF reformulates Forward-Forward using distance-metric learning, centroid-based representations, and a goodness-based N-pair margin loss. It also introduces layer-collaboration updates intended to reduce information loss from greedy local learning. The paper reports less than 40% of backpropagation's memory cost in its evaluated settings and tests an extension to spiking neural networks.

This is a reported result on eight datasets, not a general guarantee for every architecture or hardware target.

### 3.3 Local self-supervised learning and forward-only methods

An ICML 2026 study derives conditions under which local self-supervised learning rules can reproduce global backpropagation updates in deep linear networks, then proposes nonlinear variants tested on image datasets. A 2026 Nature Communications paper, Forward Projection, explores a closed-form randomized learning procedure using one forward pass over the dataset without retrograde output feedback.

These methods differ in what information is allowed to travel backward, whether local gradients remain, and whether optimization is iterative or closed-form. Those details determine whether they are practical.

Sources:
- NoProp: Training Neural Networks without Back-propagation or Forward-propagation (CoLLAs 2026): https://proceedings.mlr.press/v330/li26a.html
- Advancing the Forward-Forward Algorithm towards High-Performance Deep Local Learning (2026): https://doi.org/10.1016/j.neunet.2026.108765
- Can Local Learning Match Self-Supervised Backpropagation? (ICML 2026): https://proceedings.mlr.press/v306/zihan26a.html
- Closed-form Feedback-free Learning with Forward Projection (Nature Communications, 2026): https://www.nature.com/articles/s41467-026-69161-1

## 4. Cross-family implications and test plan

| Mechanism | Main computation changed | Potential advantage | Critical unknown |
|---|---|---|---|
| NAC | Dense/static attention scores become sparse gated dynamics | Adaptive temporal processing and fewer selected interactions | Language quality and actual CPU latency |
| CDLM / MPDC | Discrete diffusion training across corruption/noise paths | Fewer or more consistent refinement steps | End-to-end speed and long-form coherence |
| CoDD | Factorized parallel-token output gains a tractable coupling layer | Preserve token dependencies while drafting in parallel | Coupling overhead and scaling |
| PoE-Bridge | Diffusion proposals are corrected toward an autoregressive target | Parallel drafts with stronger dependency control | Rejection rate and verification cost |
| NoProp | Global inter-layer credit assignment becomes local denoising objectives | Less cross-layer activation/gradient traffic | Target representation assumptions and task transfer |
| Distance-Forward | Local goodness learning becomes metric/centroid-based | Lower reported training memory and improved local learning | Scaling to language and long-horizon reasoning |

For every candidate, record the exact task, training regime, parameter count, hardware, peak RAM, throughput, and baseline. For generation, compare quality at equal wall-clock time and equal compute budget; for learning rules, compare total training cost and held-out generalization, not memory alone.

## Coverage update

This pass adds newer continuous-time sparse attention, three 2026 discrete-diffusion generation mechanisms, and more specific alternatives to global backpropagation. NLI itself is already documented in file 15 and is not duplicated here. Continue the search for distinct mechanisms rather than repeatedly listing model names; once successive passes stop adding new mechanism families, move to equations, assumptions, complexity analysis, and matched tests.

