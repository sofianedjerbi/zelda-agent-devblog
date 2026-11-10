---
layout: post
title: "Log 007"
subtitle: "It was standing on GAME OVER"
permalink: /log/007/
---

**What I learned:** I spent six hours fixing what I thought were navigation problems in Minish Woods, one by one, by hand. Then I built a tool that finds where runs get stuck. The biggest "navigation" problem turned out to be the **GAME OVER screen**: Link died and my agent never noticed.

## How I got stuck

After [Log 006]({{ '/log/006/' | relative_url }}), I traced each failure by hand and fixed what I found: lily pads, house archways, the reflex, animated tiles, the pond's current, water, portals, thorns. Ten fixes in a row. The navigation score went **down**, from 11 to 8 trips out of 28, and no trip ever reached the shrine.

That's the trap: I was the one learning each mechanic, one rule at a time. It doesn't scale, and it drifts toward memorising one forest.

New rule for myself: **three fixes without a measured gain means I change the method, not the next patch.**

## The tool: a sink report

Every run is cut into 200-action windows. A window where nothing new happens (no new tile, no new room, no new line of text, no flag) is a **sink**. For each sink, the run saves the game state and what was going on: who had the controls, which goals failed, where Link stood. A summary ranks sinks across all runs.

The first report, over every navigation trip:

<div class="scroll" markdown="1">

| sink | actions lost |
|---|---|
| Minish Woods, "standing still" | 52,800 |
| Minish Village, standing still | 24,600 |
| everything else | under 12,000 |

</div>

## What "standing still" was

I loaded one of the saved woods sinks. This is what it showed:

**GAME OVER. Save / Don't Save.**

When Link dies, the game refills his health in memory *before* the GAME OVER screen. My death check looked for zero health, so it never fired, and the agent kept pressing buttons on a menu for thousands of actions. **297 of 502 sink windows were this screen**, and 293 of 298 in the woods. Most of the "woods navigation" I had been fixing was Link dying and never coming back.

The same check found two more silent bugs:

- **Link's size was never read.** The game reports Minish form under a different name than I used, so every size check I added that day thought Link was normal-sized.
- **The item list I gave the language model was always empty.**

## Water

You noticed the agent jumping into water over and over. Three causes:

1. **It forgot.** A fall marks that tile as water, but outdoor water is animated, and any tile change wiped what was learned.
2. **It didn't notice.** The game puts Link back exactly one tile, 16 px, with no damage. I only looked for jumps of 40 px or more.
3. **No cost.** Now a goal where Link falls counts as failed, and the tile is avoided.

Falls in 8,000-action dungeon games: **187-297 down to 68-98**.

## The story layer

The local language model can now use what it knows about the game's story. It gets the title, where Link is, his form and items, what people said, and which options he can actually reach. It writes the next objective and picks an option:

> *"Reach the shrine… by using the Minish form to access the small door."*

So far it doesn't measurably help: **10 of 28 trips with it, 11 without.**

## Where it stands

<div class="scroll" markdown="1">

| navigation (28 dev trips, 4 seeds) | reached |
|---|---|
| this morning | 11 |
| after ten hand fixes | 8 |
| with the GAME OVER fix | **11** |
| + story layer | 10 |

</div>

No trip reaches the shrine yet. The top sink now is real: Link waiting on the pond's ledge for a lily pad that never lines up.

## What I take from it

- **Measure where it stops before deciding what to fix.** One saved screenshot beat six hours of tracing.
- **Check the bottom layer first, always.** Death, size and items were all read wrong, silently.
- **Next:** the dungeon numbers need re-measuring (the GAME OVER bug affected them too), then the village sink.
