# Demo batch manifest

Rendered from `results/learned_baselines/v18a/all_episodes.csv` -- every video matches a row in that file.

Selection rule, applied to that file and nothing else: three kinds of episode
(fair / trade / only), one scenario per difficulty level within a kind, and within
each (kind, level) bucket the episode closest to that bucket's MEDIAN time in
formation. So these are representative episodes, not best cases.

Each subfolder is one scenario: same level, same seed, therefore the same sampled
layout, start jitter and corridor for all four frameworks. View a folder as a 2x2
panel to compare them directly.

Each video runs past the scored end of its episode, until the rovers are parked on
their goal markers. The benchmark stops the instant every rover is within the 0.30 m
success tolerance, which on screen is still short of the markers -- those extra frames
are labelled `settling, +N steps not scored` in the HUD and change no reported number.
A video that ends with the team still mid-map is a timeout at the step horizon, not a
truncated render.

**Health is NeuroRL-GT's objective and no baseline's.** The HUD shows a health trace
for every controller because health is environment state, but the baselines carry no
health term (`r_intrinsic == 0`). Read their trace as the cost of their route choice,
not as a score they were pursuing.

| scenario | kind | level | seed | what to watch |
|---|---|---|---|---|
| `L1_s6016_fair` | fair | N3_RAND_L1_sparse | 6016 | fair: NeuroRL-GT finishes in 339 steps with 0 contacts and 2% within 5 deg; MAGIC times out (89 contacts), DCG finishes (0 contacts), MAPF finishes (20 contacts) |
| `L2_s6018_fair` | fair | N3_RAND_L2_moderate | 6018 | fair: NeuroRL-GT finishes in 336 steps with 0 contacts and 8% within 5 deg; MAGIC finishes (0 contacts), DCG finishes (0 contacts), MAPF finishes (27 contacts) |
| `L3_s6018_fair` | fair | N3_RAND_L3_dense | 6018 | fair: NeuroRL-GT finishes in 363 steps with 0 contacts and 7% within 5 deg; MAGIC times out (119 contacts), DCG finishes (0 contacts), MAPF finishes (0 contacts) |
| `L6_s6013_trade` | trade | N3_RAND_L6_saturated | 6013 | trade: NeuroRL-GT finishes in 539 steps with 0 contacts and 6% within 5 deg; MAGIC times out (0 contacts), DCG finishes (0 contacts), MAPF finishes (25 contacts) |
| `L5_s6058_trade` | trade | N3_RAND_L5_extreme | 6058 | trade: NeuroRL-GT finishes in 954 steps with 0 contacts and 7% within 5 deg; MAGIC finishes (0 contacts), DCG finishes (0 contacts), MAPF finishes (41 contacts) |
| `L4_s6010_trade` | trade | N3_RAND_L4_severe | 6010 | trade: NeuroRL-GT finishes in 461 steps with 0 contacts and 3% within 5 deg; MAGIC times out (0 contacts), DCG finishes (0 contacts), MAPF finishes (34 contacts) |
| `L6_s6024_only` | only | N3_RAND_L6_saturated | 6024 | only: NeuroRL-GT finishes in 671 steps with 0 contacts and 6% within 5 deg; MAGIC times out (0 contacts), DCG times out (0 contacts), MAPF times out (0 contacts) |
| `L5_s6011_only` | only | N3_RAND_L5_extreme | 6011 | only: NeuroRL-GT finishes in 462 steps with 0 contacts and 6% within 5 deg; MAGIC times out (0 contacts), DCG times out (0 contacts), MAPF times out (0 contacts) |
| `L4_s6033_only` | only | N3_RAND_L4_severe | 6033 | only: NeuroRL-GT finishes in 548 steps with 0 contacts and 9% within 5 deg; MAGIC times out (17 contacts), DCG times out (0 contacts), MAPF times out (0 contacts) |

## Measured outcomes (from the results file, not from the render)

### `L1_s6016_fair` -- N3_RAND_L1_sparse, seed 6016

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 339 | 0 | 0.921 | 1.8% |
| DCG | yes | 339 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | no | 1500 | 89 | n/a (no health objective) | 0.0% |
| MAPF | yes | 352 | 20 | n/a (no health objective) | 0.0% |

### `L2_s6018_fair` -- N3_RAND_L2_moderate, seed 6018

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 336 | 0 | 0.815 | 8.0% |
| DCG | yes | 344 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | yes | 721 | 0 | n/a (no health objective) | 0.4% |
| MAPF | yes | 343 | 27 | n/a (no health objective) | 0.0% |

### `L3_s6018_fair` -- N3_RAND_L3_dense, seed 6018

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 363 | 0 | 0.825 | 7.4% |
| DCG | yes | 333 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | no | 1500 | 119 | n/a (no health objective) | 0.0% |
| MAPF | yes | 332 | 0 | n/a (no health objective) | 0.0% |

### `L6_s6013_trade` -- N3_RAND_L6_saturated, seed 6013

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 539 | 0 | 0.720 | 6.3% |
| DCG | yes | 349 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAPF | yes | 321 | 25 | n/a (no health objective) | 0.0% |

### `L5_s6058_trade` -- N3_RAND_L5_extreme, seed 6058

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 954 | 0 | 0.000 | 6.9% |
| DCG | yes | 357 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | yes | 698 | 0 | n/a (no health objective) | 0.6% |
| MAPF | yes | 384 | 41 | n/a (no health objective) | 0.0% |

### `L4_s6010_trade` -- N3_RAND_L4_severe, seed 6010

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 461 | 0 | 0.626 | 3.2% |
| DCG | yes | 329 | 0 | n/a (no health objective) | 4.2% |
| MAGIC | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAPF | yes | 327 | 34 | n/a (no health objective) | 0.0% |

### `L6_s6024_only` -- N3_RAND_L6_saturated, seed 6024

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 671 | 0 | 0.000 | 5.8% |
| DCG | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAPF | no | 1500 | 0 | n/a (no health objective) | 1.0% |

### `L5_s6011_only` -- N3_RAND_L5_extreme, seed 6011

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 462 | 0 | 0.849 | 6.5% |
| DCG | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAPF | no | 1500 | 0 | n/a (no health objective) | 0.0% |

### `L4_s6033_only` -- N3_RAND_L4_severe, seed 6033

| framework | success | steps | collisions | min health | in formation |
|---|---|---|---|---|---|
| NeuroRL-GT | yes | 548 | 0 | 0.408 | 8.6% |
| DCG | no | 1500 | 0 | n/a (no health objective) | 0.0% |
| MAGIC | no | 1500 | 17 | n/a (no health objective) | 0.0% |
| MAPF | no | 1500 | 0 | n/a (no health objective) | 0.0% |
