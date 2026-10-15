# Compression, inference optimization, and hardware

## Model compression

- **Quantization:** FP16/BF16, FP8, INT8, INT4, INT2 and mixed precision; lower weight bandwidth/RAM often helps CPU deployment, but accuracy and kernel support matter.
- **GPTQ / AWQ / SmoothQuant:** post-training quantization methods with different calibration and scaling strategies.
- **Quantization-aware training (QAT):** train while simulating quantization effects.
- **GGUF and runtime-specific formats:** packaging and tensor layouts, not an architecture by themselves.
- **Pruning / sparsity:** remove weights or connections; speedup requires kernels that exploit the resulting pattern.
- **Low-rank factorization / tensor decomposition:** reduce parameter count at the cost of extra operations or approximation error.
- **Weight sharing / distillation:** reduce model size or transfer capability from a larger teacher.
- **LoRA/adapters:** efficient parameter updates, not necessarily a smaller inference model unless merged/handled appropriately.
- **BitNet / BitLinear / ternary weights:** very low-bit weight representations; real CPU benefits require optimized integer kernels and quality validation.

## Inference optimizations (not new architectures)

- **KV-cache reuse, compression, quantization, and offload:** reduce memory pressure for long-context decoding.
- **Speculative decoding:** draft model proposes tokens; target model verifies; gains depend on acceptance rate and relative model speed.
- **Prefix caching:** reuse repeated prompt computation.
- **Continuous batching:** important for serving many requests, less relevant to a single local user.
- **Early exit / adaptive computation / layer skipping:** save compute on easy inputs but require confidence controls and quality checks.
- **CPU SIMD and cache blocking:** improve dense kernels; use hardware-specific measurements.
- **Memory mapping and lazy loading:** reduce startup/RAM pressure for large knowledge stores.
- **GPU offload / DirectML / Vulkan / Metal / CUDA:** backend availability and kernel quality vary by device and driver; a backend existing does not guarantee speedup.
- **XLA / TVM / MLIR and graph compilers:** can optimize execution graphs, but compile/runtime overhead and operator coverage matter.

## CPU-first measurement checklist

Record:
- CPU model, ISA support (e.g., AVX2 availability), clock/power conditions, thread count.
- OS, compiler, runtime commit/version, backend, build flags.
- Model architecture, exact file, quantization, model size, context/batch settings.
- Cold load time, warm prefill, decode tokens/s, peak RAM, swap activity, power if available.
- CPU baseline and any GPU/accelerator comparison.
- Correctness and quality before/after optimization.

## Key caveats

- FLOPS alone do not predict autoregressive inference. Weight traffic and memory bandwidth often dominate.
- Shared GPU memory is not the same as dedicated VRAM; data transfer and synchronization can erase compute gains.
- Training speedup is not inference speedup.
- A 4-bit model is not necessarily twice as fast as an 8-bit model; dequantization and kernel details matter.
- Report measured speed separately from theoretical ceilings and unverified estimates.