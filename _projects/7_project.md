---
layout: page
title: Flotilla
description: A Raft consensus engine built from scratch in Go — ten ships at sea that must always agree on the truth, even while some of them are sinking.
img:
importance: 1
category: Side Projects
github: https://github.com/HopzAlot/flotilla
giscus_comments: false
---


Picture a **flotilla** of ten ships out at sea, each one out of sight of the shore, none of them able to fully trust its own radio. They still have a job to do: agree, permanently and without contradiction, on what goes in the ship's log — even if the Captain's ship sinks mid-sentence, even if a storm cuts three ships off from the rest for a few minutes, even if two officers try to declare themselves Captain in the same breath.

That agreement problem is called **consensus**, and the algorithm that solves it is called **Raft**. **Flotilla** is my own implementation of it, written from scratch in Go — no `hashicorp/raft`, no consensus library doing the hard part for me. This was my first-ever Go project, and before writing a single line of it I went and learned Go concurrency on purpose — goroutines, channels, mutexes, race conditions, deadlocks — because I already knew a project like this would punish anyone who didn't respect them.

---

## System Overview

- **Backend:** 10 independent `flotillanode` OS processes in Go, each running the identical Raft state machine, talking to each other over plain HTTP+JSON RPC
- **Frontend:** A live React dashboard visualizing elections, replication, and failures in real time
- **Core algorithm:** Leader election, log replication, log compaction via snapshots, and ReadIndex-based linearizable reads
- **Persistence:** BoltDB-backed durable storage for term, vote, log, and snapshot state
- **Chaos testing:** Scripts that kill the leader mid-write and verify nothing committed is ever lost

---

## The Story: How a Fleet Agrees on Anything

### Electing a Captain

At any moment, only one ship is allowed to be Captain — the one giving orders. Every ship listens for a heartbeat radio signal from its Captain. As long as that heartbeat keeps arriving, everyone's happy and no one does anything drastic.

But say the Captain's ship sinks, or just goes quiet. Every other ship is running its own private countdown timer, and — because these timers are randomized — one ship's clock runs out first. That ship panics slightly, declares "I think the Captain's gone, vote for me," and radios the rest of the fleet. If more than half the fleet votes yes, that ship is the new Captain, for a new *term*.

Here's the detail that makes it actually work: those timers are deliberately randomized per ship. If they weren't, two ships would time out at the exact same instant, both call for a vote simultaneously, split the fleet's votes down the middle, and get... nothing. No Captain. Repeat forever. Randomization means a split vote can still happen occasionally, but it's a temporary bad roll of the dice, not a structural deadlock — the term just resets and someone's timer will win the race next time.

And a sunk minority can never crown itself Captain, no matter how many times it tries. Sink two out of three ships in a cluster, and the lone survivor will burn through election term after election term forever, never once winning — because it can never *prove* a majority is backing it. That's not a bug. That's Raft correctly choosing consistency over availability: better to refuse to answer than to risk two Captains giving contradictory orders at once.

### Writing in the Manifest

Say a client radios the Captain: "log this cargo — key `x`, value `5`." The Captain doesn't just scribble it in her own manifest and declare victory. She writes it down as *unconfirmed*, then radios the exact same entry to every other ship in the fleet.

Only once **more than half the fleet** has written that same entry into their own manifest does it become *official* — what Raft calls **committed**. And this is the part that actually took me a while to internalize: committed and *applied* are not the same moment. Being "official" just means the entry can never be erased or overwritten again — it doesn't mean every ship has already stacked that cargo into its hold. That stacking-into-the-hold step (applying it to the actual key-value store) happens slightly later, once each ship learns — via the *next* heartbeat's `LeaderCommit` field — exactly how far the fleet's official log now extends. A cluster that goes quiet for a while can genuinely have durable, un-erasable data sitting there un-applied, just waiting for the next heartbeat to tell everyone it's safe to act on it.

The Captain only tells the original client "success" *after* she's confirmed the majority write — never before. If she told the client "success" right after her own local write and then immediately sank, that entry could vanish along with her, and the client would have been lied to. Proving the majority first is what makes that lie impossible.

### "Are You Still Really the Captain?"

Now say someone radios the Captain: "how much treasure do we have in the hold right now?" This is a *read*, not a write — it never touches the manifest at all. You'd think it's the easy case. It isn't.

Here's the trap: a ship can be fully alive, fully convinced it's still Captain, and *completely wrong* about that — because a storm silently cut it off from the rest of the fleet a few minutes ago, and it simply hasn't found out yet. If that confused ex-Captain answered from its own memory, it could hand out stale, wrong answers all day long while genuinely believing they're correct.

So before answering any read, the Captain does something almost paranoid: she pings the *entire* fleet — "do you still recognize me as Captain, this term, right now?" Only once a real majority answers yes does she trust her own manifest enough to actually answer the question. Only after that does she double check her own hold has finished stacking everything that's officially been declared cargo up to that point. Two separate proofs, both required, before a single "here's your treasure" answer goes out. This is the **ReadIndex** protocol, and it's what makes Flotilla's reads *linearizable* — provably as fresh as the most recently committed write, not just "probably fine."

### Trimming the Manifest

A ship that's been at sea for a long voyage ends up with an enormous manifest — years of entries, most of them long since superseded. Every so often, a ship consolidates its whole manifest into a single summary page (a **snapshot**) and throws away the old line-by-line entries it summarizes. And if a ship that's been gone a *long* time finally rejoins the fleet, nobody makes it replay the entire ancient log entry by entry to catch up — it's just handed the current summary page directly (`InstallSnapshot`) and picks up from there.

---

## Key Features

- **Leader election** with randomized timeouts, term numbers, and majority-vote quorum — real failover, not a scripted demo
- **Log replication** with the majority-commit rule, so a client is never told "success" on an entry that could still vanish
- **Log compaction via snapshots**, including catching up far-behind peers via `InstallSnapshot`
- **Linearizable reads (ReadIndex)** — a genuinely hard, often-skipped piece of "vanilla" Raft tutorials
- **10 real OS processes**, not an in-memory simulation, talking over actual HTTP RPC
- **Chaos testing scripts** that kill the leader mid-write to verify committed data is never lost
- **A live React dashboard** to watch elections, replication, and recovery happen in real time

---

## Why It Matters

Most "Raft from a tutorial" projects stop at leader election and basic replication, because that's the part with the clearest textbook diagram. The two pieces that actually separate a toy from something closer to production — log compaction and linearizable reads — are also the two pieces almost every tutorial quietly skips, because they're the ones that force you to confront the gap between "looks correct in a demo" and "is actually correct under partition."

Building Flotilla was my direct, hands-on introduction to distributed systems — not reading about consensus, but implementing the safety rules myself and watching, node by node, exactly which ones break the moment you cut a corner.

---

## Link to the Live Project

You can try the deployed project here:
**[Flotilla](https://flotilla-rosy.vercel.app/)**

## Link to the CodeBase

You can explore the source code here:
**[GitHub: Flotilla](https://github.com/HopzAlot/flotilla)**

**Small Note:** The live dashboard spins up an actual 10-ship fleet behind the scenes — real processes, real elections, real sunk ships — not a simulation. Give it a few seconds to get its sea legs when you first load it.