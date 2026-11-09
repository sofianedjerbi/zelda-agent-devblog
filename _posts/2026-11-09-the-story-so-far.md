---
layout: post
title: "The story so far"
---

**Goal:** an AI that finishes any top-down Zelda, on one Mac, with local models only.
**Rule 1:** always step back: what's slow, what's dumb, what to drop.
**Rule 2:** no memorising. Every test uses rooms and dungeons the agent never trained on.

## 1. Brute force in the real game

Reinforcement learning on *The Minish Cap* itself, plus Go-Explore (save a state at every new place, restart from the most promising ones).

- It reached **Deepwood Shrine, rooms 3 to 5**.
- But it works by remembering routes through *this* game. That's overfitting, so we dropped it as the main path.

## 2. Generated dungeons

A fast simulator (~2,000 steps/s) that builds a new random dungeon every episode: keys, switches, blocks, pots, levers, enemies.

- In generated dungeons it had never seen: **88% solved** (random play: 0%).
- In the real game: **random level**. It fought an enemy the sword can't hurt until it died, and pushed into walls.
- Lesson: simulator scores were the wrong yardstick.

## 3. Does the simulator help at all?

The same real-game training, 300k actions, starting from the simulator weights or from scratch. Then 120 Deepwood starts neither had trained on:

<div class="scroll" markdown="1">

| | reached a new room | game over |
|---|---|---|
| pretrained | **78** | 24 |
| from scratch | 54 | 32 |
| random | 45 | 28 |

</div>

- **Getting around: a real gain** (p ≈ 0.002). After 1M actions: 110 of 120 starts reach a new room, only 6 game overs.
- **Puzzles: no gain.** 12 solved against random's 8.
- Lesson: reinforcement learning learns *movement* that transfers. It doesn't *discover* multi-step puzzles.

## 4. Breaking the model

The network saw channels with names I picked: "block", "pot", "switch". Anything real outside that list got squeezed into the nearest name. So every new mechanic meant a new channel and more retraining.

New view: no names. Just the room's shape, "something is here", and what the agent itself learned by acting: *I hit it, nothing happened. I pushed it, it moved. It hurt me.*

## 5. Brain and hands

How people actually play: pick a goal, try it, remember what happened, choose the next try.

- **Hands:** a small policy that does what it's told ("go there", "hit that", "push that"). Trained with plentiful rewards: did you do it?
- **Brain:** decides what to try next from memory: untried things, shut doors, marked floor, unexplored exits. It gets bored of useless tries. A local LLM can take this role, but has to beat the simple version first.

Testing the brain with *perfect* hands (seconds per 100 dungeons) took it from **37% to 85%** of unseen dungeons, through four general habits:

1. try every action on a shut door, not just pushing;
2. a lasting change (something opens, appears) isn't the same as merely moving a thing;
3. keep a map of the dungeon, and go back to a locked door once you have a key;
4. try a room properly before leaving it.

## Where it stands (9 Nov)

- Brain + perfect hands: **85%**.
- Brain + learned hands: **0%**. The hands had learned to farm points by stepping back through the door they came in. Fixed, retraining.
- Real game with the new design: **not tested yet.** That's the only result that counts.

## Step back

- **What worked:** splitting the agent into parts, a test fast enough to iterate, and reading *why* it fails instead of training longer.
- **What wasted time:** trusting proxy scores, too many runs at once, and bugs in my own measuring.
- **Next:** brain + learned hands in generated dungeons, then 120 held-out real Deepwood starts against random play.
