---
layout: post
title: "The bugs were at the bottom"
---

**Result:** on 15 real *Minish Cap* puzzles the agent never trained on, it now solves **8**. Random play solves 3.

## The yardstick

"Reached a new room" turned out to mostly reward accidents, like falling down a hole. So we built a **puzzle bench** from saved states the agent never trained on: Link starts in a room with an unsolved puzzle, and success means that room's own puzzle flag gets set within 1,000 actions.

The rooms: the barrel room (statue onto a button), the mushroom lever room, and the Slug & Torches room.

## The new agent

What the research says: the AIs that finished long Nintendo games (Gemini in Pokémon) were a language model with good tools, not reinforcement learning. So:

- **Brain:** a local LLM (Qwen3-30B) reads the room as text, with real object names ("Pushable Statue", "Button") and what each try did, and picks the next experiment.
- **Hands:** a path planner plus the game's own action macros. No learning needed to walk somewhere.

## Where the time went

The brain was fine. The layers under it were broken:

1. **A lever looked like it did nothing.** Its flag is set only *while* it's held pulled, and cleared when it retracts, all inside one action. We only looked before and after. Now we watch every frame.
2. **Pulls never grabbed.** Link has to be lined up with the object pixel by pixel. An old "align" step had been lost. With it back, the mushroom slingshot works.
3. **Our actions were the wrong ones.** We'd rewritten tested macros into untested ones.
4. **The map learned fake walls.** "This move failed" got recorded during a text box, or while pushing a heavy statue. Now Link only learns a wall with control, toward empty floor, after two failures.
5. **The brain looped.** It once gave the same goal 700 times in a row. Now a loop breaker drops a goal after 4 repeats with no effect.

## Numbers

<div class="scroll" markdown="1">

| | solved | game over |
|---|---|---|
| LLM brain + planner | **8 / 15** | 2 |
| simple brain + planner | 7 / 15 | 3 |
| random play | 3 / 15 | 3 |

</div>

## Watch it

Real game, unedited runs from the bench, about 4x speed. The caption shows the brain's current goal.

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

These are puzzles from the bench I debugged on, so they show what works, not how often.

## The honest check

Every fix above came from tracing those same 15 puzzles, so I built a **held-out test bench**: 33 other saved states in 5 rooms, two of them never looked at.

<div class="scroll" markdown="1">

| on the held-out test | solved | game over |
|---|---|---|
| LLM brain + planner | **13 / 33** | 11 |
| simple brain + planner | 5 / 33 | 9 |
| random play | 6 / 33 | 16 |

</div>

- **The LLM brain generalises:** about twice random play, on puzzles I never debugged. Object names plus the model's world knowledge carry over to new situations.
- **The simple brain doesn't:** its hand-tuned habits were partly fitted to the dev set. Overfitting, one level up.
- **Since then,** from dev-only diagnosis: chests open only from the front, so the side became part of the experiment; stepping onto things comes first; stuck walks end early. With those, the simple brain went to 8 / 33 on the test.

## Step back

- **The lesson:** test the bottom first. Every clever decision is wasted if the button presses are wrong.
- **The LLM's edge:** small on the dev set (8 vs 7), large on the held-out test (13 vs 5). The test set is the one that counts.
- **Next:** find out why the gains don't transfer to the test starts, using only the dev set for diagnosis and the test set only for scoring.
