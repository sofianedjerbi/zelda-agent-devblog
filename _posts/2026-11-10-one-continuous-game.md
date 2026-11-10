---
layout: post
title: "Log 004"
subtitle: "One continuous game"
permalink: /log/004/
---

**What I learned:** my benches were hiding the real problems. A bench drops Link inside one room. A real game makes him walk in from somewhere else, and almost every bug I found today only shows up on arrival. Once I tested that way and fixed what broke, one game with no reloads went from **8 to 12 dungeon flags** and reached the barrel room for the first time.

## The test I should have had from the start

One continuous game from the Deepwood entrance: 20,000 to 30,000 actions, no reloads, game overs allowed (the continue screen), several seeds.

This morning, every game stopped at 8 flags and stood still for 15,000 actions in the lever room. Yet on the bench my agent pulls that same lever 7 times out of 8.

## Why it stood still

Not the puzzle. Six small things, all in the lowest layers:

1. **It decided on the door tile.** Coming through a door, the game walks Link in by itself. Seen from the door tile, the room is all walls: 1 goal out of 82 looked reachable. So it walked straight back out. Now I make Link wait until he has stopped and is off the door. Lever from a real arrival: **0/6 to 6/6**.
2. **Statues reset.** Three statues block the hall's top door every time Link comes back. My map treated them as walls, so the room looked like a 4-tile box. One game spent 213 goals in that box.
3. **Walls that stopped being walls.** Link learned "can't go there" at the water's edge. Then the lever ran a bridge over that exact water, and the old walls blocked it. Now learned walls expire after 400 steps, and are dropped when the room changes.
4. **Doors only one way.** Link left the dungeon and never knew that door led back. Now a door you came through is a known way back.
5. **Memory reset every visit.** I named objects by the engine's memory address, which changes every time a room loads. Now: kind + tile.
6. **Deep water reads as floor.** It shares its tile type with shallow water you can walk in. Now falling in teaches "water here".

## Watch it

<div class="clips">
  <figure>
    <video src="{{ '/assets/video/slingshot-to-barrel-room.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Seed 19, about 3x speed: the mushroom island, the slingshot, the barrel room. No reloads</figcaption>
  </figure>
</div>

## Numbers

<div class="scroll" markdown="1">

| continuous game from the entrance | best | lever pulled | past the lever room |
|---|---|---|---|
| this morning (3 seeds) | 8 flags | 0/3 | 0/3 |
| tonight (3 seeds) | 12 flags | 3/3 | 1/3 |
| later tonight (12 fresh seeds) | 12 flags | 12/12 | 8/12 |

</div>

The last row adds one more fix: when a mushroom pull flings Link the wrong way, he tries again from another side.

On the held-out puzzles (33 puzzles x 2 seeds): **30 solved** (26 this morning), but **11 game overs** (4 this morning). Almost all are in one room where Link starts with nearly no health. Not fixed yet.

## What I take from it

- **Test the way the game is played.** Every bug above only appears when Link arrives from somewhere. The continuous game is now my main test.
- **Fix the rule, not the room.** Doors, statues, water, memory: none of the fixes mention Deepwood.
- **Next:** the barrel room. Its middle is a giant barrel my map reads as a wall.
