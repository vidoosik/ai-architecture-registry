# Further architecture leads: diffusion language models, reservoirs, spiking prediction, and adaptive compute

This pass deliberately broadens the survey beyond symbolic/graph approaches. It separates **model architecture**, **dynamics/training paradigm**, and **inference-time control**. These are not interchangeable categories, and none is selected as the project's preferred direction.

## 1. Discrete diffusion language models: a distinct generation paradigm

Autoregressive language models generate tokens in a fixed causal order. Discrete diffusion language models instead learn to recover partially corrupted token sequences over iterative denoising steps. Depending on the corruption process and sampler, they may revise tokens, generate in different orders, fill gaps bidirectionally, or update multiple positions in parallel.

Recent 2025–2026 work shows that this is not one settled architecture:
- **Generalized interpolating discrete diffusion (GIDD)** studies corruption processes beyond simple masking and targets the inability of masked diffusion to revise already generated words.
- **OeMDM / LoMDM** formalize different generation orders and jointly learn an ordering policy with the denoising backbone.
- **XDLM** connects masked and uniform-noise diffusion through a stationary noise kernel and studies the trade-off between understanding and generation.
- **Scaling Beyond Masked Diffusion Language Models** compares discrete diffusion families at scale and warns that perplexity alone can mis-rank methods when sampling cost and task quality differ.
- **Consistent Diffusion Language Models (CDLM)** investigate consistency training to reduce the many refinement steps that otherwise limit practical speed.

**Why include it:** diffusion changes the generation process itself, not merely the attention layer. It is a serious alternative to study for infilling, revision, parallel generation, and controllability.

**Limits / questions to preserve:** parallel token updates do not automatically mean faster wall-clock inference; each refinement step still costs computation, and quality can lag a matched autoregressive model. Compare total latency, number of denoising steps, RAM, output quality, and CPU kernel efficiency at matched scale.

Sources:
- GIDD, ICML 2025: https://proceedings.mlr.press/v267/von-rutte25a.html
- OeMDM / LoMDM, ICML 2026: https://proceedings.mlr.press/v306/hong26f.html
- XDLM, ICML 2026: https://proceedings.mlr.press/v306/liu26gi.html
- Scaling Beyond Masked Diffusion Language Models, ICML 2026: https://proceedings.mlr.press/v306/sahoo26a.html
- Consistent Diffusion Language Models, ICML 2026: https://proceedings.mlr.press/v306/amin26a.html
- Overview of discrete diffusion models in NLP (2026 review): https://link.springer.com/article/10.1007/s44336-026-00040-5

## 2. Reservoir computing: fixed recurrent dynamics, trained readout

**Core idea:** a recurrent dynamical system (the reservoir) transforms an input history into a rich evolving state; usually only a comparatively small readout layer is trained. Echo State Networks (ESNs) use continuous-valued dynamics; Liquid State Machines (LSMs) use spiking dynamics. Physical reservoir computing uses the dynamics of electronic, optical, mechanical, or other physical systems as the reservoir.

This differs from training a conventional RNN end-to-end, from SSMs with learned state-space parameters, and from NCA's local self-organizing updates. Its appeal is cheap training and temporal feature extraction; its limitation is that fixed reservoir dynamics may constrain what can be learned.

**Relevance to examine, not assume:** lightweight sequence processing, online adaptation, and compact temporal memory. Evidence is strongest in temporal/signal tasks, not open-domain language generation. Ask whether the reservoir can represent compositional symbolic operations, not just predict a time series.

Sources:
- Physical reservoir computing tutorial, *Natural Computing* (2024): https://doi.org/10.1007/s11047-024-09997-y
- Physical reservoir computing with emerging electronics, *Nature Electronics* (2024): https://www.nature.com/articles/s41928-024-01133-z
- Reservoir Computing: Foundations, Advances, and Challenges (2026 review): https://www.mdpi.com/2673-2688/7/2/70
- Emerging opportunities and challenges for reservoir computing, *Nature Communications* (2024): https://doi.org/10.1038/s41467-024-45187-1

## 3. Spiking predictive coding: event-driven neural dynamics plus prediction errors

This family combines two concepts that must remain distinct:
- **Spiking neural networks (SNNs):** neurons communicate via discrete events/spikes rather than only continuous activations.
- **Predictive coding:** internal states or neural populations encode predictions and prediction errors, with iterative updates intended to reduce discrepancies.

The 2026 survey organizes spiking predictive-coding approaches by where/how prediction errors are represented: explicit error-neuron populations, membrane potentials, or implicit encodings. It also discusses neuromorphic implementations and emphasizes that energy-efficiency claims remain debated.

**Why include it:** it explores asynchronous/event-driven computation and a different account of perception and iterative inference. It is not simply another small dense neural network.

**Limits:** hardware, simulator overhead, training rules, latency, and accuracy matter. A sparse stream of spikes does not guarantee energy or time savings on an ordinary CPU, and this literature does not establish a drop-in text-reasoning architecture.

Source:
- Predictive coding with spiking neural networks: A survey, *Neural Networks* (2026): https://doi.org/10.1016/j.neunet.2025.108371

## 4. Adaptive test-time computation is a control axis, not a base architecture

A fixed model can spend different amounts of computation per question by routing to tools/models, generating multiple candidate trajectories, verifying intermediate steps, expanding or pruning search, or stopping early when evidence is sufficient. This is orthogonal to whether the underlying model is a Transformer, SSM, diffusion model, or another family.

Recent 2026 papers provide concrete variants:
- **Verifier-guided adaptive allocation:** a process reward model guides pruning/expansion across reasoning trajectories and selects the final result.
- **Bandit-based allocation:** estimates query difficulty and directs more compute to challenging but solvable questions.
- **Evolving in-context demonstrations:** uses successful examples from related queries to change subsequent attempts rather than repeatedly sampling from a fixed distribution.

These results support the idea that compute allocation itself can be learned. They do not show that adaptive search is free: candidate generation, tool overhead, and verifier errors must be included in total cost.

Sources:
- What If We Allocate Test-Time Compute Adaptively? (ICML 2026): https://proceedings.mlr.press/v306/bilal26a.html
- Strategic Scaling of Test-Time Compute: A Bandit Learning Approach (ICLR 2026): https://proceedings.iclr.cc/paper_files/paper/2026/hash/d8d2c5f79c53a2a685dbdf93f78c5695-Abstract-Conference.html
- Adaptive Test-Time Compute Allocation with Evolving In-Context Demonstrations (ACL Findings 2026): https://aclanthology.org/2026.findings-acl.1754/
- Adaptive Computation in the LLM Era: A Unified Survey (2026 working paper; not peer reviewed): https://client.prod.orp.cambridge.org/engage/coe/article-details/6a2fa5614770e67d92f5bb0c

## 5. Neuroevolution is broader than evolutionary program synthesis

Evolution can optimize neural-network weights, topology, learning rules, modularity, or behavior. This includes neuroevolution methods such as topology-and-weight evolution, novelty search, and quality-diversity approaches; it is distinct from evolving executable programs (DreamCoder, SOAR, AlphaEvolve) even when the search loop looks similar.

A 2025 review frames neuroevolution as optimizing neural architectures and/or parameters under environmental constraints, and identifies open research opportunities. This is a broad method family rather than one specific architecture.

**Questions for later analysis:** what is evolved (weights, topology, modules, learning rule, or program)? What is the fitness signal? Is the objective single-score optimization or a diverse archive of behaviors? How expensive is evaluation, and how does the result generalize beyond the fitness cases?

**Limits:** evolutionary search can require many evaluations and overfit its evaluator. It should be compared against gradient-based and simple random-search baselines on the same compute budget.

Source:
- Neuroevolution insights into biological neural computation (2025 review): https://pmc.ncbi.nlm.nih.gov/articles/PMC12238971/

## 6. Current conclusion: the search is not exhausted

These leads add several meaningfully distinct axes:
1. **Generation dynamics:** autoregressive versus discrete diffusion and other non-autoregressive methods.
2. **Fixed versus learned recurrent dynamics:** reservoir computing versus end-to-end recurrent/state-space models.
3. **Event-driven computation:** spiking systems and neuromorphic execution.
4. **Inference control:** adaptive allocation of computation, which can sit on top of different model families.
5. **Search over the learner itself:** neuroevolution of topology, weights, modules, or learning rules, distinct from program synthesis.

Some topics overlap with existing files (predictive coding, recurrent models, and evolutionary synthesis), so this file records the sharper distinctions and recent evidence rather than pretending every item is wholly new. No family is ranked as the winner. The next research passes should continue covering energy-based/equilibrium computation, associative memory, differentiable logic and theorem proving, hypernetworks, meta-learning, active inference, and learning rules that avoid standard backpropagation. After coverage broadens further, study each candidate's mechanics and equations before comparing them.
