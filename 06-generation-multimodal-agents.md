# Generation, multimodal models, agents, and planning

## Generative families

- **Autoregressive models:** predict the next token; flexible language generation, sequential decoding cost.
- **VAE / VQ-VAE:** latent-variable and discrete-code generation.
- **GANs:** generator-discriminator training; fast sampling after training but potentially unstable training and coverage issues.
- **Normalizing flows:** invertible transformations with tractable density; architecture constraints can limit flexibility.
- **Diffusion / DDPM / DDIM / score-SDE:** iterative denoising generation; quality strong in many domains, sampling can be expensive.
- **Flow matching / rectified flow / consistency models:** alternative training and sampling formulations aimed in part at fewer steps.
- **Discrete diffusion language models:** iterative refinement of token sequences; active research, compare latency and quality to autoregressive baselines.
- **Energy-based generation:** sample or search over outputs using learned compatibility scores.

## Multimodal architectures

- **CLIP-style dual encoders:** align image and text embedding spaces; strong retrieval, not a full generative assistant by itself.
- **Cross-attention vision-language models:** condition language generation on visual features.
- **Perceiver / Perceiver IO:** latent bottleneck for high-dimensional inputs/outputs.
- **Flamingo-style gated cross-attention:** insert visual/audio conditioning into a language model.
- **Q-Former-style bridges:** compress or select visual features for language models.
- **Vision Transformers / Swin:** transformer-based visual representation.
- **Audio/speech tokenizers and streaming models:** encode audio into discrete/continuous units; real-time use depends on latency and chunking.
- **Unified multimodal models:** share representations or model backbone across text, image, audio, and video.
- **Multimodal diffusion:** generate images/audio/video conditioned on other modalities.

## Agents and planning

- **Tool-using LLM agents:** call search, code execution, files, or UI tools; the agent's claims must be grounded in tool outputs.
- **Planner–executor–critic:** separate planning, action, and verification; can improve reliability but adds calls and failure modes.
- **Code execution and test feedback:** generate code, run tests, inspect exceptions, revise. This is essential for a coding assistant that must verify rather than merely suggest.
- **Q-learning / DQN, policy gradients, Actor–Critic, PPO, SAC, TD3:** reinforcement-learning families; useful for policies/control, not drop-in language-model replacements.
- **Model-based RL / MuZero:** learn dynamics or search over predicted outcomes.
- **Monte Carlo Tree Search (MCTS):** search-based planning; effective when actions and value estimates are available, can be costly.
- **Hierarchical RL / options:** learn temporally extended actions.
- **Imitation / offline RL:** learn from demonstrations or logged behavior.

## Creativity and humor evaluation

These capabilities should be measured directly. Build separate test sets for:
- ambiguous phrases with context-sensitive interpretations;
- incongruity and expectation reversal;
- analogy and concept blending;
- story continuations with constraints and consistent characters;
- novel but coherent combinations;
- revision after feedback;
- detecting when a joke is culturally or linguistically dependent.

Do not claim creativity from high perplexity scores or humor from keyword matching. Use human evaluation and blinded pairwise comparisons where possible.