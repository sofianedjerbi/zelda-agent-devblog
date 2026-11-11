---
layout: post
title: "Log 008"
subtitle: "Thirteen rules, measured one at a time"
permalink: /log/008/
---

**What I learned:** in one day I added 13 hand rules for navigation, each tested on 4 to 6 games. Together they made the agent worse **everywhere**, dungeon included. My tests were too small to see it, so I kept adding rules on top. Then I spent hours hunting a "culprit" that was mostly noise.

## How bad it got

<div class="scroll" markdown="1">

| | this morning | end of the day |
|---|---|---|
| reach the barrel room (24 dungeon games) | 22 | 12 |
| above the barrel | 13 | 8 |
| navigation trips (28) | 11 | 11 |

</div>

I bisected it to one change (resetting memory when Link changes size) from 2 wins out of 6 vs 4 out of 6. Then I checked: in the dungeon, Link's size never changes. **2/6 vs 4/6 is a coin toss.** I had found nothing.

## The same game twice is not the same game

I ran the exact same code on the same 8 starting seeds twice: **4/8, then 6/8**. Part of it was Python's hash randomization, which changes the order the agent walks through its options. I pinned that, and runs are now nearly repeatable. But the spread between seeds is real, so any test needs many games.

## What I changed

1. **Version control.** One commit per change, with its measured result. Bisecting through accidental folder copies is over.
2. **A 6-minute test.** Saved mid-game moments, each with a goal the agent is never told: lever room to barrel room, out of the barrel, village to woods, field to town, two woods-to-shrine trips. 8 to 12 games each. It also counts how many places Link reaches, so I see progress even when the goal is missed.
3. **All 13 rules off by default.** With all of them on: lever to barrel room **5/12**, village to woods **2/12**. With all off: **10/12** and **11/12**.
4. **Each rule comes back only if it wins alone.** Six tasks, 8 games each, one rule turned back on at a time:

<div class="scroll" markdown="1">

| rule turned back on | goals reached /48 |
|---|---|
| none (the new default) | 27 |
| **calm reflex** (stop fighting what never hurts) | **27**, and 10x more of the woods explored |
| talk to people, count it as progress | 27 |
| look again after 200 choices | 26 |
| 6 others | 24-25 |
| skip look-alike objects | 23 |
| treat objects that move you as doors | 22 |
| size-aware water | 20 |
| use portals and stairs | 19 |

</div>

Only the calm reflex earned its way back. Some of the others help in one place (portals open up the woods) and break another (village to woods drops to 0/8, because Link keeps going into the dozen tiny houses).

## The dungeon came back

With the new default (rules off, calm reflex on), the 24-game dungeon test again:

<div class="scroll" markdown="1">

| 24 dungeon games | this morning | last night | now |
|---|---|---|---|
| reach the barrel room | 22 | 12 | **23** |
| above the barrel | 13 | 8 | **14** |

</div>

The best numbers so far, from *removing* rules.

## One real bug, found the right way

The biggest sink in the woods: 77,000 actions of Link stepping UP, DOWN, UP, DOWN under a tree. A nut hangs there, and the game lists it as something that moves. My planner's reflex says "next to something that moves: turn to face it, then swing". But turning meant *pressing the direction*, so Link stepped onto the nut, and the route stepped him back.

Now he turns in place, and stops swinging at something still there after 6 hits. Success didn't change, but woods coverage doubled.

## What I take from it

- **Small tests lie.** 8 games minimum, one change at a time.
- **Rules interact.** Thirteen reasonable ideas together were worse than none.
- **Navigation isn't the problem I thought.** Every one-step trip works (4/4). Every failure needs Link to shrink. That's the next layer.
