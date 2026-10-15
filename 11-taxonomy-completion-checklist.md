# Taxonomy completion checklist

This checklist helps avoid quietly dropping architecture families from the master list. It is intentionally broad; not every item deserves implementation.

## Sequence and language
- [ ] Decoder-only Transformer / GPT
- [ ] Encoder-only / BERT
- [ ] Encoder–decoder / T5
- [ ] Prefix LM and denoising objectives
- [ ] MHA, MQA, GQA, MLA
- [ ] Local, sparse, linear, approximate attention: Performer/FAVOR+, Linformer, Nyströmformer, Reformer/LSH
- [ ] FlashAttention (kernel optimization)
- [ ] Transformer-XL, Compressive Transformer, RMT
- [ ] Memorizing Transformer, kNN-LM, RAG
- [ ] RNN, Elman/Jordan, LSTM, GRU, bidirectional RNN
- [ ] QRNN, SRU, ESN/reservoir
- [ ] RWKV, RetNet, LRU, H3
- [ ] S4/S4D/DSS/S5/HiPPO
- [ ] Mamba/Mamba-2, HGRN/HGRN2, Mega, Griffin, xLSTM
- [ ] Hyena, GLA, Based, MVA, Gated DeltaNet, DeltaStack

## Memory and structured representation
- [ ] External symbolic memory / knowledge graph
- [ ] Vector retrieval, RAG, kNN-LM, neural cache
- [ ] NTM, DNC, fast weights, modern Hopfield
- [ ] Titans, MIRAS, ATLAS, TNT
- [ ] VSA/HDC: HRR, Binary Spatter Codes, MAP, SBDR, TPR, Semantic Pointer Architecture
- [ ] Temporal/spatio-temporal knowledge graphs
- [ ] Episodic memory, memory consolidation, continual learning
- [ ] Tensor networks: Tensor Train, MPS/MPO, Tensor Ring, Tucker
- [ ] Structured matrices: Monarch, Butterfly, Toeplitz, Circulant, Kronecker, low-rank

## Symbolic, causal, and neuro-symbolic
- [ ] Production systems, propositional logic, first-order logic
- [ ] Datalog, Prolog, SAT/SMT, description logic
- [ ] ILP, probabilistic/Markov logic
- [ ] Differentiable logic, LTN/LNN, DeepProbLog
- [ ] Neural theorem provers, semantic parsing, LLM + solver
- [ ] Bayesian networks, HMM, Kalman/particle filters, MRF/CRF
- [ ] Causal graphs and structural causal models
- [ ] Graph neural networks: GCN, GAT, GraphSAGE, GIN, MPNN, R-GCN
- [ ] Graph Transformers and knowledge graph embeddings

## Learning and alternative computation
- [ ] Autoencoders, VAE, VQ-VAE, contrastive learning, MAE
- [ ] JEPA, predictive coding, inference learning, active inference
- [ ] Energy-based models, Boltzmann machines/RBM, score-based models
- [ ] Deep Equilibrium Models, Neural ODE/CDE
- [ ] Hebbian/Oja, equilibrium propagation, Forward-Forward
- [ ] Liquid Time-Constant Networks, Neural Circuit Policies
- [ ] Neural Cellular Automata, spiking neural networks
- [ ] Hypernetworks, meta-learning, neural program synthesis
- [ ] KAN/FastKAN, Neural GPU, reservoir computing, neuroevolution

## Generation, vision, audio, and agents
- [ ] Autoregressive generation, GAN, normalizing flows
- [ ] Diffusion/DDPM/DDIM/score-SDE, flow matching, rectified flow, consistency models
- [ ] Discrete diffusion LMs and energy-based generation
- [ ] CLIP, cross-attention VLM, Perceiver/Perceiver IO, Flamingo, Q-Former
- [ ] ViT/Swin, equivariant networks, capsule networks
- [ ] Audio/speech tokenizers, streaming and unified multimodal models
- [ ] Q-learning/DQN, policy gradients, Actor-Critic, PPO/SAC/TD3
- [ ] Model-based RL, MuZero, MCTS, hierarchical RL/options, imitation/offline RL
- [ ] Tool agents, planner–executor–critic, code execution and verification

## Compression and systems
- [ ] FP16/BF16/FP8/INT8/INT4/INT2, GPTQ/AWQ/SmoothQuant/QAT
- [ ] BitNet/BitLinear/ternary, pruning, low-rank, weight sharing, distillation, LoRA
- [ ] KV cache compression/offload, speculative decoding, prefix caching, early exit
- [ ] CPU SIMD/cache blocking, memory mapping, GPU/CPU offload
- [ ] DirectML, Vulkan, CUDA, Metal, XLA, TVM, MLIR
- [ ] Neural operators, implicit neural representations, wavelet/multiresolution models

A checked box should mean the topic has a source-backed entry and a stated relevance/limitation—not merely that its name appears somewhere in the archive.