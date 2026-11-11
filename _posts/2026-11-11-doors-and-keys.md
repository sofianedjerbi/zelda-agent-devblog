---
layout: post
title: "Log 012"
subtitle: "Doors and keys"
permalink: /log/012/
kpi: "base,random"
---

**What I learned:** my agent now walks into caves and into the first dungeon of Zelda 1. Nobody told it what a door looks like. It learned that from walking through one. Inside the dungeon it stalls after a few rooms.

## One cave per game

At 43 screens, Link explored the overworld well but entered only one cave per game, the sword cave.

The reason: the agent tries any **kind of ground it has never stood on**. The first cave mouth was new, so Link stepped on it and went in. Every other cave mouth is the same kind of ground. Old news, so he walked past them all.

## Learning what a door is

A player who walks into one dark doorway tries the next one. So the agent now counts, for each kind of ground:

- how often stepping on it (away from the screen's edge) took Link into another room
- how often it didn't

If it led somewhere at least once in 20 steps, that kind is a **door**, on every screen.

<div class="scroll" markdown="1">

| kind of ground | led into a room | steps on it | door? |
|---|---|---|---|
| cave mouth | 6 | 63 | yes |
| sand | 15 | 19,027 | no |

</div>

One detail mattered: the game shows the new room one action late, so each entry is credited to the last ground Link stood on before it.

<div class="scroll" markdown="1">

| Zelda 1, 80k steps, 16 games | before | now |
|---|---|---|
| caves per game | 1 | 4 to 5 |
| second item found | 1 / 16 | **6 / 16** |
| first dungeon entered | 0 of 2 checked | **6 / 16** |
| screens | 42.9 | 45.9 |

</div>

## Two more traps, two more pictures

- **Half on a rock.** At a screen's edge Link stood half on a rock tile. The map grows outward from his tile, and that tile was rock, so the whole screen read as wall. Now every tile under his body counts.
- **5 pixels low.** In a one-tile corridor Link stood 5 pixels too low, so every step clipped the rock below. Lining him up when a step is refused fixes that game, but over 16 games it's a wash, so it stays off.

## Inside the dungeon

The first dungeon stopped Link after 3 rooms. The picture showed why: he was **holding a key**, and the way on was a locked door he had bumped into early on. The agent had learned that door as a wall.

A player with a new key goes back to the doors. So now: **when Link gets a key, walls that refused him in only one or two places are tried again.** Common walls stay walls.

<div class="scroll" markdown="1">

| | dungeon rooms (16 games) |
|---|---|
| before | 19 |
| key retry | **22** |

</div>

Small, but in one game Link unlocked the door and went on. He then got stuck in the next room for 29,000 actions, trying every exit, including the one he came in by. That trace is next.

## What I tried that didn't work

- **Fight when the exits won't open** (Zelda's shutter doors open once the room's enemies are dead): no more dungeon rooms. Off.

## What I take from it

- **Learn what a thing does, not what it looks like.** A door is "ground that took me somewhere", counted, not labelled.
- **New stuff in the pocket means old walls deserve a second look.**
- **Next:** the room where every exit fails.
