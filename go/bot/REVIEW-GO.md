> **This file is a review. It is not a GO.**
>
> It does not start the run. It does not mean get ready. Do not send. Do not write `GO-BOT.md` from this file. Do not stop or start a node. Do not turn a miner on or off.
>
> Reply to stp. Agree, or disagree with a reason and the change you want. Silence is not a yes.

# Review for the bot, Saturday 17 Oct 2026

Please read this and say if you can run your side as written. The shared book is [../README.md](../README.md). The columns are [../LOGGING.md](../LOGGING.md). Build's file, for context, is [../build/GO-BUILD.md](../build/GO-BUILD.md). You do not edit Build's file.

This review follows the shape of the [11 Oct GO](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/bot/GO-BOT.md) and the [10 Oct bot run](https://github.com/STP-KAS/tn10-storm-2026-10-10/blob/main/go/bot-run/GO-BOT-RUN.md). The rates and the questions are Saturday's, not those storms.

## Your side, as written

- You have **no node and no miner**. The box node stays stopped.
- You send about **360 tx/s** to the five public nodes only. Fee **400** sompi/gram, fixed. No climb, no sit-out, no quote skip.
- The five names: `muon-10.kaspa.blue`, `boson-10.kaspa.red`, `electron-10.kaspa.blue`, `neutrino-10.kaspa.stream`, `vector-10.kaspa.green`.
- At most 8 senders, one scheduler, staggered launch, at most one relaunch per sender per minute, no launch under 1.5 GB free RAM.
- A public node with about 20 errors in a row is cooled for 30 seconds. Load moves to the other live ones. You do not stop the whole side because one public node fails.
- You call every gate. You do not advance a step on the clock.
- You ask stp to stop and restart the one desk node in Run A. That node keeps its one miner on through the resync.
- You ask Build to turn that miner off, then on, only if Run B's gate passes (hashrate share at least 15% over the last hour, block rate about 10/s for 10 minutes). Otherwise you skip Run B and log `skip`.
- You do not ping Kaspa Pulse. His count for 13:30–16:30 UTC is the record.
- You log your half from 13:30 UTC. Your chain count reads one named public node. You do not add it to Build's count.
- Only stp halts.
- Label on the load: no-signature hop, anyone-can-spend, not realistic payments. Testnet-10 only. tKAS has no value.

## Clock

| When | UTC | Local (Brussels, CEST) |
|---|---|---|
| Pre-test. Be ready. Do not send. | Sat 17 Oct 2026, 12:00 | 14:00 |
| Logging opens. Pulse's window opens. | 13:30 | 15:30 |
| Real test, Run A, if you have called G1 passed. | 14:00 | 16:00 |
| G1 still open: call Run A off, check Run B. | 14:30 | 16:30 |
| Run B still open: skip it. | 15:45 | 17:45 |
| Hard stop. Your senders off. | 16:30 | 18:30 |

## Gates you would call

| Gate | Pass when | If not |
|---|---|---|
| G1 | Fee rows every minute, at least 4 of 5 public nodes answer, your senders connected and idle, the one desk miner confirmed on, at least 10 baseline fee rows | Hold. Still open at 14:30 UTC: Run A off. |
| A1 | 10 complete minutes at 360 tx/s from you and from Build. Priority and normal on each read node within 2x of the median. | Hold. 30 min: Run A stops. |
| A2 | Desk node about 9,000 blocks behind. Build is not filling that gap on the public nodes. | Keep it off. 45 min: restart and log `gate-fail`. |
| A3 | Desk node synced. IBD yes or no logged from its own log. | Hold. 45 min: Run A stops. Frozen (DAA still over 3 min and sink lag over 3 min): one restart. |
| A4 | Back within 2x of the median for 10 minutes, and off the fee floor when the median is. | Hold. 60 min after synced is itself a result. Run A ends. |
| B0 | Blocks found by the one desk miner were at least 15% of all blocks over the last hour. Block rate about 10/s for 10 minutes. Desk node synced. | Skip Run B. Latest skip 15:45 UTC. |
| B1 | 10 minutes, miner on, block rate and tx per block steady. | Hold until the hard stop. |
| B2 | Miner off. Block rate has fallen and held 10 minutes. | 25 min, or under 5/s for 2 min: miner back on. |
| B3 | Miner on. Block rate within 5% of the B1 baseline for 10 minutes. | Hold until the hard stop. Never a second miner. |

## What Saturday does not take from 10 Oct and 11 Oct

Those runs are the pattern for the files, the columns, the lock, and the node-dead rule. They are not the pattern for the load.

- Not a flat-out target, and not a climb toward 3,000 tx/s.
- Not a bot node, and not a bot miner.
- Not a sit-out, and not a quote skip.
- Not two kaspad processes on the desk.
- Not moving Build's Run A gap onto the public nodes.

## Please answer, point by point

1. Can you send about 360 tx/s at fee 400, fixed, on those five public nodes, with no node of your own?
2. Are those five names still the public TN10 nodes you will use? If one has changed, give the replacement name and no IP.
3. Do you agree to call the gates above, and to leave the clock where it is when a target is not met?
4. Do you agree that during A2 you keep your own 360 tx/s on the public nodes, and you do not ask Build to replace its stopped node with public-node sends?
5. Do you agree you have no miner, and that the one desk miner stays on through Run A?
6. Do you agree Run B is skipped when the last-hour share is under 15%?
7. Do you agree to log from 13:30 UTC in the columns of [LOGGING.md](../LOGGING.md), with an empty cell when a read did not happen?
8. Do you agree Pulse's count is the record, and that you will not add your chain count to Build's?
9. Is anything here something you cannot do, or something you would do differently? Say what, and the change you want.

Reply to stp. Do not commit `GO-BOT.md`. Do not start senders.

If stp accepts your review, the next file is [PROMPT-GO-BOT.md](PROMPT-GO-BOT.md). That prompt is what tells you to write `GO-BOT.md`. This review does not.
