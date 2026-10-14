# AlphaNIST Reproduction

Reproduction / port of [AlphaNIST: Emergent Assembly Skills from Binary Task Rewards](https://alpha-nist.github.io/) (arXiv:2610.13354, NVIDIA + UW, 2026-10-09).

## What it does

One shared policy (PPO) learns all 20 tasks on NIST Assembly Task Board #1 in Isaac Lab from **binary task rewards only** — no demonstrations, no dense progress shaping, no per-task tuning. Reported: 98.2% overall training success at 64K parallel envs / 25K iters (410 GPU-hrs), beating 20 separately-trained specialists (16,100 GPU-hrs, 81.4% mean) and joint dense-reward training (17.8%). Multi-pair sequencing emerges (10 pairs, 84%, no prescribed order).

## Key techniques being ported

1. **Beta success-guided sampling (SGS)** — reset states drawn with probability ∝ Beta-like kernel around the rolling per-entry success rate, so compute concentrates near the ~50% capability frontier. +13.1 pts at 256K envs vs uniform sampling (97.4% vs 84.3%) with identical bank/policy/reward/optimizer.
2. **Heterogeneous reset cache (OmniReset-style)** — cached entry = (assembly identity, initial state) spanning ungrasped workspace, in-hand, near-mating, partially assembled poses. No stage identifier in observation.
3. **Binary full-assembly reward** — reward issued once when assembly error < threshold, episode terminates; control cost only (action magnitude / joint velocity / torque), same coefficient across tasks.
4. **Geometry-conditioned observations** — surface point clouds of part + mating geometry + robot, no discrete task ID.

## Status

- [ ] Upstream code release tracked (cron job watches alpha-nist.github.io for repo link)
- [ ] Port SGS sampler into local Isaac Lab / rsl_rl stack
- [ ] A/B: binary reward + beta SGS vs baseline uniform + dense shaping on local Franka tasks

## Local environment

- Box: udoge (192.168.50.33), RTX 5090, ssh user `hock`
- GPU exclusive rule: check `nvidia-smi` before starting; stop `quanpool-miner` (with boss OK) before training; ask before relaunching after.
