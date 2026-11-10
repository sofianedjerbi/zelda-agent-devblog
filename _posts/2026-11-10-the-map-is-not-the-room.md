---
layout: post
title: "Log 005"
subtitle: "The map is not the room"
permalink: /log/005/
---

**Result:** out of 24 fresh games from the Deepwood entrance, **15 now climb out of the giant barrel** onto the ledge above the barrel room. None did this morning. Best game: **13 dungeon flags** (was 12).

## Watch it

One continuous game, about 2x speed: into the giant barrel, out through its window, onto the ledge, then the statue in the side wing.

<div class="clips">
  <figure>
    <video src="{{ '/assets/video/barrel-window-ledge.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Seed 33: barrel, window, ledge, statue. No reloads</figcaption>
  </figure>
</div>

## Inside the barrel, the map lies

The agent reads each room's layout from the game's memory. Inside the giant barrel, that layout is a plain rectangle of floor. The real walls and the window exits are drawn somewhere else.

Walking up through the top-left window drops Link on a ledge above the barrel room: two locked doors, mushroom enemies, and a side wing with two buttons and a statue. The brain had only ever found it by accident.

## What got fixed

All general: none of these mention barrels, statues or Deepwood.

1. **The door you came in by.** If an opening lies straight behind Link (up to 6 tiles), that's the door he just used. The barrel's window throws Link 5 tiles from the barrel's door, which looked like a fresh exit, so he walked straight back in.
2. **An exit is a band, not a tile.** The shrine's front exit is 5 tiles wide. Each tile counted as a new, unexplored exit, so Link went out and came back 5 times, dozens of trips per game.
3. **Push a block aside, but only where it can move.** Three statues plug the hall's top exit, and only the middle one moves. Walks now push a block when that's the only way through, and only when the tile behind it is free. The first version pushed statues into walls 50 times a game.
4. **Hands that stand still give up.** Next to a button, the side the hands wanted was cut off by a stone head the map calls floor. Result: 400 no-ops in a row. Now any reachable side will do, and a goal that hasn't moved Link in 8 actions ends.

## Tried and dropped

**Walking along the walls** to find exits the map doesn't show. In a big room it never runs out of wall to walk, and games stayed stuck in the hall: barrel room **1/12**. Reverted.

## Measuring got honest

The same code, run twice on 12 games, reached the barrel room 9 times, then 4. One run of 12 is noise. The yardstick is now **24 fresh games**.

<div class="scroll" markdown="1">

| continuous game from the entrance | lever room | barrel room | inside the barrel | above the barrel |
|---|---|---|---|---|
| Log 004 (12 games) | 12/12 | 8/12 | not measured | 0 |
| now (24 games) | **24/24** | **18/24** | **15/24** | **15/24** |

</div>

## The held-out test caught a regression

The held-out puzzles (33 starts I never debug on, 2 seeds) fell from 30 to 26 solved. All of the loss was in the barrel room: **4/16 to 0/16**. I added an off switch for each of today's fixes: none of them did it.

The culprit was older: the door into the barrel. It looked like an unexplored exit, so Link walked in before trying the room's statue and buttons. Doors guessed from the map now come after the room's own untried experiments: barrel room back to **2/16**.

## Step back

- **The lowest layer, again.** Every gain today came from the map and the hands, not the brain.
- **Wins on one test can hide losses on another.** The continuous game improved while the barrel-room puzzles broke. Both tests run on every change now.
- **Next:** in the games that fail, the mushroom slingshot never fires. And the mushroom enemies on the ledge still cause game overs.
