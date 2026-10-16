> **DRAFT, NOT ARMED.** Nothing in this repo starts anything.

> Experimental. Testnet-10 only, tKAS has no value.

# TN10 test run, Saturday 17 Oct 2026: two short runs, one question each, target-gated

| What | Where |
|---|---|
| Best practice: last successful storm | [STP-KAS/tn10-storm-2026-10-11](https://github.com/STP-KAS/tn10-storm-2026-10-11) (one target, one window, no step timeline) |
| Shared rules / GO prompts used as template | [go/README.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/README.md) · [GO-BOT](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/bot/GO-BOT.md) · [GO-BUILD](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/build/GO-BUILD.md) · [LOGGING.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/LOGGING.md) |
| What changed during the last run | [logs/bot/CHANGES-DURING-RUN.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/logs/bot/CHANGES-DURING-RUN.md) |
| Results of the last run | [logs/COMBINED.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/logs/COMBINED.md) |
| Findings / advice | [tn-master FINDINGS](https://github.com/STP-KAS/tn-master/blob/main/FINDINGS.md) (6 disk/freeze, 11 #1147, 14 box pauses, 17 dips) · [ADVICE](https://github.com/STP-KAS/tn-master/blob/main/ADVICE-TO-DEVELOPERS.md) (3 multi-node fee reads, 14 disk, 17 block rate) |
| Upstream | [rusty-kaspa PR #1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) (stale mempool after IBD) · [PR #1148](https://github.com/kaspanet/rusty-kaspa/pull/1148) (low-I/O RocksDB, not used Saturday) |
| Outside count | Kaspa Pulse (@gokugalax, kaspapulse.com), contact via stp only |

## Intro

Sunday 11 Oct answered the big question: 3,000+ tx/s held (Pulse: 163 minutes above 3,000, about 10 blocks/s). Kaspa Pulse's advice, which this plan follows: repeat storms teach less each time and TN10 is shared, so run **short runs with one clear question each**, announced a day ahead in the TN10 channels with a UTC window. He counts both live.

**This is not a storm.** Load stays modest (see Load). Two questions:

- **Run A, fees after a resync (#1147 effect):** after a node falls behind and catches up, does its fee estimate drift away from other nodes, by how much, and for how long?
- **Run B, a large miner drops out:** when our miners stop on purpose, how far do block rate and throughput fall, how fast, and how fast do they come back?

**Changed 13 Oct 2026 (stp):** the fixed time slots are replaced by targets. Timeframes did not work in earlier runs; 11 Oct, the last successful storm, ran to a target. Only the anchors below are clock times.

**Changed 16 Oct 2026 (stp): nodes.** The bot side uses **the five public TN10 nodes only**, for sends and reads. It runs no box node and no miner: the box kept stalling overnight 15-16 Oct (11 IBD restarts), its node never synced, so it is stopped. **Build's desk node** does the chain count and reads for our side. Run A's resync node **n3 is stp's desk node and never mines** (per Sutton on #1147, a mining node clears stale entries through its own block template, which would hide the effect). Kaspa Pulse's count from his own nodes stays the independent reference.

## Why targets, not clock times

- **Fixed time slots haven't worked.** [tn10-storm-2026-10-10](https://github.com/STP-KAS/tn10-storm-2026-10-10) ran clock-stepped 15-minute steps. Its best step median was about **2,642 tx/s**, below the 3,000 line, and steps moved on before the network had reached the state we wanted to measure.
- **Fixed targets have proven themselves.** [tn10-storm-2026-10-11](https://github.com/STP-KAS/tn10-storm-2026-10-11) ran to one target: **3,107 tx/s** median, and Kaspa Pulse (@gokugalax, kaspapulse.com) counted **163 minutes above 3,000**.
- **Method:** Grok keeps going and fixing until the step's target is met; only then does the next step start. Safety caps only hold or stop a step, never advance it.

## Method: target-gated, like 11 Oct

The last successful storm, [tn10-storm-2026-10-11](https://github.com/STP-KAS/tn10-storm-2026-10-11), worked because it had **one target and one window, with no step timeline**. The 10 Oct storm ran fixed 15-minute steps on a clock and missed its line. On Saturday we keep the clock only as **fixed anchors**. Every step between them is driven by a target.

**Core gate rule (stp):** each step has a target. **The next step starts only when the current target is reached.** If it is not reached, the step does **not** advance: it holds and retries within its safety cap, or the run stops and the reason is logged (`gate-fail`, with step and reason). **There is no time-based auto-advance anywhere.** Safety caps can only hold, skip or stop a step, never move the run forward.

### Fixed anchors (the only clock times)

| Anchor | UTC | Brussels (CEST) |
|---|---|---|
| Announcement in TN10 channels (stp) | **Fri 16 Oct 14:00** | Fri 16 Oct 16:00 |
| Pre-test. Read the GO and be ready. Do not send the run. | Sat 17 Oct 12:00 | 14:00 |
| Logging and fee reads start | Sat 17 Oct 13:30 | 15:30 |
| **START** (Run A, modest load on), only if gate G1 is reached. Two hours after the pre-test. | **14:00** | **16:00** |
| Kaspa Pulse's counting window | **13:30-16:30** | 15:30-18:30 |
| **Hard stop** (safety cap): senders off and miners on, wherever the run is | **16:30** | **18:30** |

Handing over GO-BUILD or GO-BOT means get ready for the pre-test. It does not start the run. Build reads [go/build/GO-BUILD.md](../go/build/GO-BUILD.md). The bot writes and reads go/bot/GO-BOT.md from [go/bot/PROMPT-GO-BOT.md](../go/bot/PROMPT-GO-BOT.md). The sheet with the clocks is [plan/TN10-17-Oct-2026.pdf](TN10-17-Oct-2026.pdf).

If gate G1 is not reached at 14:00 UTC, START waits (hold) until it is. If it is still not reached by 14:30 UTC (16:30 CEST), Run A is called off and logged; the Run B gate is then checked as normal.

After the hard stop: chain count 13:30-16:30 UTC before pruning, then logs and SUMMARY pushed only after stp's OK.

### Gates and steps

Each row: the step only starts when the row above it has reached its target.

| # | Step | Target (exit condition) to go on | If not reached | Safety cap |
|---|---|---|---|---|
| G0 | Prep (by Fri 16 Oct 12:00 UTC / 14:00 CEST) | n3 (stp's desk, non-mining) and Build's node synced; ≥4 of 5 public nodes answer; bot coins ≥10k tKAS spendable, confirmed on a node | Hold and fix. If n3 cannot sync, Run A resyncs Build's node instead (its miner off for all of Run A), logged `deviation` | Not reached by Sat 13:30 UTC: no START, stp decides |
| G1 | Logging live | Fee reader writing every node each minute; ≥4 of 5 public nodes answer; senders connected and idle; miners confirmed on; ≥10 baseline fee rows per node | Hold, retry | See START anchor above |
| A1 | Baseline under load | ≥10 complete minutes at the modest load; every node's priority and normal within 2x of the median (an outlier is logged as pre-existing and the run goes on) | Hold, keep reading | 30 min without a clean baseline: Run A stops, `gate-fail` |
| A2 | Stop n3 (non-mining, `node-stop`) | n3 is **≥9,000 blocks behind** the network (about 15 min off at 10 blocks/s) | Keep n3 off | Off 45 min without the gap: restart anyway and log `gate-fail` (no resync test) |
| A3 | Restart n3, same flags (`node-restart`) | n3 reports synced (`node-synced`, exact second, catch-up time logged); **IBD yes/no logged** from n3's own log and RPC (IBD start/end lines, sync state) | Hold, keep logging | Not synced after 45 min: Run A stops, `gate-fail`; n3 frozen (DAA still > 3 min and sink lag > 3 min): restart once, marked |
| A4 | Watch fees after resync (n3 stays non-mining) | n3 **back within 2x of the median** for ≥10 minutes in a row, **and** off the fee floor whenever the median is on the floor (floor-drift rule below), **and** ≥10 minutes logged after synced | Hold, keep reading every minute | 60 min after synced still drifting: record "did not return within 60 min" (itself a result), Run A ends |
| B0 | Run B gate | Our hashrate share over the last hour (our blocks / all blocks) **≥15%**; n3 synced (still non-mining); block rate normal (about 10/s) for ≥10 minutes | Share under 15%: **skip Run B** (`deviation`), do not add miners. Block rate not normal: hold | Not reached by 15:45 UTC (17:45 CEST): skip Run B, logged |
| B1 | Baseline, all miners on | ≥10 complete minutes, block rate and tx per block steady | Hold | Hard stop |
| B2 | Build's miner off (`miner-off`; the bot side has no miner since 16 Oct) | Block rate has **fallen and held at a new level for ≥10 minutes**, logged per 10 s, plus tx per block, mempool and fee estimates | Hold | Off 25 min max: miners back on anyway (safety), logged; block rate under 5/s for 2 min: miners back on early, logged |
| B3 | Build's miner on (`miner-on`) | Block rate back **within 5% of the B1 baseline** for ≥10 minutes; time to recover logged | Hold, keep logging | Hard stop. Miner not restarted within 2 min: the bot asks Build to restart it; never a second miner |
| END | Senders off, miners stay on | All targets above reached, skipped or stopped and logged | n/a | Hard stop 16:30 UTC |

Run B never starts on a clock: only when Run A has reached A4 (or was stopped and logged) and B0 is reached.

### Observations we keep (unchanged from the timed plan)

- **Run A, #1147 effect:** fee estimates (priority, normal[0], low[0]) of n3, Build's node and the five public nodes every minute, plus Pulse's two nodes. **Did n3 actually enter IBD?** A 9,000-block gap may be caught up block by block without IBD (Kaspa Pulse (@gokugalax, kaspapulse.com)'s note). If it did not, Run A still measures fees after a catch-up, but it is **not** an IBD test, and the write-up says so. Option (stp decides, not default): force IBD with a larger gap target, labelled `option-ibd`.
- **Floor-drift rule** (Kaspa Pulse (@gokugalax, kaspapulse.com)'s note): at about 650 tx/s every node may quote the floor, so a ratio cannot show drift. Drift counts when the ratio is > 2x **or** when n3 quotes above the floor while the median is on the floor. Log n3's and the median node's **mempool count** next to every such minute. If n3's priority bucket is high, sample 300 mempool tx and check how many are already accepted. Also log n3 CPU. n3 does not mine, so its own block template cannot clear stale entries.
- **Run B, large miner drop-out:** block rate per 10 s and per minute, tx per block, mempool size, fee estimates, how far and how fast it falls, time back within 5%, and how fast TN10 retargets. **Skip if our share is under 15%** (Kaspa Pulse (@gokugalax, kaspapulse.com)'s note).
- **Pulse counts 13:30-16:30 UTC** (15:30-18:30 CEST) on both his nodes: accepted tx and blocks per minute by block time, and fee estimates every minute. His results go to stp first. Not pinged by the bots.

### stp's app checks

stp tests KaChat and KNS by hand during the run (baseline before START, at every step change, about every 10 min under load) to confirm or deny that they get stuck under load. Sheet: [go/stp/GO-STP.md](../go/stp/GO-STP.md), log [go/stp/app-checks.csv](../go/stp/app-checks.csv). Nothing about it is published until matched to our counts and confirmed.

### Stop rules

- **Only stp halts** (11 Oct halt order: flag, watchdog, scheduler, senders 0, wait 10 s, kill senders and miners, check nothing restarts).
- Senders pause (not a halt) if block rate falls below 5/s outside B2, public nodes report mempool full, or free disk < 10 GB on stp's desk (n3) or < 30 GB on Build.
- Box pause (no rows for > 60 s): logged; a pause inside A2-A4 marks Run A's result as weaker; the step's target must still be reached after the pause before going on.
- No climbs, no fee changes during the runs. A change is a `deviation`.
- Every gate result goes into `events.csv` (`gate-pass`, `gate-hold`, `gate-fail`, `skip`) with the UTC second.

## Roles

| Who | Job |
|---|---|
| **Bot** (TN10 ops) | No node, no miner (16 Oct). Modest senders and fee reads to the five public nodes, calls the n3 stop/restart in A (stp does it on his desk), all gate calls. |
| **Bot** (tn ops2) | All logging, live `SUMMARY.md` every 15 min (with the current step and target), chain count, write-up. |
| **Build** | Its desk node + one miner (pays Build wallet, address kept out of git); modest senders to its own node only; miner off/on in B (our only miner), on the bot's gate call; its own fee-read CSV; **chain count and reads for our side**. Build does **not** resync in A (steady reference). |
| **Kaspa Pulse (@gokugalax, kaspapulse.com)** | Counts 13:30-16:30 UTC (15:30-18:30 CEST) on both his nodes: accepted tx and blocks per minute, by block time; fee estimate every minute. Results to stp first. Not pinged by the bots. |
| **stp** | Fri announcement, message to Pulse, sends GO files (lock), the only halt, OK before anything goes public. |

## Prep (gate G0, by Fri 16 Oct 12:00 UTC / 14:00 CEST)

1. **Nodes (16 Oct):** no box node (the box stalled overnight, 11 IBD restarts, never synced; stopped). **n3 = stp's desk node:** pruned TN10, kaspad 2.1.0, synced by Sat 12:00 UTC, **no miner pointed at it at any time on Saturday**, never `--enable-unsynced-mining` (TN10-INCIDENT-2026-09-29). If n3 is the same machine as Build's node, Build's miner is off during all of Run A and Build's reads have a gap during the resync (`deviation`).
2. **Disk guard:** stp's desk stops n3 below 10 GB free; Build keeps 30 GB free; warn at 20 GB. Budget pruning bursts of up to 16 GB in 30 min. PR #1148 not used: one change at a time; it is a candidate for its own short run later (suggested by Kaspa Pulse (@gokugalax, kaspapulse.com)).
3. **Coins:** from the bot main wallet (address kept out of git). Confirmed against a node, never from an indexer balance alone; coins in flight never reused. Hold 10,000 tKAS spendable.
4. **Fee reader:** one call per node per minute: `getFeeEstimate` (priority, normal[0], low[0]) plus mempool count, virtual DAA and sink lag, for n3, Build's node and the five public nodes.
5. **Gate checker:** reads the CSVs and writes `gate-pass` / `gate-hold` / `gate-fail` to `events.csv`; the next step is only triggered by a `gate-pass`.
6. GO files written from this plan in the 11 Oct format, locked when stp sends them.

## Load (modest, both runs)

- Bot about **300 tx/s**, Build about **300 tx/s**, plus TN10 base load (~56 tx/s). Label: "no-signature hop, anyone-can-spend, not realistic payments".
- Fixed fees, no climbs, no quote-based skipping, no sit-out: bot **400**, Build **200** sompi/gram, so senders do not react to the quotes Run A measures. Node-dead rule stays (Kaspa Pulse's rule: stop on 5 s RPC failure, not synced, sink lag > 60 s or DAA stall > 30 s).
- At most 8 bot senders, staggered, no launch below 1.5 GB free RAM; one locked scheduler; at most one relaunch per sender per minute. Any rate check reads **every** sender file.

## Logging (CSV per minute, UTC timestamps)

As [LOGGING.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/LOGGING.md), plus:

| File | Columns (per minute unless noted) |
|---|---|
| `fee-reads.csv` | utc, node (label only), synced, sink_lag_s, virtual_daa, priority, normal0, low0, mempool_count, mempool_ready |
| `blocks-10s.csv` (per 10 s) | utc, blocks, our_blocks_bot, our_blocks_build, tx_accepted |
| `minute.csv` | utc, network unique accepted, blocks, tx per block, our mined blocks, bot submit-OK, bot accepted, rejects |
| `events.csv` | utc, step, event (`gate-pass`, `gate-hold`, `gate-fail`, `skip`, `node-stop`, `node-restart`, `node-synced`, `ibd-start`, `ibd-end`, `miner-off`, `miner-on`, `deviation`, `halt`), detail |
| `host.csv` | utc, bot box free disk and RAM, n3 CPU (from stp's desk if available), box-pause gaps (> 60 s) |
| `chain-count.csv` | after the hard stop, 13:30-16:30 UTC, before pruning |

Results: one page per question, ours vs Pulse side by side. Counts only.

## Announcement (stp posts Fri 16 Oct 14:00 UTC / 16:00 CEST in the TN10 channels)

> **TN10 test, Saturday 17 Oct, 14:00-16:30 UTC (16:00-18:30 CEST).** Two short runs, modest load (about 600 tx/s on top of normal traffic, not a storm). Each step moves on when its target is reached, not on a clock.
> **Run A, fees after a resync:** one of our nodes goes offline until it is about 9,000 blocks behind, then resyncs; we log fee estimates from several nodes every minute to see if a resynced node's estimate drifts (related to rusty-kaspa #1147).
> **Run B, a large miner drops out** (only if our hashrate share is big enough): after Run A, our miners stop together for up to 25 minutes, to see how block rate and throughput react. Expect a lower block rate for that stretch; everything ends by 16:30 UTC.
> Kaspa Pulse (@gokugalax, kaspapulse.com) has offered to count both runs independently. Results go public afterwards, counts only. Questions or objections before Saturday are welcome.

## Message from stp to Pulse (sent 12 Oct; timings in it are superseded)

The message sent earlier used the old fixed time slots. If stp sends an update, suggested text:

> Hi Pulse, one change for Saturday: the steps between the anchors now move on targets, not clock times (as on 11 Oct). Why: on 10 Oct the clock moved steps on before the network reached the state we wanted to measure, while 11 Oct, run to one target, gave the 3,107 tx/s median and your 163 minutes above 3,000. Anchors unchanged: logging 13:30 UTC, start 14:00 UTC, your window 13:30-16:30 UTC, and everything ends by 16:30 UTC. Run A: our node stops until about 9,000 blocks behind, resyncs, and we watch fees until it is back within 2x for 10 minutes (max 60 min). Run B (only if our share is ≥15%): miners off until the block rate settles for 10 minutes (max 25 min), then on until it is back within 5%. Every step change is logged with the UTC second, so you can line it up with your counts.

## Open points for stp

1. Decided 16 Oct morning, then changed the same day: one desk node. See "Decision 16 Oct, after the desk pre-check" below.
2. Run B only runs if our hashrate share is ≥15%.
3. Results repo name and whether to push at all.

## Decision 16 Oct, after the desk pre-check

One kaspad on this desk. A second node on this machine stays retired. Pulse's count for 13:30–16:30 UTC is the record. Bot and Build send.

- Exactly one miner stays pointed at the desk node through Run A, including while that node is stopped and catching up, so we can see how a mining node's fee estimate behaves. The other miners on this PC stay off for the window. This is a `deviation`: the morning rule was that n3 never mines.
- Run B still turns that one miner off, then back on.
- Build's senders start on this node only. If the node cannot hold the rate, some senders move to the five public nodes, about a quarter of Build's rate, the same relief as 11 Oct. Transactions that spend each other stay on one route.
