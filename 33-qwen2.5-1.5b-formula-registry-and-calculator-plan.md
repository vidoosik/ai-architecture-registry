# Qwen2.5-1.5B: formula registry and calculator validation plan

**Status:** Stage 1 — model manifest and reference map (initial record; not yet independently executed against downloaded tensors)  
**Purpose:** Establish a faithful, testable reference for the complete forward pass before experimenting with alternative formula chains.  
**Canonical archive:** https://github.com/vidoosik/ai-architecture-registry  
**Model card/files:** https://huggingface.co/Qwen/Qwen2.5-1.5B  
**Reference implementation:** https://github.com/huggingface/transformers/blob/main/src/transformers/models/qwen2/modeling_qwen2.py

## Research rules

1. Keep three labels separate: **documented**, **locally measured**, and **hypothesis**.
2. Record exact model revision, tokenizer revision, library versions, dtype, device, sequence length, and test inputs for every reproducible comparison.
3. Compare intermediate tensors, not only final text. Final text can match despite numerical differences, and small numerical differences need not imply functional failure.
4. Do not call the simulator accurate until it has been compared against an independent implementation on identical weights and inputs.
5. Do not infer language understanding from arithmetic correctness. Numerical validation and behavioral evaluation are separate gates.
6. Do not assume all operations can be reordered. For candidate operations A and B, test whether A(B(x)) equals B(A(x)) under stated conditions; for nonlinear operations this is generally not guaranteed.
7. Do not assume a fixed count of formulas makes a better model. Every new mechanism needs a purpose, equation, shape contract, cost estimate, training implications, and ablation test.

## Stage 1 — model manifest

The following are the published Qwen2.5-1.5B configuration values to verify against the exact downloaded `config.json` before implementation:

| Field | Expected value | Why it matters |
|---|---:|---|
| `model_type` | `qwen2` | Selects the implementation family |
| `vocab_size` | 151936 | Token ID range and output-logit width |
| `hidden_size` | 1536 | Residual-stream width |
| `num_hidden_layers` | 28 | Number of repeated decoder blocks |
| `num_attention_heads` | 12 | Query-head count |
| `num_key_value_heads` | 2 | GQA key/value-head count |
| `head_dim` | 128 | Per-head width; verify config/code derivation |
| `intermediate_size` | 8960 | SwiGLU intermediate width |
| `max_position_embeddings` | 32768 | Configured maximum positional range; not a guarantee of equal quality at every length |
| `rms_norm_eps` | 1e-6 | RMSNorm numerical constant |
| `rope_theta` | 1000000.0 | RoPE frequency base |
| `tie_word_embeddings` | false | Output language head is not assumed tied to input embeddings; verify actual state-dict keys |
| `hidden_act` | `silu` | Gated MLP activation |
| `use_cache` | true | Enables inference KV cache in supported paths |

**Gate:** Do not freeze these values into code until the downloaded configuration and state-dict tensor names/shapes are inspected. Model revisions can change files.

## Stage 2 — tokenizer and embedding path

Expected conceptual path:

`UTF-8 text → tokenizer normalization/pre-tokenization → BPE tokens → token IDs → embedding lookup`

For token IDs (t_1,\ldots,t_n), embedding lookup returns:

`X[i] = E[t_i]`

where the input embedding matrix is expected to have shape `[vocab_size, hidden_size]`. The tokenizer is a separate algorithm/artifact, not a neural layer evaluated on every model forward pass. Record special tokens, chat template (if applicable), normalization, exact token IDs, and decoded round-trip behavior.

**Tests to add:**
- Known strings with spaces, punctuation, Unicode, Cyrillic, and mixed scripts.
- Exact token IDs against the reference tokenizer.
- Empty input and special-token behavior.
- Input embedding rows compared exactly or within dtype-specific tolerance.

## Stage 3 — decoder block equation map

This is the expected high-level Qwen2 block; verify implementation details and tensor layouts from the pinned source revision.

### 3.1 RMSNorm

For a hidden vector (x\in\mathbb{R}^{d}):

`RMSNorm(x) = (x / sqrt(mean(x ⊙ x) + ε)) ⊙ γ`

Check the exact placement of epsilon, accumulation dtype, and learned scale `γ` in the reference implementation. No mean-centering is performed as in LayerNorm.

### 3.2 Query/key/value projections and GQA

`Q = X W_Q^T + b_Q`  
`K = X W_K^T + b_K`  
`V = X W_V^T + b_V`

Use actual stored tensor shapes and library conventions rather than relying on notation alone. With 12 query heads, 2 KV heads, and head dimension 128, the query width is 1536 and key/value width is 256. KV heads are shared across query-head groups during attention.

### 3.3 RoPE

Apply position-dependent rotations to Q and K according to the exact Qwen2 implementation, `rope_theta`, rotary dimensions, and position IDs. Do not replace this with a generic positional embedding. Add small hand-checkable tests for position 0, position 1, and multiple tokens.

### 3.4 Causal attention

Conceptual equation:

`scores = (Q Kᵀ) / sqrt(head_dim) + causal_mask`  
`P = softmax(scores, dim=-1)`  
`context = P V`

For grouped-query attention, expand/share K and V heads exactly as the reference does. Verify mask values, dtype behavior, attention scaling, softmax axis, and whether an optimized kernel changes the execution path. Attention output is then reshaped and passed through the output projection.

### 3.5 Residual and pre-norm structure

Conceptual sequence:

`X1 = X + Attention(RMSNorm(X))`  
`Y = X1 + MLP(RMSNorm(X1))`

This is a shape-level description; match exact source code, tensor layout, dtype, and any implementation-specific details.

### 3.6 SwiGLU MLP

`gate = SiLU(X W_gateᵀ)`  
`up = X W_upᵀ`  
`mlp = (gate ⊙ up) W_downᵀ`

Check the three actual parameter tensors and shapes. Verify whether biases are present; do not silently assume them.

## Stage 4 — final output path

1. Apply the model's final RMSNorm to the last hidden states.
2. Apply the output language-model head to obtain logits of width `vocab_size`.
3. If probabilities are required, apply softmax in the correct dtype/precision.
4. Separate logits from decoding policy: greedy selection, temperature, top-k, top-p, repetition penalties, and stopping rules are inference choices, not all part of the learned core.
5. Verify whether output head weights are tied to input embeddings by config and state-dict inspection; do not infer tying from family conventions.

## Stage 5 — training path (separate from inference)

Record separately:
- next-token targets and shifting;
- cross-entropy loss and ignored/padded positions;
- logits-to-loss numerical precision;
- gradient calculation and backpropagation;
- optimizer, learning-rate schedule, weight decay, gradient clipping, and update rule;
- mixed-precision and gradient-scaling behavior, if used.

The inference calculator does not need to implement training gradients in its first version. Training support is a later module and must be validated independently.

## Stage 6 — calculator architecture

Build a small reference harness with pluggable operators:
- tokenizer adapter;
- embedding lookup;
- RMSNorm;
- linear projection;
- RoPE;
- attention mask and softmax;
- grouped-query head mapping;
- SwiGLU;
- residual addition;
- final norm and LM head.

For each operator, store: equation, input/output shapes, parameter names, dtype, FLOP estimate, memory reads/writes estimate, invariants, reference implementation, unit tests, measured max/mean absolute error, relative error, and notes on numerical stability.

## Validation protocol

### A. Operator-level
Use tiny deterministic tensors and hand-computable cases. Include shape errors, zero inputs, large/small magnitudes, and finite-value checks.

### B. Block-level
Compare every intermediate result from the calculator with the independent reference on the same weights, tokens, dtype, and mask. Record:
- max absolute error;
- mean absolute error;
- relative error with a safe denominator;
- cosine similarity where meaningful;
- NaN/Inf count;
- tensor name and shape.

### C. Full-forward
Compare every layer's hidden states, final hidden state, logits, and top-k token rankings. Run both short and longer sequences. Establish tolerances empirically for each dtype and kernel, not by choosing a convenient tolerance after seeing results.

### D. Reproducibility
Pin model revision and library versions; save exact input token IDs, sequence length, dtype, device/backend, and command line. Separate deterministic reference kernels from optimized kernels.

### E. Performance
Measure prompt processing and one-token decoding separately. Record warm-up, repetitions, median and spread, peak RAM, model disk size, KV-cache size, and hardware/software details. FLOPs alone are not a speed result.

## Planned repository structure

- `33-qwen2.5-1.5b-formula-registry-and-calculator-plan.md` — this plan and running equation registry.
- `calculator/` — implementation, added only after manifest and reference checks.
- `tests/reference_cases/` — saved token IDs, tiny tensors, expected outputs, and tolerances.
- `results/` — machine-readable comparison logs and benchmark conditions.

## Current status / next action

**Done:** selected the model family and mapped the major stages at a conceptual level.

**Not yet done:** download/pin the exact model revision; inspect tokenizer files and tensor names/shapes; execute numerical comparisons; implement the calculator.

**Next concrete task:** inspect `config.json`, tokenizer artifacts, and safetensors index/metadata; save a machine-readable manifest and a first exact tokenizer test before implementing neural operators.
