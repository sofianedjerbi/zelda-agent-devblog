---
layout: post
title: "Log 010"
subtitle: "Standing in the trees"
permalink: /log/010/
---

**What I learned:** on the first Zelda, my agent now finds **26 screens** in the time random buttons find **14**. Last log it was 18.6. Almost all of the gain came from one bug: Link stood half inside a row of trees, and the agent decided the ground under his own feet was a wall.

## The frontier, fixed

When a screen has nothing new left, Link walks back to the nearest screen with an exit he hasn't tried. Go-Explore does the same with save states; this does it from memory.

It had a bug. Link arrived at that screen and ignored the exit he came for, so he walked straight back. In one game he went village, next screen, village... hundreds of times, 5 to 7 steps each way.

Now he tries that exit first. If it fails there, it stops counting as unexplored.

## The tree line

One Zelda 1 game stayed on 7 screens for the whole game. On one screen Link stood still for 11,400 actions.

The cause:

- Zelda 1 lets Link tuck half into a row of trees.
- The agent learns "trees are solid" the first time one blocks him.
- The 16-pixel block under Link then read as a tree. **His own tile became a wall.**
- The agent builds the map outward from where Link stands. From a wall, it found nothing: no floor, no exits, no goal.

The fix works for any game: **the tile Link stands on is never a wall.**

## Measured one at a time

<div class="scroll" markdown="1">

| Zelda 1, 80k steps, 8 games | screens | game overs |
|---|---|---|
| last log | 19.5 | 33 |
| + frontier fix | 20.0 | 17 |
| + own tile never a wall | **25.5** | 48 |
| own tile fix taken out again | 20.0 | 17 |
| exits split by side taken out | 25.5 | 48 |

</div>

I'd also split exits by side of the screen. Taking it out changed nothing, not a single decision, so it's gone.

## Two fixes that didn't work

More screens means more fights. Game overs went up, and each one sends Link back to the first screen.

I traced the deaths. Link has 3 hearts all game, and he dies on the same few screens full of enemies, just walking through.

- **Avoid screens where he died 3 times:** 21.8 screens. Those screens are crossroads; avoiding them walled him in. Off.
- **Swing at enemies 2 tiles away, not just 1:** 20.8 screens. Off.

Two misses in a row, so I stopped patching and checked the measurement instead.

## Checked on fresh games

16 games I had never run (new seeds), same budget for both:

<div class="scroll" markdown="1">

| Zelda 1, 80k steps, 16 new games | screens (95% range) | game overs |
|---|---|---|
| random buttons | 14.1 (13.2 - 15.2) | 59 |
| my agent | **26.0 (24.0 - 28.1)** | 43 |

</div>

The ranges don't touch. Minish Cap is unchanged (24-26 of 48 on the quick test; the same code scores anywhere from 21 to 29).

## What I take from it

- **The biggest gain was a bug, not a new idea.** One game that got stuck, traced to the end, was worth more than any new rule.
- **Take every fix out again and measure.** One of my two fixes did all the work. The other did nothing.
- **Still weak:** every game finds the sword (16 / 16), but only 1 in 16 finds a second item, and Link fights badly. That's next.
