> Hand this file to the TN10 bot. The bot writes `go/bot/GO-BOT.md` on the Saturday repos and stops. The bot does not start the run.

# Prompt: bot, write your GO for Saturday 17 Oct 2026

You are the TN10 bot. Write your own go file and commit it.

Repos:

- https://github.com/STP-KAS/tn10-run-2026-10-17 (public)
- https://github.com/STP-KAS/tn10-run-2026-10-17-plan (private)

Put the same file in both, at `go/bot/GO-BOT.md`. Do not put IPs, addresses, or txids in either repo.

Read [plan/PLAN.md](../../plan/PLAN.md) and [plan/TN10-17-Oct-2026.pdf](../../plan/TN10-17-Oct-2026.pdf) first. Build's file is [go/build/GO-BUILD.md](../build/GO-BUILD.md). Do not edit Build's file.

## What GO means

The first lines of GO-BOT must say this, in these words:

> This file means get ready. It does not start the run.
> When this GO is handed to you, read it and be ready for the pre-test. Do not send until the real-test clock.

Receiving GO is not the start gun.

## Clock, write it in the GO

Local time is Brussels, CEST, UTC+2.

| When | UTC | Local |
|---|---|---|
| Pre-test. Be ready. Do not send. | Sat 17 Oct 2026, 12:00 | 14:00 |
| Logging opens. Pulse's window opens. | 13:30 | 15:30 |
| Real test starts, two hours after the pre-test. | 14:00 | 16:00 |
| Hard stop. Senders off. | 16:30 | 18:30 |

## What the bot's GO must contain

- No box node and no miner. The box stalled overnight 15–16 Oct, 11 IBD restarts, never synced, and it is stopped.
- Sends and fee reads go to the five public TN10 nodes only. About 360 tx/s. Fee 400 sompi/gram, fixed. No climbs, no quote skip, no sit-out.
- Pulse's count for 13:30–16:30 UTC is the record. The bot does not replace that count.
- The bot calls the gates. Run A: ask stp to stop and restart the one desk node. That node keeps its one miner on through the resync. Run B: ask Build to turn that one miner off, then on, and only if hashrate share is at least 15%. Otherwise skip Run B and log it.
- Pre-test checklist: at least 4 of 5 public nodes answer, senders connected and idle, coins confirmed on a node, fee read working. Do not send the run at 12:00 UTC.
- Real test starts at 14:00 UTC only if the plan gate is met. Hard stop 16:30 UTC. Only stp halts before that.
- Node-dead rule: stop on 5 s RPC failure, not synced, sink lag over 60 s, or virtual DAA still for 30 s.
- Label the load: no-signature hop, anyone-can-spend, not realistic payments. Testnet-10 only. tKAS has no value.

Write the file, commit it on both repos, and stop. Do not start senders.
