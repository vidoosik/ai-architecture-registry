# Transformer and attention families

## Core families

- **Decoder-only autoregressive Transformer (GPT-style):** causal self-attention plus MLP blocks; strong flexible generation, but inference cost and KV cache scale with context and model size.
- **Encoder-only (BERT-style):** bidirectional representations, usually better suited to classification/encoding than free-form generation by itself.
- **Encoder–decoder (T5-style):** separate input encoding and autoregressive output; useful for conditional generation and transformation.
- **Prefix/denoising objectives:** train with prefixes or corrupted spans to change how context and reconstruction are handled.
- **MHA, MQA, GQA:** different query/key/value head sharing trade-offs. MQA/GQA can reduce KV-cache size and bandwidth, with quality/implementation trade-offs.
- **MLA (Multi-head Latent Attention):** compresses KV representation to reduce cache footprint; actual benefit depends on implementation and hardware.
- **Transformer-XL / recurrent memory / Compressive Transformer / RMT:** extend usable context via segment recurrence, compression, or memory tokens; do not make all past information free.
- **Memorizing Transformer / kNN-LM:** add retrieval over hidden-state key-value memories; can help recall but adds indexing, memory, and retrieval costs.
- **Retrieval-augmented generation (RAG):** retrieve external passages/facts and condition generation; improves access to stored knowledge but does not guarantee correct reasoning.

## Efficient attention variants

- **FlashAttention:** exact attention implemented with IO-aware tiling; primarily an implementation/kernel optimization, not a new model family.
- **Local/window/sparse attention:** reduce attention connections; may miss distant dependencies unless paired with global tokens or memory.
- **Linear attention / FAVOR+ (Performer):** kernel feature approximations can reduce sequence-length scaling; approximation and constants matter.
- **Linformer:** low-rank sequence projection.
- **Nyströmformer:** approximate attention using landmarks.
- **Reformer / LSH attention:** locality-sensitive hashing to group similar items.
- **FNet:** Fourier mixing as an alternative to attention for token interaction.
- **Long-context hybrids:** combine local attention, recurrent state, retrieval, and/or compressed memory.

## Practical trade-offs

Dense Transformers are a quality baseline because pretrained weights and runtimes are widely available. Architectural FLOP reductions do not guarantee CPU speedups: optimized dense kernels can beat irregular sparse work. For small CPU deployment, compare parameter count, quantized weight bandwidth, cache traffic, KV memory, context length, and kernel maturity.

## Hypotheses to test

- Use a small pretrained model for language flexibility, not as the sole source of truth.
- External typed memory may be cheaper than expanding the model solely to memorize facts.
- GQA/MLA and context/KV compression may matter more for long contexts than short prompts.
- Linear attention should be tested on long sequences and CPU-friendly implementations; do not assume it is automatically faster at small sequence lengths.