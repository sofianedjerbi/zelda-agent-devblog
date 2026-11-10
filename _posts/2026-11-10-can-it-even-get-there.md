---
layout: post
title: "Log 006"
subtitle: "Can it even get there?"
permalink: /log/006/
---

**What I learned:** I'd spent days inside one dungeon, but more than half of a Zelda game happens outside dungeons. When I tested the agent out there, it couldn't reach the dungeon at all. Inside a small, closed dungeon, "try every exit I haven't used" works. In an open world it never runs out, and the agent gets lost.

## The test

Four games from real saves, 20,000 actions each, no reloads:

<div class="scroll" markdown="1">

| start | what happened |
|---|---|
| Minish Woods, story done | went the wrong way, then in and out of the tiny Minish houses all game |
| Hyrule Field, story done | two screens of field, all game |
| Festival Town, mid-story | 15,700 actions in town |
| start of the game | house, field, town in 6,000 actions, then stuck in town |

</div>

None reached Deepwood. So I built a **navigation test**: a saved game plus a place to reach. The target is only for scoring; the agent is never told where to go.

## What the overworld broke

- **Look-alikes.** In the woods, Link spent his first 3,000-4,000 actions on 177 experiments with 8 identical lily pads. The overworld is full of copies: bushes, grass, rocks. Now, what I learned on two copies counts for the rest.
- **Doors that are objects.** In a Minish house, the way out is an archway you walk into. My brain saw "it did something" and kept going back to try it again: same house, 14 times. Now an object that takes Link to another room is a door on the map.
- **The reflex kept the controls.** The survival reflex takes over when something that moves is close. In the woods, a harmless nut that falls from the trees kept it in charge for **3,689 of 4,000 actions**. Now something that stays close for 30 steps without hurting Link is ignored.
- **Walking in place.** A goal ended when Link stopped moving. Stepping back and forth between two tiles never counted, so goals ran their full 150-600 actions. Now a goal ends when Link hasn't got closer in 20 actions.
- **Special floor.** The shrine is only open to Minish-sized Link. You shrink by standing on a portal stump and pressing the action button. My brain only experimented on objects, never on floor. Now it tries special floor too, and on its first try it shrank on the portal.

## Listening to the game

A player isn't searching blind: people tell you where to go. My agent ignored every line of text. Now it keeps everything it hears, and a local language model (Qwen3-30B, on this Mac) picks which unexplored exit to try next.

The first time it ran, a villager had just said *"the shrine to the north is filled with monsters"*. Out of 10 exits, the model picked **"Minish Village: door in the north"**.

## Two bugs in my own test

Both made the agent look better than it was:

1. **The area number flickers during screen fades.** "Reached the shrine in 834 actions" was a fade. A trip now counts only after 20 actions in the target area.
2. **Leftover checks from earlier trips.** My test reused worker processes, and each trip stacked its "arrived?" check on top of the last one's. "Reached the village after 0 actions" was an old check firing. Each trip now gets a fresh process, and every benchmark runs on a frozen copy of the code, because I had also been editing files during runs.

## Where it stands

On the clean test (6 trips, 2 seeds each), the agent reaches **4 to 6 of 12**, and that spread is noise. Short trips work: field to woods, village to woods, house to village. **No trip reaches the shrine yet.** The fixes above remove the worst sinks, but I can't show a gain on this test yet.

## What I take from it

- **Test where the game actually is.** One dungeon hid everything above.
- **Control comes first.** Any part that can take the controls for thousands of actions has to be found before tuning anything else.
- **Next:** more seeds per trip so the numbers mean something, crossing the woods pond on lily pads, and measuring whether the guide's advice helps.
