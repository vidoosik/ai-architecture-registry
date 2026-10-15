# Further research pass: depth-progressive local learning, randomized KANs, and typed program synthesis

This pass follows up on three distinct design questions: can layers learn without global gradient coordination; can a model be built from explicit functional components with a different training algorithm; and can abstract reasoning be framed as synthesis of a typed executable program? These mechanisms are not interchangeable, and none is selected as the preferred architecture.

## 1. Depth-Progressive Monotonic Learning (DMoL)

DMoL replaces globally coordinated backpropagation with layer-wise local belief objectives, progressively refining them across network depth. Its design targets the update-locking problem: in ordinary backpropagation, a layer's update depends on the forward activations and backward error signal of the whole network. DMoL claims monotonic improvement of local objectives as depth increases and supports dynamically modifying depth during training.

The ICML 2026 paper reports results across several tasks, including a 4.3% accuracy gain on CIFAR-100, a 37.5% improvement on Cora under its graph-smoothing metric, and more than 35% reduction in final loss for its tested diffusion-model setup.

**What this contributes:** local credit assignment and flexible depth are coupled. It is more than a memory-saving tweak to backpropagation; it changes how layer objectives are coordinated.

**Limits:** benchmark-specific gains cannot be assumed to transfer to language modelling. A fair assessment must include training compute, final model quality, stability, and inference cost; avoiding global gradients does not itself make inference cheaper.

Source:
- Depth-Progressive Monotonic Learning without Global Backpropagation (ICML 2026): https://proceedings.mlr.press/v306/ye26q.html
- Code: https://github.com/ychAlbert/DMoL

## 2. Hierarchical Kolmogorov-Arnold Network without backpropagation (HKAN)

HKAN is a hierarchical KAN-related approximation model trained with randomized learning and a sequence of convex optimization subproblems. Rather than using standard iterative gradient backpropagation, each stacking stage refines the approximation produced by the previous stage.

**Distinctive mechanism:** the functional architecture is paired with a non-backpropagation fitting procedure. This separates two questions often conflated in architecture comparisons: what function family the model represents, and how its parameters are found.

**Potential relevance:** deterministic or randomized fitting procedures may be useful where repeated gradient optimization is too expensive or difficult to deploy. But approximation accuracy on the tested tasks does not establish broad language competence, compositional reasoning, or competitive memory/latency on a CPU.

Source:
- HKAN: Hierarchical Kolmogorov-Arnold network without backpropagation (Neural Networks, August 2026): https://doi.org/10.1016/j.neunet.2026.108796

## 3. TransCoder: abstract reasoning via typed program synthesis

TransCoder frames abstract problem solving as neural program synthesis over a typed domain-specific language (DSL). The generative component proposes candidate programs in a constrained language; the program's structure is intended to express reusable operations rather than only an unstructured answer string.

**Why it matters:** a typed DSL can make some invalid solutions unrepresentable, expose the structure of a proposed solution, and support execution-based checking. The challenge moves to choosing the DSL primitives, grounding observations into symbols, searching the program space, and avoiding brittle hand-designed task assumptions.

**Key distinction:** this is not the same as a neural network learning all concepts from raw data, nor is it equivalent to a general-purpose theorem prover. A typed DSL is an inductive bias: it can help if its primitives fit the task, and limit transfer if they do not.

Evaluation should include held-out rule compositions, new surface forms, changed object identities, distractors, counterfactuals, and execution traces. Compare with a non-synthesizing baseline under equal search/compute budgets.

Source:
- Solving Abstract Reasoning Problems With Neurosymbolic Program Synthesis and Task Generation (2026): https://doi.org/10.1177/29498732261419309

## 4. Related backpropagation-free graph learning

A 2026 paper on boosting-based backpropagation-free graph neural networks proposes local neuron-level learning in GNNs. It is worth tracking separately from DMoL: DMoL progressively refines layer-local objectives, while boosting-based graph learning changes the training strategy for graph representations.

This line could be relevant to relational data, but graph-task success does not establish that a system can parse ordinary language, choose the right graph representation, or reason robustly over unseen combinations. The parser/grounding problem and the inference mechanism must be evaluated separately.

Source:
- Local Learning with Boosting-based Backpropagation-Free Graph Neural Networks (Machine Learning, 2 May 2026): https://doi.org/10.1007/s10994-026-07041-x

## 5. Mechanism comparison

| Family | Representation / computation | Learning or search mechanism | Main uncertainty |
|---|---|---|---|
| DMoL | Deep layered network with local belief objectives | Progressive layer-wise refinement; no global BP | Whether local objectives preserve useful language representations at scale |
| HKAN | Hierarchical KAN-related function approximator | Randomized learning and convex subproblems | Generalization, architecture scaling, and inference cost |
| TransCoder | Typed executable programs in a DSL | Neural proposal / program synthesis | DSL coverage, search cost, grounding, and out-of-distribution transfer |
| Boosting-based BF-GNN | Graph neural representations | Local neuron-level learning with boosting | Whether the gains extend beyond graph benchmarks and to language-grounded reasoning |

## 6. What to measure next

For learning-algorithm candidates, measure training wall time, peak RAM, number of passes over data, scaling with depth, stability across seeds, and held-out generalization. For program synthesis, separately measure candidate-generation quality, search expansions, execution/verification cost, and success on unseen compositions. For every result, preserve the paper's task and baseline; do not convert an image or graph benchmark into a claim about general intelligence.

## Coverage update

This pass adds depth-progressive local learning, a randomized non-backpropagation KAN variant, typed-DSL abstract reasoning, and a distinct local-learning route for GNNs. It does not replace earlier entries on NoProp, Distance-Forward, Forward Projection, or neural language interpreters; those methods change different parts of the learning or reasoning pipeline. No architecture is selected or ruled out.
