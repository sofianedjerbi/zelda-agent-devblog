---
layout: post
title: "Log 002"
subtitle: "The bugs were at the bottom"
permalink: /log/002/
---

**What I learned:** when my agent failed a puzzle, the brain was almost never the problem. The layers under it were: how it reads the game, how it presses buttons. Once I fixed those, it solved **8 of 15** real puzzles it never trained on. Random play solves 3.

## A test that measures puzzles

My old test, "did it reach a new room?", mostly rewarded accidents like falling down a hole. So I built a **puzzle bench** from saved games: Link starts in a room with an unsolved puzzle, and success means that room's puzzle gets solved within 1,000 actions.

The rooms: the barrel room (statue onto a button), the mushroom lever room, and the Slug & Torches room.

## The agent

The AIs that finished long Nintendo games (Gemini in Pokémon) were a language model with good tools, not reinforcement learning. So I tried that:

- **Brain:** a local language model (Qwen3-30B) reads the room as text, with real object names ("Pushable Statue", "Button") and what each try did, and picks the next experiment.
- **Hands:** a path planner plus the game's own button sequences. Nothing to learn for walking somewhere.

## Five bugs, all at the bottom

1. **A lever looked like it did nothing.** Its flag is only on *while* the lever is held, and it switches off again inside one action. I only looked before and after. Now I watch every frame.
2. **Pulls never grabbed.** Link has to line up with an object pixel by pixel. An old "align" step had been lost. With it back, the mushroom slingshot works.
3. **My button presses were wrong.** I had rewritten tested button sequences into untested ones.
4. **The map learned fake walls.** "This move failed" got recorded during a text box, or while pushing a heavy statue. Now Link only learns a wall when he has control, toward empty floor, after two failures.
5. **The brain looped.** It once gave the same goal 700 times in a row. Now a goal is dropped after 4 repeats with no effect.

## Numbers

<div class="scroll" markdown="1">

| | solved | game over |
|---|---|---|
| language-model brain + planner | **8 / 15** | 2 |
| simple brain + planner | 7 / 15 | 3 |
| random play | 3 / 15 | 3 |

</div>

## Watch it

Real game, unedited runs from the bench, about 4x speed. These are puzzles I debugged on, so they show what works, not how often.

<div class="clips">
  <figure>
    <video src="{{ '/assets/video/mushroom-room.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Mushroom lever room: walks over, lines up, pulls the lever</figcaption>
  </figure>
  <figure>
    <video src="{{ '/assets/video/barrel-2.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Barrel room: button puzzle in 90 actions</figcaption>
  </figure>
  <figure>
    <video src="{{ '/assets/video/barrel-statue.mp4' | relative_url }}" controls muted loop playsinline preload="metadata"></video>
    <figcaption>Barrel room, another start: 25 actions</figcaption>
  </figure>
</div>

## The honest check

Every fix above came from staring at those same 15 puzzles. So I built a **held-out test**: 33 other saved games in 5 rooms, two of them never looked at.

<div class="scroll" markdown="1">

| held-out test | solved | game over |
|---|---|---|
| language-model brain + planner | **14 / 33** | 8 |
| simple brain + planner | 5 / 33 | 9 |
| random play | 6 / 33 | 16 |

</div>

- **The language model generalises:** about twice random play, on puzzles I never debugged. Object names plus what the model knows about games carry over.
- **My simple brain doesn't:** its hand-tuned habits were fitted to the 15 dev puzzles. That's memorising again, one level up. (Later fixes brought it to 8 / 33.)

## What I take from it

- **Test the bottom first.** A clever decision is wasted if the button presses are wrong.
- **The held-out test is the one that counts.** On dev puzzles the language model barely beat my simple brain (8 vs 7); on held-out puzzles it was far ahead (14 vs 5).
- **Next:** in the barrel room, 5 of 8 test starts need Link to leave, go around through other rooms and come back. My brain only shuttles between two rooms. It needs a real dungeon map.
