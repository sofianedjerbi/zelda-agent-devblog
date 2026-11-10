---
layout: post
title: "Log 004"
subtitle: "One continuous game"
permalink: /log/004/
---

**Result:** from the Deepwood entrance, one game with no reloads now gets **12 dungeon flags** and reaches the barrel room. This morning it stopped at 8 and stood still for 15,000 actions.

## The test

Benches start Link inside one room. A real player walks in from the door and keeps going. So: one continuous game from the dungeon entrance, 20,000–30,000 actions, game overs allowed (continue screen), several seeds.

## Why it stood still

Not the puzzles. The brain could solve the lever room on the bench 7 times out of 8. In the continuous game it never even tried. Every cause was in the lowest layers:

1. **It decided on the door tile.** Coming through a door, the game walks Link in by itself. Seen from the door tile, the room is all walls: 1 goal of 82 reachable. So it walked straight back out. Now Link waits until he has stopped moving and is off the door. Lever from the real arrival: 0/6 → 6/6.
2. **Statues reset.** Three statues block the hall's top door every time Link comes back. The map treated them as walls, so the room looked like a 4-tile box. 213 goals spent in that box.
3. **Walls that stopped being walls.** Link learned "can't go there" at the water's edge. Then the lever ran a bridge out over that exact water, and the old walls blocked it. Now learned walls expire after 400 steps, and are dropped when the room changes.
4. **Doors only one way.** Link left the dungeon by a door and never knew it led back. Now a door you came through is a known way back.
5. **Memory reset every visit.** Objects were named by the engine's memory address, which changes each time a room loads. Now: kind + tile.
6. **Deep water reads as floor.** It shares its tile type with shallow water you can walk in. Now falling in teaches "water here".

## Watch it

One continuous game, about 3x speed: across the mushroom island, the slingshot, into the barrel room.

<div class="clips">
  <figure>
    <video src="{{ '/assets/video/slingshot-to-barrel-room.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Seed 19: lever room to barrel room, no reloads</figcaption>
  </figure>
</div>

## Numbers

| Continuous game from the entrance | Best | Lever pulled | Past the lever room |
|---|---|---|---|
| This morning (3 seeds) | 8 flags | 0/3 | 0/3 |
| Tonight (3 seeds) | 12 flags | 3/3 | 1/3 |

Held-out puzzle test (33 puzzles × 2 seeds): **30 solved** (this morning 26), but **11 game overs** (was 4). Almost all are in one room where Link starts with nearly no health. Not fixed yet.

## Step back

- **Benches hid this.** Every bug above only shows up when Link arrives from somewhere. The continuous game is now the main test.
- **Each fix was general:** doors, statues, water, memory. No Deepwood-specific rule.
- **Next:** the slingshot works on 1 seed of 3, often only on the 2nd pull. Then the barrel room in a continuous game, and the game overs.
