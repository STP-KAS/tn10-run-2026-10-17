> **DRAFT, NOT ARMED.** Experimental. Testnet-10 only. tKAS has no value.
>
> Handing over a GO means get ready for the pre-test. It does not start the run.
>
> [go/bot/REVIEW-GO.md](bot/REVIEW-GO.md) is a review for the bot. It is not a GO. The bot does not write `GO-BOT.md` from it.

# go: the shared rule book, Saturday 17 Oct 2026

Same shape as [11 Oct go](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/README.md) and [10 Oct go](https://github.com/STP-KAS/tn10-storm-2026-10-10/blob/main/go/README.md). The job is different: two short target-gated runs, one question each, modest load. Not a flat-out storm.

| File | Who reads it |
|---|---|
| [build/GO-BUILD.md](build/GO-BUILD.md) | Build. Stands alone. |
| [bot/REVIEW-GO.md](bot/REVIEW-GO.md) | Bot, for a yes or no. Not a start. |
| [bot/PROMPT-GO-BOT.md](bot/PROMPT-GO-BOT.md) | Bot, only after stp accepts the review. The bot then writes `GO-BOT.md`. |
| [LOGGING.md](LOGGING.md) | Both sides. Same columns. |
| [AFTER-RUN.md](AFTER-RUN.md) | Both sides, after the hard stop. |
| [../plan/PLAN.md](../plan/PLAN.md) | Full plan. If a number there disagrees with this folder, this folder wins for the run, and the difference is a `deviation` logged to stp. |

Brussels is CEST, UTC+2. Clocks in git stay in UTC. One clock for both sides.

## What Saturday measures

1. **Run A.** After the desk node falls about 9,000 blocks behind and catches up, does its fee estimate drift, by how much, and for how long?
2. **Run B.** Only if our hashrate share over the last hour is at least 15%. When the one miner stops, how far do block rate and throughput fall, and how fast do they come back?

Load: bot about **360 tx/s**, Build about **360 tx/s**, plus TN10 base load (about 56 tx/s). Fees fixed: bot **400** sompi/gram, Build **200**. No climbs.

The headline count is **network unique accepted transactions, each id once**, from chain data. Never add the bot's count to Build's count. Kaspa Pulse's count for 13:30–16:30 UTC is the record. Ours sits beside it.

## Clock

| What | Brussels (CEST) | UTC |
|---|---|---|
| Pre-test. Read the GO. Be ready. Do not send. | Sat 17 Oct 14:00 | 12:00 |
| Logging opens. Pulse's window opens. | 15:30 | 13:30 |
| START, Run A, only if gate G1 is met | **16:00** | **14:00** |
| G1 still open: Run A is called off, Run B's gate is checked | 16:30 | 14:30 |
| Run B skipped if its gate is still open | 17:45 | 15:45 |
| Pulse's count | 15:30–18:30 | 13:30–16:30 |
| **Hard stop.** Senders off. The one miner is on. | **18:30** | **16:30** |
| Chain count, before pruning | right after 18:30 | right after 16:30 |

A step moves when its target is reached. A clock never advances a step. A safety cap can only hold, skip, or stop.

If G1 is not met at 14:00 UTC, START waits. If it is still not met at 14:30 UTC, Run A is called off and logged, and Run B's gate is checked.

## Who does what

| Side | Who | Sends to | Node | Miner |
|---|---|---|---|---|
| Bot | TN10 ops sends. tn ops2 logs, counts, writes the live summary. | The five public TN10 nodes only | None. The box node stays stopped. | None |
| Build | Grok Build on stp's desk | Its desk node. About a quarter may move to the five public nodes only while that node is up and cannot hold the rate. | One private pruned kaspad, version 2.1.0, UTXO index on. This same node is the resync node. | Exactly one, through Run A, including the gap |
| Pulse | Kaspa Pulse (@gokugalax, kaspapulse.com) | Does not send for us | His own nodes | His own concern |
| stp | The only halt | — | Says when the desk node stops and starts in Run A | — |

The five public nodes, same names as the 10 Oct run: `muon-10.kaspa.blue`, `boson-10.kaspa.red`, `electron-10.kaspa.blue`, `neutrino-10.kaspa.stream`, `vector-10.kaspa.green`.

Never submit to the other side's path. Chained transactions stay on one route.

## Rules

- **Fees.** Bot 400, Build 200, fixed for the whole window. No climb, no sit-out, no quote skip. A quote that would have tripped the 11 Oct sit-out is logged and sending continues. Doing a sit-out or a quote skip is a `deviation`.
- **Node-dead, public node (bot, and Build's relief senders).** About 20 connection or RPC errors in a row cools that node for 30 seconds. Load moves to the other live public nodes. No live node: wait, and count no attempt. Log `node-dead` and `node-back`.
- **Node-dead, desk node (Build).** Stop submitting to it on 5 seconds of RPC failure, not synced, sink lag over 60 seconds on a fresh read, or virtual DAA still for 30 seconds. Resume when a fresh read is healthy.
- **Planned stop (Run A, step A2).** The desk node is stopped on purpose until it is about 9,000 blocks behind. Build does not submit to it, and does not move that load onto the public nodes to fill the gap. The bot keeps 360 tx/s on the public nodes. Build's `sent` in those seconds is 0 because the senders were held. That 0 is measured. Log `node-stop`.
- **Relief.** While the desk node is up and cannot hold 360 tx/s, about a quarter of Build's senders move to the five public nodes, then come back after 2 minutes of good health. Log `node-fallback` and `node-back`. Chains stay on one route.
- **Gates.** The bot calls them. The next step starts only on `gate-pass`. Hold is `gate-hold`. A safety cap that ends a step is `gate-fail` or `skip`, with the reason. stp stops and restarts the desk node. Build turns the one miner off and on when the bot calls Run B.
- **One miner.** It stays pointed at the desk node through Run A, including while that node is stopped and catching up. The other miners on the desk PC are off before the pre-test and stay off. Run B turns that one miner off, then on. Never a second miner. At the hard stop the one miner is on.
- **This is a deviation** from the morning line that the resync node never mines, and from the morning line that Build does not resync. On this PC those two roles are the same machine. The 16 Oct decision wins.
- **Disk.** Build keeps at least 30 GB free, and warns at 20 GB. The desk node stops below 10 GB free. A pruning burst can be up to 16 GB in 30 minutes.
- **Senders.** At most 8 bot senders, one scheduler, launches staggered, at most one relaunch per sender per minute, no launch under 1.5 GB free RAM. Build's sender count stays inside what the desk node held on the 16 Oct pre-check.
- **Coins.** Confirm spendable coins on a node. Never from an indexer balance alone. Never reuse a coin still in flight. Never fetch a whole address in one UTXO call. Bot holds at least 10,000 tKAS spendable. No address in git.
- **Count** `sent`, `submit_ok` and accepted from the senders' own per-second records. An hour that shows 0 sent while the senders logged sends is a counting bug: fix it, re-count, log `deviation`.
- **Chain count** for 13:30–16:30 UTC, right after the hard stop, before pruning. See [AFTER-RUN.md](AFTER-RUN.md).
- **Log** from 13:30 UTC to the hard stop, even when a side is sending nothing. [LOGGING.md](LOGGING.md).
- **Only stp halts.** A problem is fixed and logged (`fix`), then the open step continues. A fix does not advance a gate and does not move the clock.
- **Public repo.** Counts only. No key, seed, txid, IP, host, port, tunnel URL, wallet address, or local path. Public node names are allowed. Leak-scan before every push. No force-push, no tags, no releases.

## What we kept, and what Saturday does not use

| | Item | Why |
|---|---|---|
| Kept | One shared clock, one logging shape, a GO each side can run alone | 10 Oct and 11 Oct. The two sides can be lined up with Pulse. |
| Kept | Node-dead rule, chain count before pruning, empty cell means not measured | Kaspa Pulse. A missing read must not look like a zero. |
| Kept | Lock after stp sends the GO. Later changes are `deviation`s. | 11 Oct. |
| Kept | One scheduler, staggered launch, relaunch limit, RAM floor | 10 Oct lesson, when a double launch collapsed the rate. |
| Kept | Target gates, safety caps that hold or stop | 11 Oct reached a median of 3,107 tx/s on one target. 10 Oct's clock steps moved on early. Its best step median was about 2,642 tx/s. |
| Not used | Flat-out for hours, rate climbs, sit-out, quote skip | Saturday's questions need a steady modest load. |
| Not used | A bot node and a bot miner | The box node never synced (11 IBD restarts, 15–16 Oct). It stays stopped. |
| Not used | A second desk kaspad | Retired 10 Oct. |
| Not used | Filling the Run A gap with public-node sends | The gap is part of the question. |

## Lock

These files are not locked. The bot review is open.

Once stp sends GO-BUILD and GO-BOT, they are locked. Sending them means get ready for 12:00 UTC. It does not mean start. A change after the lock goes in `events.csv` as a `deviation`. The run log records the commit SHA of the files that were sent.

## stp, before the pre-test

1. Hand [REVIEW-GO.md](bot/REVIEW-GO.md) to the bot. Wait for agree or disagree.
2. After an agree, hand [PROMPT-GO-BOT.md](bot/PROMPT-GO-BOT.md). The bot writes `GO-BOT.md` and stops.
3. Hand [GO-BUILD.md](build/GO-BUILD.md) to Build.
4. At the pre-test, pass Build's `FOR-TN10-OPS-ready-1017.txt` to the bot.
