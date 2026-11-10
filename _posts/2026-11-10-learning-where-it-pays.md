---
layout: post
title: "Log 003"
subtitle: "Learning where it pays"
permalink: /log/003/
---

**What I learned:** reinforcement learning fails when the reward is rare and late (solving a puzzle), and works when the signal is dense and immediate (staying alive, "did this action change something?"). Using learning only there took my agent to **26 of 66** held-out puzzles with only **4** game overs.

<div class="scroll" markdown="1">

| held-out test | runs | solved | game over |
|---|---|---|---|
| brain + action model + survival reflex | 66 | **26** | **4** |
| brain + action model | 66 | 23 | 14 |
| brain alone | 33 | 8 | 4 |
| random play | 33 | 6 | 16 |

</div>

The last two rows were measured on one seed (33 runs), the others on two.

## Where reinforcement learning failed: choosing what to try

I tried three times to have the brain learn by reinforcement which experiments pay off (the Bandit Knight in the [bestiary]({{ '/bestiary/' | relative_url }})):

- **v1 farmed rooms:** a new room paid every time, so it shuttled through doors.
- **v2 cancelled its own starting knowledge.**
- **v3 learned that fighting pays, and died more.**

A solved puzzle is rare and comes long after the action that mattered. That's exactly where reinforcement learning is weakest.

## Where learning works

- **The action model (Lore Owl).** Every try my agent makes is already labelled: "I pulled the lever, something changed." From thousands of these it learns what things do (pots lift, levers pull) and ranks experiments with that. Dense signal, no guessing which earlier action deserves the credit.
- **The survival reflex (Guardian Slime).** Reinforcement learning trained only to stay alive near enemies, with the current button sequences. When an enemy is close or Link gets hurt, it takes over. Game overs dropped by 70%.

## Bugs I hit on the way

- **I only logged successful pushes,** so the model thought pushing always works, and pushed pots among enemies.
- **My first survival reward paid for enemies disappearing,** and Sluggulas disappear on their own: 204 "kills" in 300 steps.
- **An old reinforcement-learning policy reused as a reflex** made things worse: it was trained on older button sequences.

## What I take from it

- **Small differences are noise.** Every claim now uses 2 seeds per held-out puzzle.
- **Learn from dense signals.** Reflexes and "what does this do" work; learning the value of puzzle steps from rare rewards hasn't.
- **Next:** the barrel room's out-of-reach starts and room 3 are still at zero. They need getting around a dungeon, not just one room.
