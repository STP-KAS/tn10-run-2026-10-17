> **DRAFT, NOT ARMED.** Experimental. Testnet-10 only. tKAS has no value.
>
> **This file means get ready. It does not start the run.**
> When this GO is handed to Build, read it and be ready. Do not send until the start of the window stp confirms.
>
> This file is Build's order. Where [../README.md](../README.md) still says a flat 360 tx/s, or a miner on through Run A, this file wins for Build.

# GO-BUILD, congestion ramp, 16–17 Oct 2026

Credit for the node-dead rule and the independent count: Kaspa Pulse (@gokugalax, kaspapulse.com). Do not contact Pulse. His count is the reference.

## Which window

stp confirms the window after Pulse. One window runs.

| Window | Logging | Start | Hard stop |
|---|---|---|---|
| **Tonight, first.** Fri 16 Oct, if stp says it is on. | 21:30 UTC (23:30 CEST) | **22:00 UTC** (00:00 CEST) | **00:30 UTC** Sat 17 Oct (02:30 CEST) |
| Saturday, fallback, if tonight is off. | 13:30 UTC (15:30 CEST) | **14:00 UTC** (16:00 CEST) | **16:30 UTC** (18:30 CEST) |

Do not start before the start time. The hard stop ends the senders wherever the ramp is. There is no extension.

## Operating principle: target first

Same principle as the 11 Oct storm. **Keep going, but fix first.**

- The ramp step's target is the goal. The clock does not advance a step.
- Inside the closed window, keep going. On a bug, error, crash, or stall: restart, find a fix, continue the open step, and log `fix` or `deviation`.
- A safety cap can hold, ease, or stop. It cannot move the run to the next step.

## Goal

Fill TN10 mempools and keep them high for a long stretch. A steady 600 tx/s total will not do that. Light hops pack near 7,700 tx/s. The two sides ramp together past about 4,500 toward 6,000–8,000 tx/s total, then hold just above the rate where the watched mempools stop growing.

## Ramp

Both sides move together. Build's share is about half of each step, through the desk node. Fee on this share is **200** sompi/gram, fixed. No climb on these senders.

| Step | Total tx/s | Build's share |
|---|---|---|
| R0 | 600 | 300 |
| R1 | 1,200 | 600 |
| R2 | 2,000 | 1,000 |
| R3 | 3,000 | 1,500 |
| R4 | 4,500 | 2,250 |
| R5 | 6,000 | 3,000 |
| Hold | the step just above the last rate where watched mempools were still growing | half of that total |
| Cap | 8,000 | 4,000 |

A step advances only when, for 10 continuous minutes:

- Build's accepted rate is at least 90 percent of that step's share
- rejects are under 2 percent of submits
- node-dead is not holding the side
- the lanes are still sticky
- the watched mempool median is still growing, and it is under 80,000

If the watched mempools are flat or falling for those 10 minutes, do not advance. Hold at the step just above the last one where they were still growing.

The first step replaces the earlier flat 360 tx/s. 300 tx/s is R0, not the whole run.

## Sticky lanes

A chain of spends stays on one node for the life of that chain. The desk node is the route.

- Do not split a chain across nodes.
- Do not hop a lane because another node looks quieter.
- KNS is using boson, neutrino, and vector for three fee lanes in this same window. Do not put bulk there.
- If the desk node is up and cannot hold the open step, move a whole lane, not one spend of a chain, to `muon-10.kaspa.blue` or `electron-10.kaspa.blue` only. Log `node-fallback`. Move it back after 2 minutes of good health.
- During Run A step A2 the desk node is stopped on purpose. Hold the senders. Do not fill that gap on any public node. `sent` 0 in those seconds is measured. Log `node-stop`. The open ramp step resumes when the node is back and a fresh read is healthy. It does not skip ahead.

## Miner

**Off for all of Run A.** n3 is this desk node. It does not mine during the resync (Sutton, rusty-kaspa #1147). A mining template would hide the stale-mempool effect.

- Before the start, this miner is off. The other miners on this PC are off.
- It stays off through A1, A2, A3, and A4, including the gap and the catch-up.
- Run B is likely skipped. It runs only if the bot calls B0 passed: our blocks were at least 15 percent of all blocks over the last hour, and the block rate was about 10/s for 10 minutes. With the miner off, that share will often miss. A skip is logged. Do not add a miner to make the share.
- If B0 passes: B2 turns that one miner off, B3 turns it back on. Never a second miner.
- If Run B is skipped, the miner stays off through the hard stop.

A low block rate during Run A is logged. It is not by itself a reason to halt the ramp. The miner is off on purpose.

## Run A and Run B

The bot calls the gates. Build does not advance them.

| Step | What Build does | Done when |
|---|---|---|
| A1 | Ramp from R0 while the node is up. Miner off. | The ramp's own 10-minute rule, and 10 clean fee minutes, or the 30 min cap |
| A2 | Node stopped by stp. Senders held. Miner off. | About 9,000 blocks behind, or 45 min |
| A3 | Same node started again. Log IBD yes or no, the synced second, and the catch-up. Fee and mempool every minute once it answers. Miner off. | Synced, or 45 min. Frozen (DAA still over 3 min and sink lag over 3 min): one restart, logged |
| A4 | Ramp resumes on the open step. Miner off. | 10 minutes of fee rows after synced, or 60 min. Still drifting is a result |
| B0 | Wait. | Share at least 15 percent, or skip. Latest skip 90 minutes after the start |
| B1–B3 | Only if B0 passed. One miner off, then on, on the bot's call. | The bot's call, or the hard stop |

## Node-dead

Kaspa Pulse's rule. A failed submit is a reject.

- Desk node: stop submitting to it on 5 seconds of RPC failure, not synced, sink lag over 60 seconds on a fresh read, or virtual DAA still for 30 seconds. Resume on a fresh healthy read.
- A public lane, if one is in use: about 20 errors in a row cools that node for 30 seconds.
- Do not open a second kaspad.

## Stop rules

| Rule | What Build does |
|---|---|
| Hard stop of the confirmed window | Senders off. Node stays up. Datadir is not wiped. |
| stp halt | Senders off. |
| Watched mempool median nears 80,000 | Soft freeze. Do not advance. Ease one ramp step. |
| Watched mempool median nears 90,000 | Cut. Senders down until the median is back under 80,000, then resume the **same** step. Stay clear of the 100,000 panic class. |
| Desk free disk under 30 GB | Pause the senders and log `disk-guard`. Warn at 20 GB. The node stops below 10 GB free. |
| Desk node dead outside the planned A2 stop | Hold, fix, continue the open step when a fresh read passes. |

Watched mempools are the desk node plus at least one other live node (`muon` or `electron`). One node's mempool can be stale. Read them often enough to cut before 90,000, and log the size every minute.

## Desk node

One kaspad. Private datadir, pruned, version 2.1.0, UTXO index on. Not `--enable-unsynced-mining`. No second kaspad. The 11 Oct storm process is not this run. Its stop flag is not this run's switch.

## Pre-check, before logging opens

Do this once the GO is in hand. It is not the ramp.

- [ ] One kaspad, synced, version 2.1.0, tip moving, peers present.
- [ ] Local RPC on this machine answers a fee read, a mempool size, and a block read.
- [ ] Disk free above 30 GB. CPU and free RAM noted in the ready line, as numbers only.
- [ ] Miner off. Other miners off.
- [ ] Sender smoke: at most 50 tx/s, 2 minutes, own TN10 funds, sticky to this node. Stop at the first burst of rejects. The smoke is not R0.
- [ ] Logger armed for the confirmed window's logging time.

Ready line for stp, keys only, no address and no path:

```
build_ready: yes
window: tonight | saturday
own_node_synced: yes | no
kaspad_version: 2.1.0
peers_present: yes | no
local_rpc: yes | no
miner: off
smoke: ok | fail
disk_free_gb: <n>
```

## Logging

For the whole window, including the minutes the node is down (those cells empty, not a fake zero):

- Accepted transactions and blocks per minute, by block time.
- Fee estimate every minute: priority, normal[0], low[0].
- Mempool size every minute, desk and the watched public node.

Also keep Build's `sec.csv`, `minute.csv`, `events.csv`, and `node.csv` from [../LOGGING.md](../LOGGING.md) where those columns still fit. `network_unique_accepted` is never added to the bot's count. Pulse's count is the record. The chain count for the window is written after the hard stop, before pruning.

Empty means not measured. A held sender's `sent` of 0 is measured.

## Sender-stable word

When the open step has been stable for 10 continuous minutes under the water GO's definition, tell stp "senders stable". The 1984 water buyer starts only on that word, and only inside the same window. Its fee rule is its own GO. This file does not start the buyer and does not raise the bulk fee to match it.

## Coins

Own TN10 funds only. Confirm them on a node. Never reuse a coin still in flight. Never fetch a whole address in one UTXO call. No address, txid, IP, port, or path in the repo or in the handoff.

## Never

- A second kaspad, a wiped datadir, or `--enable-unsynced-mining`.
- A miner during Run A, or a second miner in Run B.
- Bulk on boson, neutrino, or vector.
- A chain split across nodes.
- A climb of the 200 sompi/gram bulk fee.
- Starting because this file arrived.
- Contacting Kaspa Pulse.
- Halting the run. Only stp halts.
