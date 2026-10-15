# Recurrent, state-space, and linear-time models

## Classical recurrence

- **RNN / Elman / Jordan:** compact state recurrence; vanilla training can suffer vanishing/exploding gradients.
- **LSTM / GRU:** gated recurrence that improves long-range learning; sequential dependencies can limit parallelism.
- **Bidirectional RNNs:** use both directions, usually for encoding rather than streaming generation.
- **QRNN / SRU:** move more work into parallel convolutions while retaining recurrent state updates.
- **Echo State Networks / reservoir computing:** fixed or lightly trained recurrent dynamics with a trained readout; cheap experiments, but capability depends on reservoir and task.

## State-space and modern recurrent models

- **S4 / S4D / DSS / S5 / HiPPO:** structured state-space approaches for long sequence dependencies; many were designed around efficient training kernels.
- **Mamba / Mamba-2:** selective state-space models whose state update depends on input; linear sequence scaling in core operations, but hardware and kernel behavior determine practical speed.
- **H3:** structured state-space sequence model with associative-memory motivation.
- **LRU:** linear recurrent units designed for stable long-range dynamics.
- **RetNet:** retention mechanism with parallel, recurrent, and chunkwise formulations.
- **RWKV:** recurrent inference with Transformer-like training/generation behavior; CPU performance depends on implementation and model.
- **xLSTM:** modern gated recurrent architecture with revised exponential gating and matrix-memory variants.
- **HGRN/HGRN2, Mega, Griffin:** gated recurrent/attention hybrids; candidates for quality/efficiency comparisons.
- **Hyena:** long convolutions and gating as an alternative to attention.
- **Gated Linear Attention (GLA), Based, MVA:** linear-time sequence/memory families with different gating, chunking, and hardware trade-offs.
- **Gated DeltaNet / Delta-rule models:** update associative state using a delta rule that can correct earlier stored associations.
- **DeltaStack:** stack-augmented linear attention with push/pop-style operations; useful as a research lead for structured sequence memory, not a proven general reasoning replacement.

## How to compare fairly

Use the same tokenizer, training data, parameter budget, quantization, prompt, and CPU thread settings when possible. Report prefill separately from decode. Test long-context retention, recall after interference, systematic composition, and incremental updates. Published GPU throughput does not establish old-CPU throughput.

## Compact-system hypothesis

A recurrent or SSM language core may reduce context-related inference cost, but it still needs learned weights for language and world knowledge. Explicit external memory can provide durable facts, while recurrent state carries current context. The key question is whether quality survives compression and whether CPU kernels are mature enough to beat a small quantized Transformer on the target machine.