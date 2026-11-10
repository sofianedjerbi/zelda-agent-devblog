---
layout: post
title: "Log 005"
subtitle: "The map is not the room"
permalink: /log/005/
---

**What I learned:** most of my agent's mistakes come from its map, not from its thinking. It reads each room's layout from the game's memory, and that layout lies in small, specific ways. Today I fixed five of those lies. Out of 24 fresh games, **15 now climb out of the giant barrel** onto the ledge above it. This morning, none did.

## How my agent sees a room

It never looks at the screen. Every step, it reads the game's memory and builds a grid: floor, wall, water, exits, and the objects in the room. Everything else is built on that grid: the brain picks a goal, the hands find a path.

So if the grid is wrong, a perfect brain still fails. Here is what I found wrong, one lie at a time.

## Lie 1: inside the barrel, the map is a rectangle

The giant barrel in Deepwood is a room of its own. In memory, its layout is a plain rectangle of floor. The real walls are curved, and the exits are windows. None of that is in the grid.

<div class="clips">
  <figure>
    <video src="{{ '/assets/video/barrel-window-ledge.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Seed 33, 2x speed: into the barrel, out of its window, onto the ledge, then the statue in the side wing</figcaption>
  </figure>
</div>

I found the exit by hand: walk up through the top-left window and Link lands on a ledge above the barrel room. The brain had only ever found it by luck.

I tried teaching it to **walk along every wall** to find hidden exits. It made things much worse: a big room never runs out of wall, so games stayed stuck in the first hall. Barrel room reached in **1 of 12** games. I removed it.

## Lie 2: the door I came through looks new

When Link comes out of the barrel's window, he lands 5 tiles away from the barrel's door. My rule for "the door I came in by" only looked 3 tiles back. So the barrel's door looked like an exit nobody had tried, and Link walked straight back into the barrel.

**Fix:** if there's an opening straight behind Link, up to 6 tiles, that's the way he came.

## Lie 3: an exit is one tile

The shrine's front exit is 5 tiles wide. My map counted each tile as its own exit. So Link walked out, came back, and walked out through the next tile: x=5, 6, 7, 8, 9. Dozens of trips per game.

**Fix:** touching tiles form one exit. Once one is used, they're all known.

## Lie 4: statues are walls

Three statues block the top exit of the entrance hall, and they come back every time Link re-enters. The hands treated them as walls, so the walk to that exit failed again and again.

A player would push one aside. I added that, and it got worse: the shortest path pushed statues *into walls*, about **50 failed walks per game**. Only the middle statue moves, pushed left.

**Fix:** a walk may push an object only if the tile behind it is free. Time in the hall dropped from 221 goals per game to **136**.

## Lie 5: decoration looks like floor

Next to a button, my hands wanted to stand on one particular side. The path there went through a stone head the map calls floor. After bumping it twice, the hands knew the path was blocked and returned "do nothing", **400 times in a row**. Link was right next to the button, so my "stuck" check never fired.

**Fixes:** if the wanted side is blocked, any side will do. A goal that hasn't moved Link in 8 actions ends.

## Results

<div class="scroll" markdown="1">

| 24 fresh games from the entrance | lever room | barrel room | inside the barrel | above the barrel |
|---|---|---|---|---|
| Log 004 | 12/12 | 8/12 | not measured | 0 |
| today | **24/24** | **18/24** | **15/24** | **15/24** |

</div>

## What I take from it

- **Read failures at the bottom first.** Every fix today was in the map or the hands. None was in the brain.
- **Each fix is about how games work, not this room.** Wide exits, doors you came through and statues you can push exist in every Zelda.
- **The map will keep lying.** Next I want the agent to notice by itself when the map and reality disagree, instead of me finding each case.
