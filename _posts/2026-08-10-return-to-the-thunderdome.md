---
layout: post
title: "Return to the thunderdome"
date: 2026-08-10 09:00:00 -0700
---

Monday morning, back at the desk. Orrery, for anyone landing here cold, is a 4X space game that a fleet of Claude agents is building while I direct; [the first entry]({% post_url 2026-08-09-five-days-in %}) has the whole setup. Yesterday morning the agentic loop went down, I spent half of Sunday inside the machinery, and it came back Sunday afternoon (#301, closed). Then the fleet had its biggest day so far.

The machinery half-day, for the record: mostly workflow fixes, plus the start of real infrastructure. Proxmox and base images for the local runners, so standing up the next one is a clone instead of an afternoon. And I'm chewing on moving dispatch, the piece that fires an agent at each approved issue, out of Claude routines and into an always-on shepherding loop that runs locally. The event wiring is exactly where Sunday's outage lived.

## The scoreboard

Six days in: 517 commits, 186 merged PRs, and the issue counter blew past 480, up from 341 when I wrote yesterday's posts. 65 PRs merged in the last 24 hours. The daily merge counts tell the arc: 8 the first day, then 39, 38, a quiet 16 on Saturday, 40 on Sunday, and Monday's number is still climbing as I type. Sunday alone put 124 commits on main. The test suite was about 2,700 yesterday. It's 4,168 this morning.

I want to be suspicious of these numbers, velocity metrics being what they are. But the merges are real, the suite is green, and the game does more than it did Friday.

Also, most of my share of the weekend happened on my phone. Sitting on sand at the beach, cold drink in hand. Label an issue ready, skim a verdict, answer a design question, go back to watching the water. Low touch, low stress. The half-Sunday inside the machinery was the exception, and the phone was enough for the rest.

One more scoreboard number, less fun: it's Monday and half the week's Claude allowance is already spent. Sunday's record had a price. So some of this week goes to economics, and to learning to run the fleet inside the rate limits instead of against them.

## Not the spare PC we planned

The economics post said spare PC, WSL2. What actually landed when #312 closed is a NUC running an Ubuntu VM that hosts both self-hosted runners. Plans around here age in hours. The point was relief on the Actions meter, and that happened, but the runner turned out to be the key to a much better door:

## The agents can see now

Unity runs headless in CI now. A GameCI container spins up on the NUC, runs the playmode suite, and captures screenshots of the actual rendered game. Those screenshots land on every PR as a changed-vs-baseline sticky comment (#413), the baseline refreshes when the sweep merges (#458), and, the part that still feels like science fiction on a Monday: a visual inspection loop puts agent vision on those frames (#430), and the automerge sweep gates merges on the verdict (#435). A PR that renders wrong now fails to merge even when every unit test is green. The feedback loop closed on pixels.

This was the gap I expected to stay human the longest. Screenshot QA was supposed to be my Sunday job.

## Planets

The reason there are pixels worth judging: shader work landed all weekend. An analytic planet shader with a day-night terminator, rim scatter, and night lights on the dark side (#194). Atmosphere layers stacked on top: halo, sunset fresnel, clouds, dust, storms. A lit nebula behind everything, FBM noise with two layers of domain warp (#193). There's even a planet generation doc (#474), because the agents document what they ship. The gallery captures stopped looking like programmer art sometime Saturday.

## The sim kept moving too

Under all the visual noise, the economy work didn't pause. The trade AI split into a planner and a driver (#389), the runner CLI got split per verb (#390), and the belief-record shape for Phase 3 landed early (#244): PriceBelief and BeliefSource, groundwork for ships that can be wrong about prices instead of omniscient. 49 issues open this morning, groomed and queued.

The loop broke Sunday morning and set a velocity record by Sunday night. The Yegge response post keeps accumulating material.

More soon.
