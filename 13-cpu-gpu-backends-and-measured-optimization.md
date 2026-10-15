# CPU/GPU backends and measured optimization findings

This appendix preserves implementation and hardware findings from the recent dialogues. Numbers from different models/builds/tests must not be treated as one comparable benchmark series.

## Hardware context

### Windows test laptop
- Intel Core i7-3612QM (Ivy Bridge), 4 cores / 8 threads, about 8 GB RAM, AVX but no AVX2.
- Intel HD Graphics 4000 and AMD Radeon HD 7600M/7670M-class (Turks family, about 1 GB VRAM). Confirm exact SKU via vendor/device/subsystem IDs.
- DirectCompute via D3D11/HLSL was a working backend in the user's llama.cpp fork.
- OpenCL 2.0 AMD-APP 1912.5 was available, but OpenCL GEMV was slower in the tested implementation.
- Vulkan ICD was absent in the tested Windows setup. This is not proof of universal Vulkan impossibility.
- A driver change cannot add hardware capability. Possible changes to code generation/overhead are hypotheses unless measured.

### MacBook Pro 13-inch (2017)
The discussed machine has Intel Iris Plus Graphics 640, not the Windows laptop's discrete Radeon. Do not transfer Radeon DirectCompute results to this Mac.
- Closest macOS analogue to D3D11/DirectCompute + HLSL: Metal compute + Metal Shading Language.
- OpenCL existed on this Intel generation, but current compatibility/performance require testing.
- Vulkan on macOS typically uses MoltenVK to translate Vulkan to Metal; performance is workload-dependent.
- No reliable apples-to-apples optimization result was established for this MacBook. Benchmark CPU, Metal, and any viable OpenCL/Vulkan route separately.

## Reported measurements from Windows microbenchmarks

- RAM bandwidth results varied with access pattern and test direction; representative results were about 18.7, 9.3, and 17.5 GB/s. RAM latency was about 85–108 ns for 32–256 MB working sets and about 126 ns for a 1 GB working set.
- Radeon local VRAM bandwidth peak was measured around 27.4 GB/s.
- Radeon FP32 throughput appeared around 395 GFLOPS in one test and 550+ GFLOPS in a later register-oriented test. These are different microbenchmarks, not sustained model throughput.
- CPU AVX GEMM reported 112.3 GFLOPS in a particular benchmark.
- Host-to-VRAM CopyResource reached about 2.9 GB/s; an older VRAM readback test was about 3.3 GB/s. These are not local VRAM bandwidth.

## Model results reported in the notes

### Qwen GPU offload
- Qwen 0.8B: GPU decode ~1.93 tokens/s, prefill ~2.51 tokens/s, 533 splits.
- Qwen 1B: GPU decode ~2.68 tokens/s, 197 splits.
- Related CPU runs were around 9.5–9.6 tokens/s.
These configurations made the older GPU path slower end-to-end; split count, transfers, synchronization, quantized kernels, and offload fraction may dominate.

### DirectCompute Llama optimization history
- Baseline GPU-offloaded run: 5.17 tokens/s.
- GLU optimization: 6.11 tokens/s.
- GLU + RoPE optimization: 7.07 tokens/s.
- Later tests crossed 10 tokens/s in another build/test stage.
- A separate later Llama-3.2-1B run reported ~14.47–14.99 tokens/s decode and ~25.0–26.0 tokens/s prefill.
- OpenCL GEMV was slower in the tested path; a full controlled OpenGL-vs-DirectCompute comparison was not completed.
These results are not one series: preserve exact model, quantization, build, prompt, context, split count, and timing protocol when comparing.

### One profiled decode path
A report measured about 61.84 ms/token: Q4_K GEMV 35.3 ms (~57%), Q6_K 20.7 ms (~33%), remaining components ~6 ms. Reported weight traffic was 802.76 MB/token, with effective bandwidth accounting around 13.23 and 16.21 GB/s. A ~19.2 tokens/s ceiling was an estimate from that profile, **not measured throughput**.

### Mac backend comparison from another GPU
A separate AMD Radeon 6800 test reported prompt processing around 195 tokens/s with Metal vs 102 with Vulkan, while generation was around 71 vs 75 tokens/s. This is not evidence for the 2017 Intel Iris Plus 640 or Radeon HD 7670M.

## CPU pipeline calculator research

A simplified Ivy Bridge calculator was built around workload → instructions → µops → latency → cycles → frequency → time. Example: a dependent ADD R64,R64 chain modeled as one µop and roughly one-cycle latency, with execution-port assumptions still needing verification. For 100 million dependent ADDs, the simplified ideal predicts ~47.6 ms at 2.1 GHz or ~32.3 ms at 3.1 GHz.

This is not a complete CPU model: cache/TLB, branch prediction, out-of-order execution, reorder buffer/scheduler, SMT contention, Turbo, power, and temperature remain unmodeled or need validation. The proposed validation was a separate C++ benchmark executing the same dependency chain, measuring RDTSC/RDTSCP cycles plus wall time/effective frequency, repeating runs and comparing error without fitting the model to the same benchmark.

## Interpretation rules

1. Backend names do not predict speed: kernel quality, split count, transfers, synchronization, quantization, and offload fraction matter.
2. Prompt processing and single-token generation are different workloads. BLAS/Accelerate can help prefill more than decode; measure separately.
3. Do not treat theoretical FLOPS or local VRAM bandwidth as model tokens/s.
4. Label claims as measured, calculated, estimated, hypothesized, or not tested.
5. Benchmark on the exact target device/runtime before transferring results across machines.