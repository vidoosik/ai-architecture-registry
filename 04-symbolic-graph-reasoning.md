# Symbolic, graph, neuro-symbolic, and structured reasoning

## Explicit symbolic systems

- **Production systems / rule engines:** match facts to rules and derive conclusions. Transparent and testable; brittle if language grounding and rule coverage are poor.
- **Propositional logic / first-order logic:** explicit truth conditions and quantification; requires controlled semantics and handling of incomplete information.
- **Datalog / Prolog:** relations, unification, and rule chaining; good for exact symbolic tasks when facts and parsing are reliable.
- **SAT / SMT solvers:** search for satisfying assignments or theory-consistent solutions; useful as verifiers/planners, not natural-language generators.
- **Description logics / ontologies:** class and relation constraints; useful for consistency and type checking.
- **Inductive Logic Programming (ILP):** learns rules from examples; rule search and data efficiency are key trade-offs.
- **Probabilistic / Markov logic:** attach uncertainty or weights to logical relations; inference may become expensive.
- **Differentiable logic / Logic Tensor Networks / DeepProbLog:** combine neural learning with soft or probabilistic logic; need calibration and robust generalization.

## Graph neural networks and relational learning

- **GCN / GraphSAGE / GAT / GIN:** common GNN aggregation designs.
- **MPNN:** message-passing framework for entities connected by relations.
- **R-GCN:** relation-specific transformations for multi-relational knowledge graphs.
- **Graph Transformers:** attention over nodes/edges; may be expressive but can be memory intensive.
- **Neural Theorem Provers:** learnable soft unification and rule reasoning.
- **Temporal knowledge graphs:** represent time-varying facts, often as (head, relation, tail, time); need explicit validity intervals and provenance.
- **Knowledge graph embeddings:** learn vectors for entities and relations; useful for link prediction, but a plausible score is not the same as an explicit proof.

## Representations beyond ordinary graphs

- **Tensor Product Representations (TPR):** bind roles and fillers into structured vector representations.
- **VSA / Hyperdimensional Computing:** compositional symbol operations using binding and bundling.
- **Semantic Pointer Architecture:** vector-symbolic representation with structured roles and relations.
- **Neural Turing Machine / DNC:** differentiable memory addressing for learned read/write operations.
- **Learned index structures:** neural mappings to accelerate record location in sorted data; potentially useful for storage lookup, but not semantic reasoning. Original Google Research paper: https://research.google/pubs/the-case-for-learned-index-structures/

## A graph-world representation hypothesis

For each entity, store only context-relevant attributes rather than a huge flat feature vector. Relations should be typed and directional. A fact record may contain:
- subject/entity, relation, object/value;
- context and time/validity;
- source/provenance;
- confidence/status: asserted, inferred, unknown, disputed, retracted;
- conditions and exceptions;
- support links to facts/rules used in an inference.

Inference should preserve a derivation trace. Exceptions should suppress defaults only in the relevant context; removing an exception should restore the default conclusion. Competing causes should remain competing hypotheses until evidence distinguishes them.

## Important caveat

A graph with hand-written rules can pass designed tests without having broad intelligence. The key tests are unseen compositions, paraphrased inputs, novel entity names, conflicting evidence, exception removal, counterfactuals, and explanations whose premises can be checked. A learned model is still needed for robust language understanding and flexible generation unless that capability is independently demonstrated.