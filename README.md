# NeuroRL

**Neuromodulated reinforcement learning with graph transformations for configurable multi-robot teaming.**

A centralized PPO policy edits the **inter-robot adjacency graph** rather than commanding velocities; a decentralized Laplacian controller turns the edited graph into robot motion. A neuromodulated intrinsic reward, built from two health signals (group health and per-robot health), is the interface through which an operator states team priorities — survivability, path efficiency, behavioral assimilation, or symmetric formation — **without editing the objective, the task, the map, or the controller.**

This repository currently publishes the **evaluation results and demonstration videos** for the paper *"NeuroRL: Neuromodulated Reinforcement Learning with Graph Transformations for Robot Teaming,"* submitted to *The International Journal of Robotics Research*. The source code follows shortly — see [Code](#code--coming-soon).

---

## See it first

NeuroRL commanding a three-rover team: the graph is re-formed into a line to clear a corridor, then back into a symmetric ring once through.

<video src="https://raw.githubusercontent.com/autonomybot/NeuroRL/main/demos/highlights/neurorl_line_then_ring.mp4" controls muted loop width="640"></video>

[`demos/highlights/neurorl_line_then_ring.mp4`](demos/highlights/neurorl_line_then_ring.mp4)

A full scored evaluation episode, with the health traces and formation gate visible in the HUD.

<video src="https://raw.githubusercontent.com/autonomybot/NeuroRL/main/demos/highlights/neurorl_eval.mp4" controls muted loop width="640"></video>

[`demos/highlights/neurorl_eval.mp4`](demos/highlights/neurorl_eval.mp4)

> If a video does not play inline in your browser, click the file link beneath it.

---

## Abstract

Planetary exploration increasingly relies on robot teams to perform infrastructure, inspection, and navigation tasks in environments that are unsafe or inaccessible to humans. To verify robot survivability and that critical systems function as intended, robot teaming relies on autonomy that yields interpretable and configurable behavioral strategies. Deterministic controllers can produce explainable behavior, but they often lack adaptability in novel or changing terrain. This paper combines graph representations with reinforcement learning to capture team relations and adapt multi-robot behavior across a hierarchy of navigation objectives. Drawing inspiration from biological neuromodulation, behavioral explainability is grounded in geometric relations within the graph, while configurability arises through a neuromodulated reinforcement pipeline that intrinsically motivates task-agnostic strategy within the graphical transformations (NeuroRL). We test the graph-transformation performance in a Martian simulation and a real-world sandbox using a team of LEO Rovers. Results show that NeuroRL maintains high navigation success while producing configurable tradeoffs among survivability, behavioral assimilation, and symmetric formation, obtained without altering the reinforcement objective itself.

---

## Contributions

1. **A neuromodulated RL framework for interpretable multi-robot teaming.** NeuroRL couples a centralized graph-level PPO policy with decentralized Laplacian consensus control, so that learned team strategies remain expressed as interpretable geometric structure from training through deployment.

2. **Graph transformation as the action space.** The policy reshapes inter-robot adjacency relations through bounded multiplicative, additive, and subtractive masks, lifting the RL action from individual motion to team structure.

3. **Health-based neuromodulated intrinsic reward.** A biologically inspired intrinsic reward, grounded in group health and robot health, makes the return a function of health alone. Team behavior is reconfigured by changing how health is earned and spent, with the objective, task, map, controller, and training budget held fixed.

4. **Modular attribution of behavioral effects.** The framework separates what the reward configures from what the controller configures: the intrinsic weights shift team health and hazard exposure without altering the controller, while a single avoidance gain trades formation for throughput without altering the reward, attributing each effect to its true source.

---

## Results

### Comparison to learned multi-agent coordination baselines

Mean ± std over 360 episodes per framework across six obstacle-density levels. All four controllers receive identical observations and identical episode budgets. Baselines are MAGIC, DCG and MAPF. **Bold** marks the best value in each row.

| | Metric | NeuroRL | MAGIC | DCG | MAPF |
|---|---|---|---|---|---|
| **Task** | Task success (%) | **83.6** | 35.6 | 62.2 | 59.7 |
| | Completion time (s) | 25.09 ± 8.77 | 45.37 ± 11.96 | 17.62 ± 1.46 | **15.91 ± 1.36** |
| | Steps to success | 502 ± 175 | 907 ± 239 | 352 ± 29 | **318 ± 27** |
| **Safety** | Contact-free episodes (%) | **100.0** | 79.2 | 97.5 | 29.2 |
| | Contacts per episode | **0.00 ± 0.00** | 17.71 ± 86.57 | 0.26 ± 2.24 | 15.55 ± 13.55 |
| | Barrier-free episodes (%) | **87.2** | 0.0 | 0.6 | 0.0 |
| | Barrier steps per episode | **1.68 ± 5.60** | 545.19 ± 573.86 | 632.61 ± 633.55 | 656.77 ± 646.43 |
| **Formation** | Cohesion (%) | **95.13 ± 4.34** | 47.40 ± 32.60 | 43.46 ± 36.11 | 53.21 ± 35.52 |
| | Symmetry (%) | **85.58 ± 6.07** | 64.14 ± 9.19 | 66.98 ± 7.42 | 65.04 ± 8.79 |
| | Node angle (deg) | **20.69 ± 10.27** | 45.10 ± 12.61 | 45.76 ± 9.71 | 46.03 ± 13.15 |
| | Time in formation (%), mean | **36.74 ± 27.63** | 0.12 ± 0.77 | 1.06 ± 1.71 | 0.38 ± 4.68 |
| | &nbsp;&nbsp;median [IQR] | **28.57 [15.84, 52.10]** | 0.00 [0.00, 0.00] | 0.00 [0.00, 2.03] | 0.00 [0.00, 0.00] |
| **Path** | Length (m) | 5.20 ± 1.41 | 5.40 ± 1.22 | 4.68 ± 0.62 | **4.20 ± 0.78** |
| | Variance (%) | **8.46 ± 3.18** | 15.67 ± 17.49 | 22.32 ± 20.19 | 20.75 ± 19.74 |

NeuroRL is the only framework with **zero contacts across all 360 episodes**, and it holds cohesion at 95.13% against 43.46–53.21% for the baselines (δ ≥ 0.82, *p* < 10⁻¹²). Under the strict criterion requiring every vertex angle within 5° of the ideal 60°, it spends 36.74% of the episode in formation against 0.12–1.06% for the baselines.

### Degradation with clutter — learned baselines

Task success per obstacle-density level: 60 held-out seeds each, layouts resampled per seed. Coverage is the mean fraction of map area lying inside an obstacle. Superscripts mark a two-sided Fisher exact test of that framework against NeuroRL at the same level (\* *p*<0.05, † *p*<0.01, ‡ *p*<0.001). Retention is *S*(8.5%)/*S*(2.4%).

| Coverage (Obst.) | NeuroRL | MAGIC | DCG | MAPF |
|---|---|---|---|---|
| 2.4% (8) | **98.3** | 51.7 ‡ | 88.3 | 88.3 |
| 3.7% (12) | **95.0** | 40.0 ‡ | 76.7 † | 83.3 |
| 4.9% (16) | **88.3** | 45.0 ‡ | 71.7 \* | 70.0 \* |
| 6.1% (20) | **81.7** | 41.7 ‡ | 60.0 \* | 51.7 ‡ |
| 7.4% (24) | **73.3** | 23.3 ‡ | 48.3 † | 40.0 ‡ |
| 8.5% (28) | **65.0** | 11.7 ‡ | 28.3 ‡ | 25.0 ‡ |
| **Retention (%)** | **66.1** | 22.6 | 32.1 | 28.3 |

### Degradation with clutter — classical planners

Both A\* columns are **privileged with the ground-truth obstacle map**, so the Agent A\* row measures what an exact search achieves with information the learned policy does not have. The retention row is the change in percentage points from 2.4% to 8.5% coverage.

| Coverage (Obst.) | NeuroRL | MA-PF | MA-A\* | Agent A\* |
|---|---|---|---|---|
| 2.4% (8) | 98.3 | 88.3 | 83.3 † | 100.0 |
| 3.7% (12) | 95.0 | 83.3 | 70.0 ‡ | 100.0 |
| 4.9% (16) | 88.3 | 70.0 \* | 68.3 \* | 100.0 \* |
| 6.1% (20) | 81.7 | 51.7 ‡ | 53.3 † | 100.0 ‡ |
| 7.4% (24) | 73.3 | 40.0 ‡ | 51.7 \* | 100.0 ‡ |
| 8.5% (28) | 65.0 | 25.0 ‡ | 25.0 ‡ | 100.0 ‡ |
| **Retention (pp)** | **−33.3** | −63.3 | −58.3 | 0.0 |

The two unprivileged-geometry baselines do not degrade gracefully: MA-PF loses 63.3 points and MA-A\* 58.3 points between 2.4% and 8.5% coverage, both ending at 25.0%. NeuroRL loses 33.3 points and ends at 65.0%.

### Reward configurability

Means over four independent training runs, ± the standard deviation across the four run means. Each row raises one intrinsic weight above the balanced baseline (×3, or ×4 for the path-efficiency pair); **everything outside the reward is held fixed**. **Bold** marks a configuration that separates completely from the baseline in the exact run-level permutation test at *p* = 0.029.

| Configuration | Task success | Minimum agent health | In formation ≤5° (%) | Angle RMSE (deg) | Symmetry (%) |
|---|---|---|---|---|---|
| Balanced | 0.806 ± 0.020 | 0.478 ± 0.005 | 42.25 ± 4.66 | 20.11 ± 0.83 | 85.93 ± 0.60 |
| α<sub>ζ</sub> ×3 | 0.807 ± 0.004 | **0.539 ± 0.008** | 44.43 ± 2.31 | 19.48 ± 0.63 | 86.38 ± 0.44 |
| α<sub>σ</sub> ×3 | 0.794 ± 0.019 | **0.228 ± 0.017** | 52.66 ± 4.71 | **16.80 ± 1.49** | 88.10 ± 0.93 |
| α<sub>edit</sub> ×3 | 0.815 ± 0.016 | **0.443 ± 0.020** | **31.02 ± 0.89** | **23.99 ± 2.12** | **83.46 ± 1.51** |
| α<sub>τ</sub>, α<sub>G</sub> ×4 | 0.803 ± 0.008 | 0.472 ± 0.004 | 39.02 ± 5.86 | 21.54 ± 1.66 | 85.18 ± 1.02 |

Task success is statistically unchanged across every configuration (0.794–0.815) while health, formation and symmetry move in the commanded direction — the configurability claim in one table.

---

## Demos

All videos are rendered from the recorded evaluation episodes; **no video is a best case**. Within each bucket the episode closest to that bucket's *median* time in formation was selected. Videos run past the scored end of the episode until the rovers park on their goal markers; those extra frames are labelled `settling, +N steps not scored` in the HUD and change no reported number. A video that ends with the team mid-map is a timeout at the step horizon, not a truncated render.

> **Health is NeuroRL's objective and no baseline's.** The HUD shows a health trace for every controller because health is environment state, but the baselines carry no health term (`r_intrinsic == 0`). Read their trace as the cost of their route choice, not as a score they were pursuing.

### Framework comparison across the difficulty ladder

The video counterpart of the paper's showcase grid. Each scenario folder holds the **same level, same seed, and therefore the same sampled layout, start jitter and corridor for all four frameworks** — view a folder as a 2×2 panel to compare them directly. Scenarios come in three kinds: **fair** (all frameworks finish), **trade** (outcomes diverge), and **only** (NeuroRL is the only framework to finish).

| Scenario | Kind | Coverage | NeuroRL | MAGIC | DCG | MAPF |
|---|---|---|---|---|---|---|
| `L1_s6016_fair` | fair | 2.4% sparse | [339 steps, 0 contacts](demos/showcase/L1_s6016_fair/neurorl.mp4) | [timeout, 89 contacts](demos/showcase/L1_s6016_fair/magic.mp4) | [finishes](demos/showcase/L1_s6016_fair/dcg.mp4) | [20 contacts](demos/showcase/L1_s6016_fair/mapf.mp4) |
| `L2_s6018_fair` | fair | 3.7% moderate | [336 steps, 0 contacts](demos/showcase/L2_s6018_fair/neurorl.mp4) | [finishes](demos/showcase/L2_s6018_fair/magic.mp4) | [finishes](demos/showcase/L2_s6018_fair/dcg.mp4) | [27 contacts](demos/showcase/L2_s6018_fair/mapf.mp4) |
| `L3_s6018_fair` | fair | 4.9% dense | [363 steps, 0 contacts](demos/showcase/L3_s6018_fair/neurorl.mp4) | [timeout, 119 contacts](demos/showcase/L3_s6018_fair/magic.mp4) | [finishes](demos/showcase/L3_s6018_fair/dcg.mp4) | [finishes](demos/showcase/L3_s6018_fair/mapf.mp4) |
| `L4_s6010_trade` | trade | 6.1% severe | [461 steps, 0 contacts](demos/showcase/L4_s6010_trade/neurorl.mp4) | [timeout](demos/showcase/L4_s6010_trade/magic.mp4) | [finishes](demos/showcase/L4_s6010_trade/dcg.mp4) | [34 contacts](demos/showcase/L4_s6010_trade/mapf.mp4) |
| `L5_s6058_trade` | trade | 7.4% extreme | [954 steps, 0 contacts](demos/showcase/L5_s6058_trade/neurorl.mp4) | [finishes](demos/showcase/L5_s6058_trade/magic.mp4) | [finishes](demos/showcase/L5_s6058_trade/dcg.mp4) | [41 contacts](demos/showcase/L5_s6058_trade/mapf.mp4) |
| `L6_s6013_trade` | trade | 8.5% saturated | [539 steps, 0 contacts](demos/showcase/L6_s6013_trade/neurorl.mp4) | [timeout](demos/showcase/L6_s6013_trade/magic.mp4) | [finishes](demos/showcase/L6_s6013_trade/dcg.mp4) | [25 contacts](demos/showcase/L6_s6013_trade/mapf.mp4) |
| `L4_s6033_only` | only | 6.1% severe | [548 steps, 0 contacts](demos/showcase/L4_s6033_only/neurorl.mp4) | [timeout, 17 contacts](demos/showcase/L4_s6033_only/magic.mp4) | [timeout](demos/showcase/L4_s6033_only/dcg.mp4) | [timeout](demos/showcase/L4_s6033_only/mapf.mp4) |
| `L5_s6011_only` | only | 7.4% extreme | [462 steps, 0 contacts](demos/showcase/L5_s6011_only/neurorl.mp4) | [timeout](demos/showcase/L5_s6011_only/magic.mp4) | [timeout](demos/showcase/L5_s6011_only/dcg.mp4) | [timeout](demos/showcase/L5_s6011_only/mapf.mp4) |
| `L6_s6024_only` | only | 8.5% saturated | [671 steps, 0 contacts](demos/showcase/L6_s6024_only/neurorl.mp4) | [timeout](demos/showcase/L6_s6024_only/magic.mp4) | [timeout](demos/showcase/L6_s6024_only/dcg.mp4) | [timeout](demos/showcase/L6_s6024_only/mapf.mp4) |

Full per-scenario measured outcomes, including minimum health and time in formation for every framework: [`demos/showcase/MANIFEST.md`](demos/showcase/MANIFEST.md).

### Classical planners across the difficulty ladder

The video counterpart of the paper's classical grid. One representative episode per planner per level, on the same map in every row. Both A\* planners are privileged with the ground-truth obstacle map.

| Coverage | NeuroRL | MA-PF | MA-A\* | Agent A\* |
|---|---|---|---|---|
| 2.4% sparse | [still](demos/classical/neurorl/neurorl_L1_sparse.png) | [video](demos/classical/mapf/mapf_L1_sparse.mp4) | [video](demos/classical/ma_astar/ma_astar_L1_sparse.mp4) | [video](demos/classical/agent_astar/agent_astar_L1_sparse.mp4) |
| 3.7% moderate | [still](demos/classical/neurorl/neurorl_L2_moderate.png) | [video](demos/classical/mapf/mapf_L2_moderate.mp4) | [video](demos/classical/ma_astar/ma_astar_L2_moderate.mp4) | [video](demos/classical/agent_astar/agent_astar_L2_moderate.mp4) |
| 4.9% dense | [still](demos/classical/neurorl/neurorl_L3_dense.png) | [video](demos/classical/mapf/mapf_L3_dense.mp4) | [video](demos/classical/ma_astar/ma_astar_L3_dense.mp4) | [video](demos/classical/agent_astar/agent_astar_L3_dense.mp4) |
| 6.1% severe | [still](demos/classical/neurorl/neurorl_L4_severe.png) | [video](demos/classical/mapf/mapf_L4_severe.mp4) | [video](demos/classical/ma_astar/ma_astar_L4_severe.mp4) | [video](demos/classical/agent_astar/agent_astar_L4_severe.mp4) |
| 7.4% extreme | [still](demos/classical/neurorl/neurorl_L5_extreme.png) | [video](demos/classical/mapf/mapf_L5_extreme.mp4) | [video](demos/classical/ma_astar/ma_astar_L5_extreme.mp4) | [video](demos/classical/agent_astar/agent_astar_L5_extreme.mp4) |
| 8.5% saturated | [still](demos/classical/neurorl/neurorl_L6_saturated.png) | [video](demos/classical/mapf/mapf_L6_saturated.mp4) | [video](demos/classical/ma_astar/ma_astar_L6_saturated.mp4) | [video](demos/classical/agent_astar/agent_astar_L6_saturated.mp4) |

The NeuroRL column is a representative **still** per level: the classical comparison was rendered as stills for NeuroRL and as video for the planners. For NeuroRL in motion at every difficulty level, use the [framework comparison](#framework-comparison-across-the-difficulty-ladder) section above, which has video for all four frameworks on matched seeds.

### Configurability

The same policy architecture, the same map, the same goal-pose structure — only the intrinsic reward weights differ. These are the videos behind the reward-configurability table.

| Demo | What changes |
|---|---|
| [`v21_n3_base_L5.mp4`](demos/configurability/v21_n3_base_L5.mp4) | balanced intrinsic weights, 3 rovers, 7.4% coverage |
| [`v21_n3_assim_L5.mp4`](demos/configurability/v21_n3_assim_L5.mp4) | behavioral-assimilation prioritized |
| [`v21_n3_swirl08_L5.mp4`](demos/configurability/v21_n3_swirl08_L5.mp4) | tangential deflection gain raised — formation traded for throughput |
| [`compare_s6016.mp4`](demos/configurability/compare_s6016.mp4) | the three configurations side by side on one seed |
| [`n4_corridor_trained.mp4`](demos/configurability/n4_corridor_trained.mp4) | four rovers negotiating a corridor |

Each configuration also has a fixed-seed variant (`*_s6016.mp4`) for frame-matched comparison.

---

## Code — coming soon

The implementation is being prepared for release and will be published in this repository. It will include the environment and graph-edit action space, the health dynamics, the Laplacian controller, the training and evaluation entry points, the campaign runners that produce the tables above, and the figure builders.

Once published, the workflow will be:

**Install** (Python 3.10+):

```bash
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install -e .                   # or: pip install -r requirements.txt for a pinned env
```

`pip install -e .` pulls the full stack, including PyTorch and Stable-Baselines3.

**Train one configuration** (four seeds per arm were used in the paper):

```bash
python scripts/train_ppo_graph_edit.py \
    --cfg configs/learned_baselines/user_map_neurorl_v19_base.yaml
```

**Run an evaluation campaign across the difficulty ladder:**

```bash
python tools/run_v20_campaign.py
```

**Rebuild the result tables from the evaluation CSVs:**

```bash
python tools/finalize_v19.py --prefix v22 --latex out/tables_v22.tex
```

**Re-render these demo videos:**

```bash
python tools/rebuild_demos.py
```

Planned layout:

| path | what it is |
|---|---|
| `neurorl_gt/` | the package: environment, graph edit action space, health dynamics, controller |
| `configs/learned_baselines/` | one config per reward configuration (`base`, `form`, `assim`, `edit`, `path`, `swirl08`) |
| `configs/eval/` | evaluation variant ladders (obstacle density L1–L6, team size) |
| `scripts/` | train and render entry points |
| `tools/` | campaign runners, table builders, figure builders, measurement probes |
| `tests/` | pytest suite |

Configurations differ **only** in the intrinsic weights of the health dynamics; the coefficients of the return are identical in every configuration.

---

## Repository layout

```
demos/
├── highlights/        the two videos at the top of this page
├── showcase/          9 scenarios x 4 frameworks, same seed within a scenario
│   └── MANIFEST.md    per-scenario measured outcomes
├── classical/         4 planners x 6 difficulty levels
└── configurability/   reward configurations on a fixed map
```

## Citation

Daniel de la Calle, Dang Tran, Sharlene Newman, Amir Rahmani, and Hongsheng He. *NeuroRL: Neuromodulated Reinforcement Learning with Graph Transformations for Robot Teaming.* Submitted to The International Journal of Robotics Research.

The University of Alabama; NASA Jet Propulsion Laboratory.

A `CITATION.cff` will accompany the code release.
