# Learning, world models, energy, and alternative computation

## Predictive and world-model approaches

- **Autoencoders / variational autoencoders (VAE):** learn compressed latent representations; reconstruction or likelihood quality does not guarantee reasoning.
- **Contrastive learning:** shape representations by pulling related examples together and separating negatives.
- **Masked autoencoders (MAE):** reconstruct masked portions from context.
- **JEPA (Joint-Embedding Predictive Architecture):** predict representations of missing/future information rather than necessarily reconstruct raw input.
- **Predictive coding networks:** represent prediction errors and iteratively update latent states; potential route to iterative inference, with compute/convergence questions.
- **Inference learning:** train local prediction/inference dynamics rather than relying only on standard end-to-end backpropagation.
- **World models:** learn latent dynamics and use them for prediction/planning; useful for agents, but model errors can compound.
- **Object-centric/slot representations:** represent scenes as interacting entities instead of undifferentiated vectors.
- **Causal graphs / structural causal models:** represent intervention and cause-effect relations, unlike mere correlations.

## Energy-based models

- **Energy-Based Models (EBMs):** assign lower energy to compatible configurations; inference searches for low-energy states. Flexible but sampling/training and normalization can be difficult.
- **Boltzmann machines / RBMs:** stochastic energy-based networks; foundational but often expensive to train or sample.
- **Score-based models / diffusion:** learn score or denoising dynamics; powerful generative approach but typically many iterative steps unless distilled/accelerated.
- **Hopfield energy formulations:** associative retrieval can be understood through an energy landscape; avoid assuming every retrieval problem needs iterative settling.
- **Equilibrium models / Deep Equilibrium Models:** define a layer as a fixed point of a transformation, reducing explicit depth but requiring convergence/solver work. Deep equilibrium does not automatically mean lower latency.
- **Neural ODE/CDE:** continuous-depth or continuous-time dynamics; adaptive solvers can help some tasks but may add runtime overhead.

## Learning rules and alternatives to backpropagation

- **Hebbian learning / Oja's rule:** local co-activation-based updates; attractive for online adaptation, but not a general substitute for modern supervised training.
- **Equilibrium propagation:** uses differences between free and nudged equilibria to estimate learning signals.
- **Forward-Forward:** trains using forward-pass goodness measures rather than conventional backpropagation; empirical trade-offs remain task dependent.
- **Spiking neural networks (SNNs):** event-driven neuron models may be efficient on suitable neuromorphic hardware, but software simulation on a conventional CPU is not automatically faster.
- **Neural Cellular Automata:** local learned update rules can produce growth, repair, or self-organization; useful research for local dynamics, not a ready-made language core.
- **Liquid Time-Constant Networks / Neural Circuit Policies:** continuous-time adaptive dynamics, often studied for control and compact policies.
- **Reservoir computing:** fixed dynamics plus a trained readout; cheap baseline for temporal tasks.

## Other model-building ideas

- **Hypernetworks:** one network generates weights or parameters for another; adds flexibility but may shift rather than eliminate compute.
- **Meta-learning:** train systems to adapt rapidly to new tasks/examples; adaptation speed and transfer must be measured.
- **Neural program synthesis / algorithmic reasoning:** learn executable procedures or algorithmic patterns; evaluate on longer/unseen instances.
- **Differentiable programming:** combine learned modules with structured computation.
- **Tensor networks:** Tensor Train, Matrix Product States/Operators, Tensor Ring, Tucker; structured factorization can reduce parameter count, but expressivity and inference speed depend on rank and implementation.
- **Structured matrices:** Monarch, Butterfly, Toeplitz, Circulant, Kronecker and low-rank transforms can reduce parameter or operation costs in suitable layers.
- **KAN / FastKAN:** learned spline-like activation functions; investigate actual end-to-end quality and runtime, not just parameter count.
- **Capsule networks / equivariant networks:** structured representations of parts, pose, and transformations; often more expensive or less mature than mainstream baselines.

## Priority hypotheses

- Predictive or object-centric state may help a compact system track entities and situations.
- Energy minimization may help reconcile inconsistent candidate interpretations, but iterative optimization can be too slow for CPU dialogue.
- Local learning may support incremental updates, but must be tested for stability, forgetting, and quality.
- Structured matrices/tensor networks may reduce model size; compression alone does not guarantee fast inference.