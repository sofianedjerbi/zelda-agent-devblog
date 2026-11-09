---
layout: post
title: "Log 003"
subtitle: "Learning where it pays"
permalink: /log/003/
---

**Result:** on 33 real *Minish Cap* puzzles never debugged on, run twice each (66 runs), the agent now solves **26** with only **4** game overs.

<div class="scroll" markdown="1">

| held-out test, 66 runs | solved | game over |
|---|---|---|
| brain + action model + survival reflex | **26** | **4** |
| brain + action model | 23 | 14 |
| brain alone | 16 | 8 |
| random play | 12 | 32 |

</div>

The brain-alone and random rows are from one seed, doubled to compare.

## Combine RL and reasoning, but where each is good

The request was RL's learning and reflexes, plus reasoning. Three tries at getting the brain's *choices* learned by RL failed (the Bandit Knight in the [bestiary]({{ '/bestiary/' | relative_url }})):

- **v1 farmed rooms:** a new room paid every time, so it shuttled through doors.
- **v2 cancelled its own starting knowledge.**
- **v3 learned that fighting pays, and died more.**

Puzzle rewards are rare and delayed, which is exactly where RL is weakest.

## Two learners that work

- **Lore Owl, the action model.** Every try the agent makes is already labelled: "I pulled the lever, something changed." From thousands of these it learns what things do (pots lift, levers pull) and ranks experiments by it. The signal is dense, with no credit-assignment problem.
- **Guardian Slime, the survival reflex.** RL trained only to stay alive near enemies, with today's action macros. When an enemy is close or Link gets hurt, it takes over. Game overs dropped by 70%.

## Bugs found on the way

- **Only successful pushes were logged,** so the model thought pushing always works and pushed pots among enemies.
- **The first survival reward paid for enemies vanishing,** and Sluggulas vanish on their own: 204 "kills" in 300 steps.
- **An old RL policy reused as a reflex** made things worse: it was trained on older button macros.

## Step back

- **Noise:** small differences were noise-sized, so every claim now uses 2 seeds per held-out puzzle.
- **What works:** learning from dense signals, and RL for reflexes. Learning sparse puzzle values with RL hasn't worked yet.
- **Next:** the barrel room's out-of-reach starts and room 3 are still at zero. They need getting around a dungeon, not just one room.
