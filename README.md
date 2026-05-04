# SciML-NODE-V7-Notebook-Weights

# **NOTE FOR RUNNING**

This notebook is currently configured to run through each cell and conduct the model evaluation by pulling down pre-saved weights and history from a prior training run. These weights are saved and made available via a zipfile in my GitHub repo release found here --> https://github.com/PMUSKET2/SciML-NODE-V7-Notebook-Weights/releases/tag/v7_weights

The model took ~30 minutes to train and evaluate using an H100 GPU in Colab so the current mode is set to EVAL_ONLY = TRUE. To actually run the full script with model training you would just set this to FALSE.  

Only final note is that I've primarily ensured this will run from any Google Collab environment. If you are running locally and running into issues then let me know and I can try to make it functional for your IDE. 

# Learning BlueROV2 Dynamics with a Neural ODE (v7)

**Main Goal:** Push VPT beyond 4.88 s (v5) by increasing the model's capacity and
training data volume

**Changes from v5:**
- Hidden dim: 64 → 128
- Training trajectories: 800 → 1200 (600 clean ramp + 600 noisy ramp)
- Trajectory length: unchanged at 66 steps (5.28 s)
- Rollout window: unchanged at 5 steps




### Full loss function

$$\mathcal{L} = \mathcal{L}_{\text{1-step}} + \lambda_r \cdot \mathcal{L}_{\text{rollout}} + \lambda_p \cdot \mathcal{L}_{\text{physics}}$$

where

$$\mathcal{L}_{\text{physics}} = \frac{1}{N} \sum_i \| f_\theta(x_i, u_i) - f_{\text{Fossen}}(x_i, u_i) \|^2$$

The physics loss compares the NODE's predicted derivative against
the analytical Fossen derivative at sampled state-control points.

### Configuration (Taking the most succesful components of previous model versoins)

| Setting | Value | Source |
|---------|-------|--------|
| Yaw reparam | Yes | v2 |
| Rotation prior | Yes | v3 |
| Rollout length | 5 steps | v2/v5 |
| Per-state weighting | Yes (3x yaw) | v3 |
| Physics loss | Yes | v4 |
| Noisy training data | Yes | v5 |
| Trajectory length | 66 steps (5.28 s) | v5 (stable) |
| **Hidden dim** | **128 (was 64)** | **v7** |
| **Training trajectories** | **1200 (was 800)** | **v7** |

---

## Notebook Structure

1. Setup
2. BlueROV2 Simulator
3. Data Generation (1200 traj, 66 steps, ramp + noisy ramp)
4. Neural ODE Architecture (2×128, rotation prior)
5. Physics-Informed Loss (Fossen reference)
6. Training (5-step rollout, 3-phase)
7. Evaluation
8. Comparison: v1 → v7
