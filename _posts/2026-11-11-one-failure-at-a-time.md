---
layout: post
title: "Log 011"
subtitle: "One failure at a time"
permalink: /log/011/
---

**What I learned:** on the first Zelda, my agent now finds **43 screens** in the time random buttons find **14**. Last log it was 26. No new idea did it. Five bugs did, and I found each one by stopping the game at a single failure and looking at it.

## New rules didn't work

After Log 010, I tried six new rules to get past 26 screens:

- avoid screens where Link died 3 times
- swing at enemies 2 tiles away
- remember which faraway exits failed
- go to exits never tried before
- learn what each object does when touched
- keep walls Link walked through open

None helped. One, swinging the sword without stepping first, halved the game overs, and still didn't add a single screen.

The same game can end anywhere from 9 to 44 screens, so 16 games can't see a small gain. I stopped looking at the end number and counted the thing I was trying to fix, which happens hundreds of times per game: **how often a trip to a known screen arrives.**

<div class="scroll" markdown="1">

| trips to a screen... | arrived |
|---|---|
| 1 screen away | 79% |
| 2 screens away | **5%** |
| 3 or more | **5%** |

</div>

Link knew the world was bigger. He just couldn't get back across what he'd already seen.

## Looking at one failure

So I stopped the game at a failed trip and saved two pictures: the screen, and the agent's own map of it.

<div class="scroll" markdown="1">

| bug | what I saw | the fix |
|---|---|---|
| **Fake water** | sand on screen, water on the map | A hit knocks Link back, and a rule for Minish Cap's deep water read that as "fell in". Now it needs no health lost. |
| **Wrong links** | "the west exit leads east" | The map noted where Link was when a walk ended, a screen or a game over later. Now: the first new screen. |
| **Walks that didn't stop** | 2 screens crossed for one exit | After a scroll, "has the screen changed?" compared against the new screen. Now: against the screen the walk started on. |

</div>

Fixing the third one took trips of 3+ screens from 5% to 34% and screens from 25 to 40. Then **no game found the sword any more**.

## The bug that found the sword

In the cave, the game holds Link still while the old man talks, even though it says the player has control. The agent pressed UP, nothing moved, it learned "wall", and walked back out.

The old "walks that didn't stop" bug had been hiding this: the walk from outside carried on into the cave and kept pressing UP until the text ended. A bug was finding the sword for me.

The fix works for any game: **a move that does nothing doesn't count as a wall until Link has taken a step on that screen.** Games often hold you still when you arrive.

## The numbers

<div class="scroll" markdown="1">

| Zelda 1, 80k steps, 16 games | screens | sword | game overs |
|---|---|---|---|
| random buttons | 14.1 | 16 / 16 | 59 |
| Log 010 | 26.0 | 16 / 16 | 43 |
| **now** | **42.9** | **16 / 16** | **25** |

</div>

Minish Cap scores 31 of 48 on its quick test, its best so far; the village-to-woods trip now works 8 times out of 8.

## What I take from it

- **Count the mechanism, not just the result.** The end number swung too much to see anything. The trip counter showed exactly what was broken.
- **Look at one failure.** One screenshot next to the agent's map beat six new rules.
- **A fix can expose a bug that was doing useful work.** When the sword vanished, it wasn't the fix that was wrong.
- **Still weak:** exploration levels off at about 43 screens by mid-game, and only 1 game in 16 finds a second item. That's next.
