---
layout: post
title: "The machine that builds the machine"
date: 2026-08-09 13:00:00 -0700
---

The first entry mentioned I write almost none of Orrery's code. This one's about what does. The setup deserves its own post, partly because it's taken as much design work as the game, and partly because it keeps teaching me things I didn't expect to learn from a hobby project.

## The cast

Five workflow files and two role documents run the show, and each role has a personality by now.

A groomer wakes hourly and tidies the backlog: researches open issues, writes specs up to kickoff quality with edge-case checklists and explicit non-goals, stages them under a `groomed` label. An implementer claims issues, one branch and one PR each, and it fires on events instead of a schedule: label an issue `ready` and a fresh session spins up within minutes. A reviewer reads every PR under a strict severity contract. Plain CI builds everything, runs the ~2,700 tests, and soaks the simulation for two game-years of ticks on every push. And an automerge sweep ties it all together: merges green work, heals merge conflicts, revives issues whose blockers closed, salvages abandoned drafts, fires the next dispatch when a merge lands.

Then me. My jobs are the ones that stay human on purpose: answering design questions, vetoing or blessing the provisional decisions agents make on their own, approving anything protected, and eventually judging whether any of this is fun.

Labels are the shared language. Issues move through `triage`, `groomed`, `ready`, `in-progress`, `blocked`, plus a handful of specialists like `design-question` and `repro`. The interesting one is `ready`: applying it is the act of approval, and the machine treats it exactly that way. I've learned to finish thinking before I touch that label, because a session will be claiming the issue about five minutes later.

## A day in the life of an issue

The groomer stages a spec. I skim it, maybe on my phone, and label it `ready`. A dispatch workflow fires an implementer, which claims the issue through a small mutex ritual (more on why below), opens a draft PR right away as a visible handle, and pushes work in increments with CI running on every push. When it flips the PR to ready for review, the reviewer wakes up. Verdicts come in three flavors: `CLEAN`, `ADVISORY-ONLY`, or `BLOCKING` with a count. Advisory findings become small follow-up issues so they never spin an extra round. Once the review is clean and checks are green, the sweep merges, closes the issue, and fires a dispatch for the next piece of ready work. On a good day the loop runs end to end with zero attention from me.

The sweep itself is event-driven, with deliberately no cron backstop. Instead it rewrites a liveness beacon, a pinned GitHub issue, on every pass, so a stale timestamp there is the alarm. That choice got tested this morning when the loop went down, and the beacon going quiet was exactly how the outage announced itself.

## Guardrails

A few fences hold the thing together. Protected classes wait for me at the merge gate: CI and workflow files, new dependencies, anything with cost impact, and the constitution documents themselves. The sweep also path-fences those files unconditionally, so a missing label is never the only thing between an agent PR and rewriting the process (or the sweep itself). Determinism rules are compile errors via banned-API analyzers, which keeps them out of review commentary entirely. A red main trips a circuit breaker: the queue pauses and the fix is revert-first within 24 hours.

And there's a budget envelope: the Claude subscription plus at most 25 dollars a month of API spend. That number exists because day one burned 32 dollars letting the reviewer default to Opus. The reviewer's pinned to Sonnet now, superseded runs get cancelled so a stale review never bills, and the API workspace carries a hard spend limit as a backstop.

## What we've learned so far

**Reviews need a scope contract, in writing.** PR #11 went eight rounds with essay-length findings before it merged. The fix was a severity contract: two severities, blocking or advisory, one-line verdicts, brevity limits, and a cap of two rounds before the disagreement escalates to me. Escalation is mechanical now: the sweep greps for it, applies a label, and that label is my queue.

**Give each actor jurisdiction over what it can actually see.** For a while the reviewer was expected to confirm CI status, and its token can't read the checks API at all. Rounds 2 through 10 of PR #299 got burned re-confirming check status the reviewer could never observe, a duty that was redundant the whole time because the sweep already refuses to merge red. The ruling that moved check status entirely to the merge gate landed this morning, as PR #341, while I was drafting this entry.

**Race conditions find you fast at machine speed.** Bulk-approving a phase once fired parallel implementers, and two claimed the same issue 55 seconds apart. Duplicate PRs, both green, both approved. The claim is now a three-layer atomic protocol with a deterministic branch name acting as a push mutex. The next phase wave ran with zero duplicates.

**Identity is architecture.** The system runs under four identities on purpose: the stock Actions token for silent bookkeeping, a GitHub App for the one kind of push that must trigger downstream workflows, the Claude grant for sessions, and me. GitHub changed the rules in June so bot-pushed PR heads queue their CI behind a human click, and untangling that taught us the house style: explicit orchestration over ambient event cascades. Every follow-on action fires deliberately, with reconciliation sweeps to catch missed deliveries.

**Prefer loud failures, and make records mean what they say.** Best bug of the week: a responder posted its dedupe marker before attempting the dispatch the marker recorded. A failure between those two steps would've silenced that PR permanently, and a broken PR is exactly the one least likely to get the new push that re-arms detection. The fix inverted the order so the marker records a dispatch that actually happened. The race that's left degrades to a duplicate, which is the cheaper failure.

**Standing process lives in documents.** Agents are enthusiastic protocol inventors. A clever procedure improvised in a PR comment thread starts looking like law within hours. The rule now: comments bind only their own thread, and process becomes real when it lands in the docs by PR, through the same gates as everything else.

## Where this is headed

The pipeline has quietly become a second product, which is more or less what the thunderdome experiment from the first entry was for. A dozen open issues track packaging the loop for reuse: extracting the sweep arms into tested scripts, splitting the process docs into a generic contract plus an Orrery overlay, externalizing the reviewer prompt. Open questions I'm chewing on: whether the roles deserve different model tiers, when the nightly soak matrix outgrows free minutes and the spare PC finally earns its keep as a runner, and how much of my remaining ten percent of the work is truly judgment versus habits I haven't written down clearly enough to hand over.

## Update, Sunday evening

That "when the spare PC earns its keep" question aged fast. As of tonight Proxmox is running locally, managing docker images for the runners, so spinning up the next one is a clone instead of an afternoon of setup. And the new hardware pulled more than CI weight. Unity now runs headless in the pipeline, playmode tests capture screenshots of the actual rendered game, those land on every PR as a changed-vs-baseline comment (#413), and an agent with vision judges the frames (#430) before the sweep will merge (#435). A PR that renders wrong now fails even when every unit test is green. The pipeline learned to see the game it's building.

More soon.
