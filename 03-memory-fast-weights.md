# Memory, fast weights, and test-time learning

## Memory types

- **External symbolic memory / knowledge graph:** exact records, relations, timestamps, confidence, and provenance; requires robust parsing, retrieval, and conflict policy.
- **Vector retrieval / RAG:** retrieves semantically similar chunks; good for text recall, less exact for multi-hop logic unless paired with structured records.
- **kNN-LM / Neural Cache:** stores key-value pairs from hidden representations and retrieves nearby examples at inference; approximate matching and index cost are central.
- **Memorizing Transformer:** adds retrieval over a large memory of key-value representations.
- **Neural Turing Machine / Differentiable Neural Computer:** differentiable read/write access to external memory; expressive but training and implementation can be complex.
- **Recurrent Memory Transformer (RMT):** passes memory tokens between segments.
- **Compressive Transformer:** compresses older activations to keep a longer history within a budget.
- **Hopfield networks / modern Hopfield layers:** associative retrieval dynamics; modern formulations connect to attention-like operations.
- **Fast weights:** temporary rapidly updated parameters or matrices used as short-term associative memory.
- **Hyperdimensional computing / Vector Symbolic Architectures (VSA):** high-dimensional distributed symbol vectors combined by binding, bundling, and permutation; attractive for compact symbolic-like operations, but compositional reasoning and precision must be benchmarked.

## Test-time learning and memory-update research

- **Titans — “Learning to Memorize at Test Time” (2025):** neural long-term memory module trained to update at test time; paper reports benefits on long-context and recall tasks. This is not proof that it replaces a full language model or runs efficiently on old CPUs. https://arxiv.org/abs/2501.00663
- **MIRAS:** framework for reasoning about memory architectures and update rules; Google Research overview: https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/
- **ATLAS — “Learning to Optimally Memorize the Context at Test Time” (ICML 2026):** test-time memorization approach; assess its paper benchmarks and hardware assumptions before extrapolating to CPU. https://proceedings.mlr.press/v306/behrouz26b.html
- **TNT — “Improving Chunkwise Training for Test-Time Memorization” (ICLR 2026):** reports up to 17× training acceleration versus its most accurate baseline configuration in the reported setting. **This is a training-speed result, not an inference-speed claim.** https://proceedings.iclr.cc/paper_files/paper/2026/hash/bdb30687f1c2255c29b11b0b45204ebe-Abstract-Conference.html
- **Delta-rule fast weights / Gated DeltaNet:** associative memory updates that can overwrite/correct earlier associations rather than only accumulate them.

## Research questions

1. Can test-time memory learn new facts without rewriting the whole model?
2. Does memory persist across sessions or only within one context?
3. How are obsolete facts, contradictions, and source provenance represented?
4. Does retrieval improve exact recall without degrading open-ended language quality?
5. What are the CPU costs of updating and querying memory?
6. How robust is retrieval to paraphrase, noise, and similar entities?

## Suggested hybrid

Keep durable facts and evidence in a sparse external store; use fast/latent memory for current interaction and learned associations; use the language model to interpret and explain. Treat the symbolic store as authoritative only for explicitly represented facts, and expose uncertainty when evidence conflicts.