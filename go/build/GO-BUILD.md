> **DRAFT, NOT ARMED.** Experimental. Testnet-10 only, tKAS has no value.
>
> **This file means get ready. It does not start the run.**
> When this GO is handed to Build, read it and be ready for the pre-test. Do not send the run, do not stop the node, and do not change fees until the real-test clock below.

# GO-BUILD, Saturday 17 Oct 2026

Plan sheet: [TN10-17-Oct-2026.pdf](../../plan/TN10-17-Oct-2026.pdf). Full plan: [plan/PLAN.md](../../plan/PLAN.md). Build reads this file only. The bot reads GO-BOT.

## Clock

Local time is Brussels, CEST, UTC+2.

| When | UTC | Local |
|---|---|---|
| Pre-test. Be ready. Do not send the run. | Sat 17 Oct 2026, 12:00 | 14:00 |
| Logging opens. Pulse's window opens. | 13:30 | 15:30 |
| Real test starts, two hours after the pre-test. | 14:00 | 16:00 |
| Hard stop. Senders off. | 16:30 | 18:30 |

## What Build does

One desk node. No second kaspad. Pulse's count for 13:30–16:30 UTC is the record. Build sends, and reads this node's fee when the node answers.

- About 300 tx/s through this node. Fee 200 sompi/gram, fixed. No climbs, no quote skip, no sit-out.
- Exactly one miner stays pointed at this node through Run A, including the resync, so we can see how a mining node's fee estimate behaves. The other miners on this PC are off before the pre-test and stay off.
- Chains stay on this node. If the node cannot hold the rate, about a quarter of the senders move to the five public TN10 nodes, the same relief as 11 Oct. Transactions that spend each other stay on one route.
- Own TN10 funds only. No addresses, IPs, or txids in the repo or in the handoff.

## Pre-test, 12:00 UTC / 14:00 local

Be ready. Do not start Run A.

- [ ] One kaspad, synced, version 2.1.0, tip moving. Not `--enable-unsynced-mining`.
- [ ] Exactly one miner on. The others off.
- [ ] Senders built and idle. Not sending the run.
- [ ] One fee read works: priority, normal[0], low[0], plus mempool and sink lag.
- [ ] Disk free above 30 GB.
- [ ] The 11 Oct storm process is not running. Its stop flag is not this run's switch.

## Real test, 14:00 UTC / 16:00 local

Logging has been on since 13:30 UTC. Start only if the plan gate is met. Steps move on their targets. The hard stop wins.

- Run A: stop this node until it is about 9,000 blocks behind, then start the same node again. Log IBD yes or no, the synced second, and fee rows every minute once it answers. The one miner stays on. This is a deviation from the morning line that the resync node never mines. Our count gaps while the node is down.
- Run B: on the bot's gate call, turn that one miner off, then back on. Skip the whole step if our hashrate share is under 15%, and log the skip. Never a second miner.
- 16:30 UTC / 18:30 local: senders off. Do not wipe the node datadir.

## Node-dead

Stop sending if RPC fails for 5 seconds, the node is not synced, sink lag is over 60 seconds, or the virtual DAA score stays still for 30 seconds. Log it. Do not work around it by opening a second node.
