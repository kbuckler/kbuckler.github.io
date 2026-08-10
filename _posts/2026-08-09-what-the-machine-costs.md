---
layout: post
title: "What the machine costs"
date: 2026-08-09 17:00:00 -0700
---

Third post today, so apparently this is what Sundays are now. This one's about money, because the first real bill for running Orrery's agent fleet showed up this weekend and it came from a direction I didn't expect.

## The envelope, as designed

The plan on paper: a Claude Max subscription at 200 dollars a month, at most 25 dollars a month of API spend for the PR reviewer, GitHub's free Actions minutes, and eventually the electricity for a spare PC. The subscription does the heavy lifting. Implementer and groomer sessions run on it, and so do the interactive sessions where I steer.

## Where the money actually went: GitHub Actions

Five days of the loop produced about 2,900 workflow runs. The billing API needs a scope my CLI token doesn't have, so I reconstructed billable minutes from job timings instead: roughly 4,100 billable minutes, call it 830 a day. The included minutes ran out this weekend. I put a 50 dollar budget on the account, and at 0.8 cents a minute that lasts about a week.

The composition is the fun part. Actual CI, which builds the sim, runs 2,700 tests, and soaks two game-years of economy on every push, averages three minutes a run: about 1,400 minutes across 478 runs. The coordination layer beat it. Dispatch fired 1,344 times and the automerge sweep 767 times, each run a few seconds of real compute, and GitHub bills every job rounded up to a full minute. Those seconds-long runs billed roughly 2,100 minutes. More than half the total. The metronome costs more than the orchestra.

The whole five-day compute bill, if every minute had been paid, is about 33 dollars. That's 27 cents per merged PR, of which there are 124. The trajectory is the problem rather than the total: at this burn rate the loop wants something like 200 dollars a month of Actions minutes, and Phase 2 will push that up.

## My spare PC finally earns its keep

Issue #312 has been waiting in the backlog: activate a spare PC as a self-hosted WSL2 runner once the nightly soak matrix outgrows free minutes. Funny thing is, the matrix turned out to be innocent. Four runs to its name. The event plumbing did it instead. Self-hosted runners bill zero Actions minutes, so moving the busy workflows there turns this line item back into electricity, which was the plan all along, just a phase earlier than expected. One caveat the week already taught us: runners are only the execution plane. Triggers, queueing, and scheduling stay in GitHub's cloud regardless, so a control-plane incident still stops the loop no matter whose hardware runs the jobs. The rounding math also suggests a second lever worth pulling either way: batch the tiny coordination runs so a billed minute buys a minute of actual work.

## The Claude side

Three buckets. The reviewer runs on an API key, pinned to Sonnet after day one produced a 32 dollar Opus surprise, with superseded runs cancelled so a stale review never bills. Its spend lives in the Anthropic console, which this machine can't see. The cloud sessions, the hourly groomer and the event-fired implementers, run on the subscription and are equally invisible from here. The local sessions I can measure exactly, because Claude Code keeps transcripts with per-call token counts.

Five days of local sessions on this Mac: about 2,200 model calls and 2.7 million output tokens. The number that actually explains the economics is the cache: 738 million tokens of context read through the prompt cache, which costs a tenth of normal input price when it's API traffic. Priced at API list rates, the local usage alone comes to roughly 900 dollars for the five days, close to three quarters of it those cache reads. Run that forward and the 200 dollar plan is absorbing what would be north of 5,000 dollars a month at list prices. And that's measured from this Mac alone, before counting the cloud fleet.

The flat rate changes behavior: the marginal agent-hour feels free, so nobody rations. The rate is flat but the pipe isn't bottomless. Max has a rolling five-hour usage ceiling, and between the fleet and my own sessions we've brushed up against it a few times this week without crossing. The loop is currently sized to just about fit inside what the plan buys. Feels less like luck and more like the way these things always go: usage expands to fill the allowance, then leans on the fence.

## The shape of it

Adding it up, the machine currently runs on about 200 dollars of subscription, an envelope of 25 for the reviewer, and an Actions trajectory of about 200 a month until the runner lands. Call it 400 and change for a system that's merged 124 PRs in five days, roughly 25 a day. I've spent more on a single conference ticket.

What surprises me is what the money turns out to be for. Intelligence is the cheap part. Caching makes re-reading the whole project nearly free, the subscription delivers well over twenty times its price in list-rate tokens, and 27 cents of compute per merged PR rounds to nothing. The bills come from plumbing: full-minute billing on second-long coordination jobs, the dispatcher firing on every label, the sweep waking on every event. The cost structure of an autonomous system, at least this one, is mostly the cost of keeping the machinery listening.

## The job changes too

One more thing this week made obvious: engineers are about to own budgets, and mostly we never have. Unless you worked on cloud infra and babysat an AWS bill, spend was somebody else's spreadsheet and your job was to implement. Run an agent fleet and that split is gone. Implementation is the cheap part now, so the human can't just be an implementor. The job moves to the constraints: what's worth dispatching, where the rate-limit headroom goes, whether a dollar of Actions minutes buys more progress than a dollar of tokens, when to stop polishing a pipeline that's already good enough. Maximize ROI, in other words. Ugh. But five days of watching this thing run and that's clearly the seat I'm sitting in. Not the fastest implementor on the team, nowhere close anymore. The one who has to understand the constraints deeply enough to point the machine at the right problems.

Missing from this post: exact figures for the cloud sessions and the reviewer key, which live on Anthropic's side of the fence. I'll pull the console export and fold the numbers in when I have them.

More soon.
