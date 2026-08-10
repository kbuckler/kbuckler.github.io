---
layout: post
title: "Five days in"
date: 2026-08-09 09:00:00 -0700
---

A coworker sent me Steve Yegge's [The Shape of Things to Come](https://yegge.ai/essays/the-shape-of-things-to-come/) a few days ago, the one where his agent fleet is blasting a hundred commits at main and CI as we know it stops making sense. The premise horrified me a little. I also couldn't argue with the part underneath it: CI, quality control, and good design are the bottleneck, and they were the bottleneck on large teams long before AI compressed the development loop. I read it twice. Then I did the thing you're probably not supposed to do with essays like that, which is take it literally.

Taking it literally turned into this project. Orrery is a single-player 4X space opera I've wanted to exist for years: a real simulated economy, thousands of agents buying and hauling and scheming, and you climb from one freighter toward something like a megacorp. Space is a graph, nodes and edges instead of a big continuous void. Real time, pausable, management-heavy on purpose. Unity 6 on the presentation side, plain C# simulation underneath that runs headless on any machine with the dotnet SDK. That's the surface goal, anyway. This project is two goals stacked on top of each other.

The other goal is the one the essay planted. I wanted a thunderdome. (Cue the "that's bait" meme. Spicy premise. I took the bait anyway.) I wanted to see how far I could push CI before it breaks, and when it breaks, what an autonomous system can actually do to fix itself. So Claude agents claim issues, implement them, review each other's pull requests, and merge their own work when the gates are green. My job is direction: answer design questions, bless or veto decisions, personally gate anything touching CI, spending, or the constitution documents. Most days that amounts to managing a very fast, very literal engineering team that works while I sleep.

Five days in, the repo holds 215 authored commits (the raw log says 385, but the rest is merge plumbing), roughly 73,000 lines of C#, about 2,700 tests, and the issue counter passed 340 this morning. I wrote almost none of it.

Also, a note for anyone reading from outside: the repo's private for now, so the issue and PR numbers scattered through these posts are internal bookkeeping. Treat them like timestamps.

## Wednesday: foundations and a constitution

Day one was scaffolding. The deterministic sim core went in first: seeded RNG streams, a fixed tick loop, an event bus everything else reads from, and golden-master hash tests so any change to simulation behavior announces itself. Determinism went into the constitution as a hard rule that morning. It's already earned its keep several times.

The process took shape the same day. GitHub Issues as the tracker, a label state machine, a dispatcher that wakes agents, and an agent reviewer that reads every PR. The very first content pipeline PR went eight review rounds before it merged. Eight. Two agents arguing politely about UTF-8 strictness and exponent overflow while I watched from the sidelines. We later added a severity contract that caps review at two rounds, which tells you something about how that first day went.

## Thursday and Friday: an economy that disagrees with itself

Phase 1 was production and markets. A hand-authored starter map, a wares tree, stations with inventories, recipes, and local price curves. No ships yet, so stations could only trade with neighbors sharing their node. The exit demo landed Friday: a full simulated game-year runs headless, and the run fails loudly unless prices for recipe inputs spread at least 1.5x across stations that actually traded them. Watching the first price charts fan out, oversupplied stations sinking while starved ones climbed... that was the moment this started feeling like a place.

The conservation ledger balances too. Every credit and every unit of ware accounted for across the whole economy. Sounds fussy until you watch it catch a bug.

## The weekend: ships, and the thunderdome delivering

Phase 2 is underway. Ships as agents, with docking, cargo, and trade AI that reasons about profit per hour after travel costs. Each NPC ship gets a generated captain whose skills nudge how well it trades, because people exist in this simulation before they matter. The first art spikes landed too: a Blender script bakes a station model down to a lit sprite that Unity can draw by the hundreds.

And the thunderdome part is delivering. This morning the whole agentic loop went down, every automerge and dispatch trigger failing at once, and most of today has gone into the machinery instead of the game. Which is the experiment working. The point was never to keep CI from breaking. The point is to find where it breaks and watch what the system does about it. A dozen issues now track packaging the loop for reuse, so the factory that's building Orrery could someday build something else.

## What I'm curious about

The decision log in DESIGN.md, the design doc the agents treat as constitution, has 56 numbered rows, plus a table of provisional calls the agents made on their own and left waiting for my veto. Reading it feels like reading the minutes of a company I own but don't attend. The next big milestone is The Observatory: a living galaxy you can watch, with no player verbs at all. If the economy is worth watching before it's a game, the game part gets much easier. That's the bet anyway.

More soon.
