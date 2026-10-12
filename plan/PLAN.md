> **DRAFT, NOT ARMED.** Nothing in this repo starts anything.

> Experimental. Testnet-10 only, tKAS has no value.

# TN10 test run, Saturday 17 Oct 2026: two short runs, one question each

| What | Where |
|---|---|
| This plan | [plan/PLAN.md](PLAN.md) (DRAFT, NOT ARMED) |
| Best practice: last storm | [STP-KAS/tn10-storm-2026-10-11](https://github.com/STP-KAS/tn10-storm-2026-10-11) |
| Shared rules / GO prompts used as template | [go/README.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/README.md) · [GO-BOT](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/bot/GO-BOT.md) · [GO-BUILD](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/build/GO-BUILD.md) · [LOGGING.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/LOGGING.md) |
| What changed during the last run | [logs/bot/CHANGES-DURING-RUN.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/logs/bot/CHANGES-DURING-RUN.md) |
| Results of the last run | [logs/COMBINED.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/logs/COMBINED.md) |
| Findings / advice | [tn-master FINDINGS](https://github.com/STP-KAS/tn-master/blob/main/FINDINGS.md) (6 disk/freeze, 11 #1147, 14 box pauses, 17 dips) · [ADVICE](https://github.com/STP-KAS/tn-master/blob/main/ADVICE-TO-DEVELOPERS.md) (3 multi-node fee reads, 14 disk, 17 block rate) |
| Upstream | [rusty-kaspa PR #1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) (stale mempool after IBD; supertypo review 12 Oct) · [PR #1148](https://github.com/kaspanet/rusty-kaspa/pull/1148) (low-I/O RocksDB for pruned nodes) |
| Outside count | Kaspa Pulse (@gokugalax, kaspapulse.com), contact via stp only |
| Repo for results (after the run, if stp says so) | proposed `STP-KAS/tn10-run-2026-10-17` (not created) |

## Intro

Sunday 11 Oct answered the big question: 3,000+ tx/s held (Pulse: 163 minutes above 3,000, about 10 blocks/s). Kaspa Pulse's advice, which this plan follows: repeat storms teach less each time and TN10 is shared, so run **short runs with one clear question each**, about an hour, announced a day ahead in the TN10 channels with a UTC window. He counts both live.

**This is not a storm.** Load stays modest (see Load). Two questions:

- **Run A, fees after a resync (#1147 effect):** after a node falls behind and catches up, does its fee estimate drift away from other nodes, by how much, and for how long?
- **Run B, a large miner drops out:** when our miners stop on purpose, how far do block rate and throughput fall, how fast, and how fast do they come back?

## Briefing (one screen)

- One clock, UTC in git. Brussels = UTC+2 (CEST until 25 Oct).
- Bot (TN10 ops + tn ops2) runs the box node, its miner, the modest senders and all logging. Build runs its desk node and miner and is the "large miner" in Run B together with the bot.
- Pulse logs fee estimates from his two nodes every minute and counts accepted tx per minute (his method). We do the same on ours.
- stp sends the announcement on Fri 16 Oct 14:00 UTC, sends the GO files, and is the only one who halts.
- Every rule from 11 Oct holds unless changed here: node-dead rule, never submit to the other side's node, count right after END, leak-scan before push, no keys/addresses/IPs in git, no comments on kaspanet repos.
- **Keep going, but fix first** (11 Oct rule): a problem is fixed and logged (`fix`, `deviation`), and every change after the GO lock goes into `CHANGES-DURING-RUN.md` as on 11 Oct. The run stops only at END or on stp's halt.
- **Public repo: counts only.** No key, seed, txid, IP, host, port, tunnel URL, wallet address or local path. Leak-scan before every push. No force-push, no tags, no releases.

## Schedule (Saturday 17 Oct 2026)

| Step | UTC | Brussels (CEST) |
|---|---|---|
| Announcement in TN10 channels (stp) | **Fri 16 Oct 14:00** | Fri 16 Oct 16:00 |
| Plan with UTC times to Pulse (stp) | Fri 16 Oct, by 14:00 | Fri 16 Oct, by 16:00 |
| Gate G0: box node synced, Build node synced, coins, disk | Sat 17 Oct 12:00 | 14:00 |
| R1: logging on (all CSVs), baseline fee reads start | 13:30 | 15:30 |
| R2: senders connected and idle, miners confirmed on | 13:50 | 15:50 |
| **Run A START** (modest load on) | **14:00** | **16:00** |
| A: box node (n3) stopped (falls behind) | 14:10 | 16:10 |
| A: n3 restarted, catches up (resync) | 14:25 | 16:25 |
| A: watch fee estimates after resync | 14:25-15:00 | 16:25-17:00 |
| **Run A END** | **15:00** | **17:00** |
| Break (load stays at baseline, miners on, logging on) | 15:00-15:15 | 17:00-17:15 |
| **Run B START** | **15:15** | **17:15** |
| B: baseline, all miners on | 15:15-15:30 | 17:15-17:30 |
| B: **our miners off** (bot + Build, same second) | 15:30 | 17:30 |
| B: miners stay off | 15:30-15:50 | 17:30-17:50 |
| B: **miners back on** | 15:50 | 17:50 |
| B: recovery | 15:50-16:15 | 17:50-18:15 |
| **Run B END**, senders off | **16:15** | **18:15** |
| Logging stops (keeps running to here for the tail) | 16:30 | 18:30 |
| Chain count per minute 13:30-16:30 UTC, before pruning | 16:30-17:30 | 18:30-19:30 |
| Logs + SUMMARY pushed (only after stp's OK) | by 18:30 | by 20:30 |

Suggested window for Pulse's counter and fee log: **13:30-16:30 UTC** (15:30-18:30 CEST).

## Roles

| Who | Job |
|---|---|
| **Bot** (TN10 ops) | Box node n3 (new, see Prep), its one miner paying the bot wallet (address kept out of git), modest senders to the five public nodes, the n3 stop/restart in A, miner off/on in B. |
| **Bot** (tn ops2) | All logging, live `SUMMARY.md` every 15 min, chain count, write-up. |
| **Build** | Its desk node + one miner (pays Build wallet, address kept out of git); modest senders to its own node only; miner off/on in B on the same second as the bot; its own fee-read CSV from its node. Build does **not** resync in A (it is the steady reference). |
| **Kaspa Pulse (@gokugalax, kaspapulse.com)** | **Confirmed 14:00 UTC (12 Oct).** Counts 13:30-16:30 UTC on both his nodes: accepted tx and blocks per minute, by block time; logs the fee estimate (priority / normal / low) every minute. His results go to stp first. Not pinged by the bots. |
| **stp** | Fri announcement, message to Pulse, sends GO files (lock), the only halt, OK before anything goes public. |

## Prep (before Fri 16 Oct 14:00 UTC)

1. **Box node:** n2's data was deleted on 12 Oct and no kaspad or miner runs on the box now (checked 12 Oct 17:30 CEST: 95 GB free, 15 GB RAM). Sync a fresh pruned TN10 node **n3** (kaspad 2.1.0, no UTXO index, not archival, loopback only, no tunnel, **never `--enable-unsynced-mining`**: on 29 Sep a fresh-synced node mined a private chain with it, see kaspa-master-file TN10-INCIDENT-2026-09-29) by **Fri 16 Oct 12:00 UTC**. If the box cannot hold it with 20 GB to spare, Run A uses Build's node as the resync node instead and the bot node becomes the reference (log `deviation`).
2. **Disk guard:** stop n3 below 10 GB free (as 11 Oct); Build keeps 30 GB free. Budget pruning bursts of up to 16 GB in 30 min. PR #1148 (lowio) is **not** used on Saturday: one change at a time, unmerged code would confound the fee question. It is a candidate for **its own short run later** (disk writes per hour, with and without lowio; suggested by Kaspa Pulse (@gokugalax, kaspapulse.com)) ([tn10-lowio-1148-notes](https://github.com/STP-KAS/tn10-lowio-1148-notes)). Warn at 20 GB free (`deviation`, as 11 Oct 07:31Z). Never delete the storm SDK in cleanup (tn-master LESSONS).
3. **Coins:** from the bot main wallet (address kept out of git, about 4.69M tKAS). Need is small: see Load. Hold 1.5x before R1. Coins are confirmed against a node, never from an indexer balance alone, and coins already in flight are never reused (ADVICE 7, 8).
4. **Fee reader:** one script, one call per node per minute: `getFeeEstimate` (priority, normal[0], low[0]) plus mempool size and virtual DAA / sink lag, for n3, Build's node, and the five public nodes.
5. GO files written from this plan in the 11 Oct format (GO-BOT / GO-BUILD), locked when stp sends them.

## Load (modest, both runs)

- Bot about **300 tx/s**, Build about **300 tx/s**, plus TN10 base load (~56 tx/s). Total about 650 tx/s, roughly a fifth of Sunday. Same no-signature hop shape; label it "no-signature hop, anyone-can-spend, not realistic payments".
- Fixed fees, no sit-out, no climbs: bot **400**, Build **200** sompi/gram. Fixed fees matter for A: the senders must not react to the quotes we are measuring.
- Coins: 600 tx/s x 8,100 s ≈ 4.9M tx; bot share ≈ 2.4M tx x ~643 grams x 400 ≈ 6,200 tKAS fees. Hold 10,000 tKAS spendable on the bot side.
- At most 8 bot senders, staggered launches, no launch below 1.5 GB free RAM; one scheduler, locked so a second copy cannot start; at most one relaunch per sender per minute (GO-BOT 11 Oct).
- **No node-quote-skip and no sit-out in either run**: on 11 Oct senders skipped nodes quoting above 400; here that would make the senders react to the fee quotes Run A measures. Node-dead rule stays (Kaspa Pulse's rule: stop on 5 s RPC failure, not synced, sink lag > 60 s or DAA stall > 30 s).
- Any rate check reads **every** sender file (11 Oct: a partial read undercounted for about an hour).

## Run A: fees after a resync (14:00-15:00 UTC / 16:00-17:00 CEST)

Question: **after a resync, does the resynced node's fee estimate drift from the other nodes, by how much, and for how long?**

Steps:
1. 14:00-14:10 UTC: baseline. All nodes read every minute (fee reader). Pulse reads his two.
2. 14:10 UTC: stop n3 cleanly (miner off with it, log `node-stop`). Senders do not use n3 anyway.
3. 14:10-14:25 UTC: n3 off for 15 min, so it falls ~9,000 blocks behind and must catch up on restart.
4. 14:25 UTC: start n3 on the same flags (`node-restart`). Log the exact second it reports synced (`node-synced`) and the catch-up time; miner back on only after synced. n3's mempool is empty after restart (not persisted, ADVICE 5), so any entries it shows after catch-up come from peers or the IBD path.
5. Until 15:00 UTC: keep reading every minute. Also log n3 mempool count each minute; if its priority bucket is high, sample 300 mempool tx and check how many are already accepted (as oskrcl did on #1147).
6. **Did n3 actually enter IBD?** A 15-min gap (about 9,000 blocks) may be caught up block by block without IBD (Kaspa Pulse (@gokugalax, kaspapulse.com)'s note). #1147 is about IBD, so log from n3's own log and RPC whether it entered IBD (IBD start/end lines, `isSynced`/sync state), with times. If it did not, Run A still measures fees after a catch-up, but it is **not** an IBD test, and the write-up says so.
7. **Option (stp decides, not default):** force IBD with a longer gap (stop n3 well before 14:00, e.g. from 12:00 UTC, restart at 14:25) or another way that makes the node IBD; labelled `option-ibd` if used.
8. Optional variant only if time allows and stp says so: a short "fell behind without full IBD" case (stop 2 min) to probe OPEN-QUESTIONS 6.

What counts as a result: per minute, n3's priority and normal vs the median of the other nodes (ours + public + Pulse's). **Drift = ratio > 2x.** **Floor case** (Kaspa Pulse (@gokugalax, kaspapulse.com)'s note): at about 650 tx/s nodes may all quote the fee floor, so a ratio can never show drift. Also count drift whenever n3 quotes **above the floor while the median is on the floor**, and log n3's and the median node's mempool count next to every such minute. Report max ratio, minutes until back within 2x, and n3 mempool size. Because n3 mines, also note whether its own block template cleared it (#1147: a mining node self-corrects; supertypo's review asks about re-check cost, so we also log n3 CPU and the catch-up time).

## Run B: a large miner drops out (15:15-16:15 UTC / 17:15-18:15 CEST)

Question: **when a large share of hashrate stops at once, how far do block rate and accepted tx/s fall, how fast, and how fast do they recover?**

Steps:
1. 15:15-15:30 UTC: baseline, all miners on, same load.
2. 15:30:00 UTC: bot and Build stop their miners **on the same second** (`miner-off`). Senders keep going.
3. 15:30-15:50 UTC: 20 min without our hashrate. Log block rate per 10 s and per minute, tx per block, mempool size, fee estimates.
4. 15:50:00 UTC: both miners on (`miner-on`).
5. 15:50-16:15 UTC: recovery. Report time until block rate is back within 5% of baseline.

Miners: exactly one per side, one thread, on the side's own node, no public mining endpoint (11 Oct rule). Before the run, log our hashrate share from the last hour (our blocks / all blocks). If our share is under 15%, **skip Run B** (log `deviation`) rather than add miners (Kaspa Pulse (@gokugalax, kaspapulse.com)'s note); the B window then runs as load-only baseline or ends early, stp decides. Difficulty adjusts over time, so the 20-min window also shows how fast TN10 retargets.

## Logging (CSV per minute, UTC timestamps)

As [LOGGING.md](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/LOGGING.md), plus:

| File | Columns (per minute unless noted) |
|---|---|
| `fee-reads.csv` | utc, node (label only, no host), synced, sink_lag_s, virtual_daa, priority, normal0, low0, mempool_count, mempool_ready |
| `blocks-10s.csv` (per 10 s) | utc, blocks, our_blocks_bot, our_blocks_build, tx_accepted |
| `minute.csv` | utc, network unique accepted, blocks, tx per block, our mined blocks, bot submit-OK, bot accepted, rejects |
| `events.csv` | utc, event (`node-stop`, `node-restart`, `node-synced`, `miner-off`, `miner-on`, `deviation`, `halt`), detail |
| `host.csv` | utc, free disk, free RAM, n3 CPU, box-pause gaps (any gap > 60 s between rows is logged as `box-pause`) |
| `chain-count.csv` | after END, 13:30-16:30 UTC, before pruning |
| `pulse-fee-<date>.csv` | Pulse's file, if he shares it, placed in `credits/kaspa-pulse/2026-10-17/` |

Live `SUMMARY.md` every 15 min. After the run: one page per question, ours vs Pulse side by side.

## Gates

| Gate | UTC | Pass | If fail |
|---|---|---|---|
| G0 | 12:00 | n3 and Build node synced; ≥30 GB free on box; bot coins ≥10k tKAS spendable | Fix by 13:30; if n3 cannot sync, Run A swaps roles (Build node resyncs) |
| G1 (R1) | 13:30 | Fee reader writing all nodes; ≥4 of 5 public nodes answer | Run with what answers, log `deviation` |
| G2 (A start) | 14:00 | Baseline fee ratios within 2x across nodes | If a node is already far off before the resync, log it as a pre-existing outlier and keep going |
| G3 (B start) | 15:15 | n3 synced and mining again; block rate normal (~10/s) | Wait up to 10 min, shift B, log `deviation` |

## Stop rules

- **Only stp halts** (halt order as 11 Oct: flag, watchdog, scheduler, senders 0, wait 10 s, kill senders and miners, check nothing restarts).
- Senders pause automatically (not a halt) if: network block rate falls below 5/s outside Run B's off window; public nodes report mempool full; free disk < 10 GB on box (guard stops n3) or < 30 GB on Build.
- n3 frozen (DAA still > 3 min and sink lag > 3 min) outside the planned stop: restart once, log `node-restart`; in Run A that minute is marked so it is not mixed up with the planned resync.
- Run B: if miners fail to restart by 15:52 UTC, the bot restarts them; never run a second miner.
- Box pause (no rows for > 60 s): logged, nothing manual; the stall detector restarts stalled senders on resume (as 11 Oct 11:56-12:04 CEST). If a box pause lands inside A's 14:10-14:30 window, note that A's result is weaker.
- No climbs, no fee changes, no extensions during the runs. A change is a `deviation`.

## Announcement (stp posts Fri 16 Oct 14:00 UTC / 16:00 CEST in the TN10 channels)

> **TN10 test, Saturday 17 Oct, 14:00-16:15 UTC.** Two short runs, modest load (about 600 tx/s on top of normal traffic, not a storm).
> **14:00-15:00 UTC, fees after a resync:** one of our nodes goes offline for 15 min and resyncs; we log fee estimates from several nodes every minute to see if a resynced node's estimate drifts (related to rusty-kaspa #1147).
> **15:15-16:15 UTC, a large miner drops out:** our miners stop together at 15:30 UTC and restart at 15:50 UTC, to see how block rate and throughput react.
> Expect lower block rate between 15:30 and 15:50 UTC. Kaspa Pulse (@gokugalax, kaspapulse.com) has offered to count both runs independently. Results go public afterwards, counts only. Questions or objections before Saturday are welcome.

## Message from stp to Pulse

Sent privately by stp; not in this public repo (it contains review lines still awaiting Kaspa Pulse's OK).

## Open points for stp

1. OK to sync a new box node n3 (about 60-90 GB of the 95 GB free)? Otherwise Run A resyncs Build's node.
2. Run B only shows something if our share of TN10 hashrate is large; we read it Friday and say if it is too small.
3. Results repo name and whether to push at all.
