# Cycle 30 — Architecture deep dive 01: Transformer family and decoder-only GPT

Date: 2026-10-15
Status: First architecture deep dive; baseline for all later comparisons.

## 1. What it is

A Transformer is a neural sequence architecture built around attention and position-wise feed-forward blocks, with residual paths and normalization. The original 2017 design is encoder–decoder. Modern language models often use a **decoder-only causal Transformer**: each token can attend only to earlier tokens and itself, then the model predicts the next token.

Primary sources:
- Vaswani et al., *Attention Is All You Need* (2017): https://arxiv.org/abs/1706.03762
- Brown et al., *Language Models are Few-Shot Learners* (GPT-3, 2020): https://arxiv.org/abs/2005.14165
- Meta, *The Llama 3 Herd of Models* (2024): https://arxiv.org/abs/2407.21783

## 2. Core computation

For token representations X, a standard attention head projects queries, keys and values:
- Q = X W_Q
- K = X W_K
- V = X W_V
- Attention(X) = softmax(Q Kᵀ / √d_k + mask) V

In a causal decoder, the mask blocks future positions. Multi-head attention runs several learned projections in parallel; the result is projected and added through a residual path. An MLP/FFN transforms each token representation. Repeating these blocks allows the representation at one position to combine information from many previous tokens.

At inference, the model uses the final-position representation to produce logits for the next token. Autoregressive generation then repeats this process for each generated token.

## 3. Main variants that must not be conflated

- **Encoder-only** (BERT-like): bidirectional representation learning; not ordinarily a next-token generator.
- **Encoder–decoder** (original Transformer, T5-like): encoder processes input; decoder generates conditioned output.
- **Decoder-only causal LM** (GPT/Llama-style): one causal stack, standard for general text generation.
- **Dense vs MoE**: dense activates most FFN weights for each token; MoE routes tokens through selected experts. MoE is a modification of the Transformer block, not a wholly unrelated sequence architecture.
- **MHA/MQA/GQA/MLA**: attention/KV representation variants. They affect memory and compute differently; they are not interchangeable.
- **Position methods**: absolute positions, RoPE, ALiBi, NoPE and hybrids modify how position is represented; they do not by themselves replace the Transformer.
- **Training objective vs inference method**: next-token training, MTP, speculative decoding, beam/search, and test-time reasoning are separate axes.

Example: the Llama 3 report describes a dense decoder Transformer with RoPE, SwiGLU, RMSNorm-related design details, and grouped-query attention in the published model configurations. These are configuration choices, not required properties of every Transformer: https://arxiv.org/abs/2407.21783

## 4. Strengths

1. **Strong general-purpose baseline.** Large decoder-only Transformers have demonstrated few-shot language behavior and broad task transfer; GPT-3 is an important documented example.
2. **Parallel training.** During training, all target positions in a sequence can be processed in parallel under the causal mask, unlike token-by-token recurrent generation.
3. **Content-addressable access to context.** Attention can directly connect a query position to specific earlier token representations, which helps exact retrieval compared with a single fixed-size recurrent state.
4. **Composable ecosystem.** Sparse experts, alternative attention, quantization, adapters, retrieval, tools, and multimodal modules can be integrated without replacing the whole stack.
5. **Mature implementations and tooling.** This makes it the required baseline when evaluating less-established architectures.

## 5. Weaknesses and costs

1. **Autoregressive latency.** Generation generally requires a sequential next-token loop; parallel training does not imply parallel ordinary decoding.
2. **Growing KV cache.** In common decoder implementations, stored keys/values grow with context length, layers, KV heads, head dimension, precision, and batch size.
3. **Memory bandwidth pressure.** At small batch sizes, decoding a quantized model can be dominated by moving weights from RAM/cache rather than peak arithmetic throughput. Fewer FLOPs alone do not guarantee faster CPU inference.
4. **Attention cost at long context.** Full attention has quadratic attention computation during prefill; during cached decode, each new token attends over the accumulated context, so per-token attention work grows with context.
5. **Parameter compression does not guarantee reasoning.** Quantization, pruning, distillation or a smaller parameter count can preserve some benchmark scores while changing other capabilities. Quality must be measured.
6. **No explicit belief revision or provenance by default.** Facts and intermediate conclusions are distributed through activations/weights; the architecture alone does not provide a symbolic record of which premise supports which conclusion or safely retract a false premise.

## 6. CPU-first assessment for the compact assistant project

**Use as the reference baseline, not assume it is the final design.**

For a CPU-first device with a few GB of RAM:
- The weight file and quantization level determine whether the model fits; runtime memory also includes KV cache, compute buffers, tokenizer, OS and application.
- Small context and small batch can make optimized dense attention competitive; long context can shift the bottleneck toward cache/memory traffic.
- GQA/MQA/MLA and KV quantization/compression are worth measuring separately from weight quantization.
- Compare prompt processing and token generation separately. Report model load time, prefill tokens/s, decode tokens/s, peak process RSS, model file size, context length, quantization, CPU instruction support, and quality.
- A hybrid that keeps attention for exact recall but uses recurrent/SSM blocks for most token processing is a plausible candidate to test—not an established winner.
- For the user's graph-reasoning concept, the Transformer can remain a language interface while explicit structured memory and a rule/proof engine handle provenance and retraction. That is a system hybrid, not a claim that a graph alone replaces language understanding.

## 7. Minimum fair benchmark

Use the same training data budget, tokenizer or byte representation where applicable, parameter/active-parameter reporting, context length, quantization, hardware, and output-quality test set when possible. For inference comparisons, record:
- prefill latency and throughput separately from decode;
- median and tail token latency after warmup;
- peak process RSS and KV-cache bytes at fixed context;
- exact hardware/runtime/thread settings;
- held-out compositions, paraphrases, long-context retrieval, contradictions/exceptions, and calibrated UNKNOWN;
- failure examples, not only aggregate score.

Never compare a small model on a narrow puzzle benchmark directly with a general LLM and conclude it is globally more intelligent.

## 8. Evidence and open questions

- **Established:** the Transformer family, causal decoder language modeling, and the broad train-parallel/decode-autoregressive distinction.
- **Paper-reported:** particular model quality, scaling, context lengths and architecture-specific gains. These depend on model, data, recipe and evaluation.
- **Not established by architecture alone:** grounded reasoning, reliable uncertainty, causal inference, safe belief revision, or efficient CPU performance.
- **Questions for subsequent cycles:** Which component contributes the most to CPU latency at short vs long context? How much KV memory is needed at target quality? Which attention/SSM hybrid retains exact fact retrieval? Can explicit graph memory improve correction and UNKNOWN calibration without damaging natural dialogue?

## 9. Sources

1. Vaswani et al. (2017), *Attention Is All You Need*: https://arxiv.org/abs/1706.03762
2. Brown et al. (2020), *Language Models are Few-Shot Learners*: https://arxiv.org/abs/2005.14165
3. Meta (2024), *The Llama 3 Herd of Models*: https://arxiv.org/abs/2407.21783
4. Registry's second-pass 2025–2026 search: https://github.com/vidoosik/ai-architecture-registry/blob/main/29-second-pass-architecture-search-2025-2026.md

## 10. Decision

Keep decoder-only Transformer/GPT as the baseline against which every candidate is compared. Do not select it as the final compact architecture before measurements. The next deep dive should examine the next family/component in the checklist, while preserving the same evaluation template.
