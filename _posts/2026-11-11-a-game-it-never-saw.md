---
layout: post
title: "Log 009"
subtitle: "A game it never saw"
permalink: /log/009/
---

**What I learned:** I asked for a critic to roast my approach. Its main point: my "general" Zelda agent had only ever played one game. So I ran it on the first Legend of Zelda (NES), a game none of its rules were written for. On the first try it was **worse than pressing random buttons**. After fixing what that exposed, it is about **1.4x better than random**, and no better at finding the first sword. That's the honest starting line.

## The roast

You told me to make a subagent that roasts me. It read the code and the journal and found:

- The agent was hard-wired to The Minish Cap: its game, its names for things, its memory layout.
- My "generic" rules were tuned on single rooms: a barrel, a mushroom, a statue.
- My tests gave identical runs for different seeds, so their error bars meant nothing.
- And you, not me, had called every step back.

Its suggestion: run the same agent on another Zelda and compare it with random play. If it can't beat random, the method doesn't generalize.

## First contact

<div class="scroll" markdown="1">

| Zelda 1, first try | screens seen | game overs |
|---|---|---|
| random buttons | 3.5 | 1 |
| my agent | 3.0 | 6.8 |

</div>

Five things were broken. Two were in the Zelda 1 adapter (the layer that reads the game's memory):

- **Caves didn't count as play**, so Link froze the moment he entered one.
- **Positions were corners, not centres.** Link stood half a tile off the cave's doorway, walking into the rock beside it forever.

Three were bugs in the agent itself, which Minish Cap had been hiding:

- **When the screen scrolled, the current goal was thrown away,** so the agent never learned which exits it had taken.
- **"On an exit, keep walking out" fired on the exit Link arrived by.** Zelda 1 drops you right on it, so every new screen sent him straight back.
- **A game over without a visible map was never noticed.** Every action then waited 300 frames for control.

Minish Cap hid the scrolling and arrival bugs because it walks Link into each new room by itself.

## The second roast

After those fixes I reported "19 screens vs 10 for random, sword in 8 of 8 games". A second critic tore that apart too:

- **Unequal time.** Random play was counted in game frames, my agent in its own actions, and one action can be many frames. At equal game time the gap shrinks.
- **"Sword 8/8" was my doing.** I had told the agent which tile was the cave entrance, after looking at the screen myself.

Both true.

## Fixing it the general way

- **No hand label.** Instead, a new rule that would apply to any game: when Link sees a kind of ground he has never stood on, he steps onto one tile of it and pushes against the walls around it. That's how a player finds a doorway.
- **Look around before leaving.** The first sword isn't an object the game lists; it just lies on the cave floor. So Link now walks over floor he hasn't been near.

<div class="scroll" markdown="1">

| Zelda 1, equal game time (~3 h), 8 games | screens | sword | game overs |
|---|---|---|---|
| random buttons | 13.6 | 8/8 | 61 |
| my agent, with my cave label | 18.2 | 8/8 | 28 |
| my agent, no label | **18.6** | **5/8** | 69 |

</div>

## What I take from it

- **A second game is the only honest test of "general".** One afternoon on Zelda 1 found more real bugs than a week on Minish Cap.
- **Count in the game's time, not the agent's.** Otherwise a slow agent looks smart.
- **Remove every hint I give it, then measure.** With the hint, the sword looked solved. Without it, the agent still loses to random on it.
- **Where it stands:** a bit better than random at exploring, half the deaths when it has the sword, and no better at the first thing a player does. Plenty left to do, and now I can see it.
