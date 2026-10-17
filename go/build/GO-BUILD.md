> **FROZEN after Pulse agreement 16 Oct.** Final version for Sat 17 Oct 2026: logging 13:30 UTC, start 14:00 UTC, hard stop 16:30 UTC. Modest Run A and Run B, about 600 tx/s total. Agreed with Kaspa Pulse (@gokugalax, kaspapulse.com) on 16 Oct. Fri 16 Oct evening is cancelled. Changes after this are logged as `deviation`.
>
> Experimental. Testnet-10 only. tKAS has no value.
>
> **This file means get ready. It does not start the run.**
> When this GO is handed to Build, read it and be ready for the pre-test. Do not send the run, do not stop the node, and do not change fees until the real-test clock below.
>
> This file stands alone. The shared book is [../README.md](../README.md). Logging columns are [../LOGGING.md](../LOGGING.md). After the hard stop, [../AFTER-RUN.md](../AFTER-RUN.md).

# GO-BUILD, Saturday 17 Oct 2026

## Your job

One desk node. You send about **300 tx/s** to it at **200** sompi/gram, fixed. This node is n3 and never mines. The Run B miners are on a public node, never on this desk node. You read its fee whenever it answers. You write Build's half of the logs.

Pulse's count for 13:30–16:30 UTC is the record. Your chain count sits beside it. Your count gaps while the node is down.

One kaspad. It is n3, so it never mines during Run A (stp, 16 Oct, later decision). No deviation to log. During A2/A3 your node is only a partial monitor; the five public nodes carry the continuous reads.
> **17 Oct, stp.** Run B is to run. Fifteen CPU miners, one thread each, on one public node (muon-10, electron-10, neutrino-10, boson-10, or vector-10). **n3 never mines.** No miner points at the desk node. B1 those 15 stay on, B2 all 15 off (25 min max), B3 all 15 back on until within 5% of the B1 baseline. If B0 is still under 15% at 15:45 UTC, stop the miners at 16:30 UTC. Senders come first. If no public node accepts miner connections, stop and tell stp. Do not point them at n3.

> **B2 pause (Pulse, 16 Oct).** If Run B runs, the 1984 water test and the KNS lanes pause for all of B2 (from `miner-off` until the bot calls B3), so they do not blur the miner drop. The bot signals the pause and the resume.

> **Not Saturday.** The congestion ramp (up to about 8,000 tx/s, soft freeze at 80,000 mempool, cut at 90,000, below the 100,000 panic) is a future run, not Saturday. Saturday keeps the modest load and keeps the 80k soft freeze / 90k cut only as safety margins.


## Your desk node

One kaspad. Private datadir, pruned, version 2.1.0, UTXO index on. Not `--enable-unsynced-mining`. No second kaspad. Do not wipe the data.

The 11 Oct storm process is not this run. Its stop flag is not your switch.

## Where you send

- Start on this node only. Fee 200 sompi/gram. No climb, no sit-out, no quote skip.
- About 300 tx/s from 14:00 UTC, and only if the bot calls G1 passed.
- While the node is up and cannot hold that rate: move about a quarter of your senders to the five public nodes, and move them back after 2 minutes of good health. Log `node-fallback` and `node-back`. Transactions that spend each other stay on one route.
- The five public nodes: `muon-10.kaspa.blue`, `boson-10.kaspa.red`, `electron-10.kaspa.blue`, `neutrino-10.kaspa.stream`, `vector-10.kaspa.green`.
- During Run A step A2 the node is stopped on purpose. Hold your senders. Do not fill that gap on the public nodes. `sent` 0 in those seconds is measured. Log `node-stop`.

## Node-dead rule

Stop submitting to the desk node on 5 seconds of RPC failure, not synced, sink lag over 60 seconds on a fresh read, or virtual DAA still for 30 seconds. Resume when a fresh read is healthy. A failed submit is a reject.

On a public node you are using for relief: about 20 errors in a row cools that node for 30 seconds.

Do not open a second node to get around a failure.

## Operating principle: target first

Same principle as last run. The target is the goal, not the timeframe. We run inside a closed window (14:00-16:30 UTC), but the tx target is what counts, so the bot and Build keep going. On a bug or error: restart, find a fix, continue within safety caps.

## Coins

Own TN10 funds only. Confirm them on a node. Never reuse a coin still in flight. Never fetch a whole address in one UTXO call. No address, txid, or path in the repo or in the handoff.

## Your miner

n3 never mines. No miner points at the desk node, in Run A or in Run B.

Fifteen CPU miners, one thread each, on one public node from the five named above. They pay the Build wallet. The address stays out of git.

- On for the hour before B0, so the share can be measured.
- B1: those 15 stay on. That is the baseline.
- B2: all 15 off until the block rate has fallen and held, 25 minutes at most. Under 5 blocks/s for 2 minutes: turn them back on and log that.
- B3: all 15 back on until the block rate is within 5% of the B1 baseline.
- If B0 fails at 15:45 UTC, stop them at 16:30 UTC.
- Senders come first. Drop miners if the send rate or this node's RPC suffers.
- If no public node accepts miner connections, stop and tell stp. Do not point them at n3.

`miner.csv` every 10 seconds, including the minutes it is off.

## Clock

Local time is Brussels, CEST, UTC+2.

| When | UTC | Local |
|---|---|---|
| Pre-test. Be ready. Do not send. | Sat 17 Oct 2026, 12:00 | 14:00 |
| Logging opens. Pulse's window opens. | 13:30 | 15:30 |
| Real test. Run A, if G1 passed. | 14:00 | 16:00 |
| G1 still open: Run A called off | 14:30 | 16:30 |
| Hard stop. Senders off. Public-node miners off if Run B was skipped. | 16:30 | 18:30 |

## Steps

You do not advance a step. The bot calls the gate. You act when the call names you.

| Step | What you do | Done when |
|---|---|---|
| Pre-test | Checklist below. Write the ready file. Senders idle. | 12:00 UTC, ready file written |
| G1 | Logging on. Senders still idle. Fee read works. | Bot calls pass, or 14:30 UTC calls Run A off |
| A1 | Send about 300 tx/s. n3 not mining. | Bot calls 10 clean minutes, or 30 min cap |
| A2 | stp stops the node. You hold senders. n3 not mining. | About 9,000 blocks behind, or 45 min |
| A3 | stp starts the same node. You log IBD yes or no, the synced second, and the catch-up. Fee rows every minute once it answers. | Synced, or 45 min |
| A4 | Keep sending once the node answers. Keep the fee rows. | 10 minutes back within 2x of the median and off the floor when the median is, or 60 min after synced |
| B0 | Public-node miners stay on. n3 stays non-mining. | Share at least 15% and block rate about 10/s for 10 min, or skip by 15:45 UTC |
| B1 | The 15 stay on. Keep 300 tx/s. | 10 minutes steady, or hard stop |
| B2 | All 15 off. Water and KNS paused by the bot. | Block rate fallen and held 10 min, or 25 min, or under 5/s for 2 min |
| B3 | All 15 back on. | Block rate within 5% of the B1 baseline for 10 min, or hard stop |
| END | Senders off. Miners off if B0 was skipped, otherwise as B3 left them. Node stays up. | 16:30 UTC |

A frozen node in A3 (virtual DAA still for over 3 minutes and sink lag over 3 minutes): stp may restart it once. You log that restart. You do not start a second process.

## Log everything

From 13:30 UTC. Your files, columns in [../LOGGING.md](../LOGGING.md):

`sec.csv`, `minute.csv`, `fee-reads.csv` (node `desk`), `public-nodes.csv` (only if relief senders ran), `rejects.csv`, `miner.csv`, `host.csv`, `node.csv`, `events.csv`, `steps.csv`, `gates.csv`, `ntp.csv`, `chain-count.csv`, `confirm.csv`, `SUMMARY.md`.

Fee read each minute the node answers: priority, normal[0], low[0], mempool count, sink lag, virtual DAA. Empty cells while it does not answer.

## Handoff

By 12:00 UTC, give stp `FOR-TN10-OPS-ready-1017.txt`:

```
build_ready: yes
written_utc: 2026-10-17T12:00:00Z
own_node_synced: yes | no
kaspad_version: 2.1.0
senders_idle: yes
target_tx_s: 300
fee: 200
miner: off
other_miners: off
disk_free_gb: <n>
notes: <anything the bot should know>
```

After the hard stop, `FOR-TN10-OPS-result-1017.txt` as in [../AFTER-RUN.md](../AFTER-RUN.md).

## Pre-test checklist

- [ ] One kaspad, synced, version 2.1.0, tip moving. Not `--enable-unsynced-mining`.
- [ ] No miner pointed at this node. n3 is not mining.
- [ ] Senders built and idle. Not sending the run.
- [ ] One fee read works: priority, normal[0], low[0], mempool, sink lag.
- [ ] Disk free above 30 GB.
- [ ] Ready file written.
- [ ] Logger is armed for 13:30 UTC and is not writing yet, unless the clock has already reached 13:30.

## Never

- A second kaspad, a wiped datadir, or `--enable-unsynced-mining`.
- A miner pointed at n3.
- Filling the A2 gap on the public nodes.
- A climb, a sit-out, or a quote skip.
- An address, a txid, an IP, a port, or a path in git or in the handoff.
- Starting because this file arrived. Wait for the clock and for G1.
- Halting the run. Only stp halts.
