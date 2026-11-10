---
layout: post
title: "Log 001"
subtitle: "The story so far"
permalink: /log/001/
---

**What I learned:** reinforcement learning on its own learns to *move* around a Zelda game, but it doesn't *discover* puzzles. And scores in a simulator told me nothing about the real game. So I split the agent into a brain that decides what to try and hands that do it.

**The goal:** an AI that finishes any top-down Zelda, on one Mac, with local models only.
**Rule 1:** always step back: what's slow, what's dumb, what to drop.
**Rule 2:** no memorising. Every test uses rooms and dungeons the agent never trained on.

## Brute force reached far, for the wrong reason

I started with reinforcement learning on *The Minish Cap* itself, plus Go-Explore: save the game at every new place, restart from the most promising ones.

It reached **Deepwood Shrine, rooms 3 to 5**. But it got there by remembering routes through *this* game. That's memorising, so I dropped it as the main path.

## A simulator gave me great scores that meant nothing

I built a fast simulator (about 2,000 steps per second) that makes a new random dungeon every episode: keys, switches, blocks, pots, levers, enemies.

- On generated dungeons it had never seen: **88% solved** (random play: 0%).
- In the real game: **random level**. It fought an enemy the sword can't hurt until it died, and pushed into walls.

## What the simulator did help with

I trained on the real game for 300,000 actions, once starting from the simulator's weights and once from scratch. Then I tested on 120 Deepwood starts neither had trained on:

<div class="scroll" markdown="1">

| | reached a new room | game over |
|---|---|---|
| pretrained | **78** | 24 |
| from scratch | 54 | 32 |
| random | 45 | 28 |

</div>

- **Getting around: a real gain** (p ≈ 0.002). After 1M actions: 110 of 120 starts reach a new room, only 6 game overs.
- **Puzzles: no gain.** 12 solved against random's 8.

## I broke the model's view

The network saw input channels with names I chose: "block", "pot", "switch". Anything outside that list got squeezed into the nearest name, and every new mechanic meant a new channel and retraining.

So I removed the names. The new view is the room's shape, "something is here", and what the agent itself learned by acting: *I hit it, nothing happened. I pushed it, it moved. It hurt me.*

## Brain and hands

That's closer to how people play: pick something to try, try it, remember what happened, pick the next thing.

- **Hands:** do what they're told ("go there", "hit that", "push that").
- **Brain:** decides what to try next from memory: untried things, shut doors, marked floor, unexplored exits. It gets bored of tries that do nothing.

With *perfect* hands, the brain went from **37% to 85%** of unseen generated dungeons through four general habits:

1. try every action on a shut door, not just pushing;
2. a lasting change (something opens or appears) isn't the same as moving a thing;
3. keep a map of the dungeon, and go back to a locked door once you have a key;
4. try a room properly before leaving it.

With *learned* hands it scored **0%**: the hands had learned to farm points by stepping back through the door they came in.

## What I take from it

- **Splitting the agent into parts worked**, and so did a test fast enough to iterate on.
- **Proxy scores wasted my time.** Only the real game counts.
- **Next:** brain plus hands on the real game, against random play.
