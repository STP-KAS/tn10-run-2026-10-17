> Do not follow this prompt until stp says the review in [REVIEW-GO.md](REVIEW-GO.md) is accepted.
>
> After that: write `go/bot/GO-BOT.md` and stop. Do not start the run. Do not send.

# Prompt: bot, write your GO for Saturday 17 Oct 2026

You are the TN10 bot. The review is already agreed. Write your own go file and commit it.

Repos:

- https://github.com/STP-KAS/tn10-run-2026-10-17 (public)
- https://github.com/STP-KAS/tn10-run-2026-10-17-plan (private)

Put the same file in both, at `go/bot/GO-BOT.md`. Do not put IPs, addresses, or txids in either repo.

Read [../README.md](../README.md), [../LOGGING.md](../LOGGING.md), [REVIEW-GO.md](REVIEW-GO.md), and [../build/GO-BUILD.md](../build/GO-BUILD.md). Do not edit those files. If your review changed a point, and stp accepted that change, write the accepted change. Do not silently pick a different rate or a different clock.

## What GO means

The first lines of GO-BOT must say this, in these words:

> This file means get ready. It does not start the run.
> When this GO is handed to you, read it and be ready for the pre-test. Do not send until the real-test clock.

Receiving GO is not the start gun.

## Sections, in this order

Use the same section shape as the 11 Oct GO and the 10 Oct bot run.

1. GO
2. Your job
3. Where you send
4. No node, no miner
5. Fees
6. Coins
7. The desk node and the one miner (what you ask stp and Build to do)
8. Clock
9. Gates you call
10. Log everything
11. Halt
12. After END
13. Never

## Facts that must be in the file

- No box node and no miner. The box stalled overnight 15–16 Oct, 11 IBD restarts, never synced, and it is stopped.
- About 300 tx/s on the five public nodes. Fee 400 sompi/gram, fixed. No climb, no sit-out, no quote skip.
- Public-node failure: about 20 errors in a row cools that node for 30 seconds. One dead public node does not stop the side.
- You call the gates in [REVIEW-GO.md](REVIEW-GO.md). A step moves on `gate-pass` only.
- Run A: ask stp to stop and restart the one desk node. That node never mines; its miner stays off through Run A. Build does not fill the A2 gap on the public nodes. You keep 300 tx/s.
- Run B: ask Build to turn that one miner off, then on, only if the share gate passes. Otherwise `skip`. If B runs, pause water and KNS for all of B2.
- Write `pulse-minute.csv` every minute: mempool per public node and rejects by reason.
- FROZEN after Pulse agreement 16 Oct. The congestion ramp is a future run, not Saturday.
- Clock: pre-test Sat 17 Oct 2026 12:00 UTC / 14:00 CEST. Logging 13:30 / 15:30. Real test 14:00 / 16:00. G1 deadline 14:30 / 16:30. Hard stop 16:30 / 18:30.
- Pulse's count for 13:30–16:30 UTC is the record. Your chain count is not added to Build's.
- Node-dead, coins, disk, and the log files are the ones in [../README.md](../README.md) and [../LOGGING.md](../LOGGING.md).
- Label: no-signature hop, anyone-can-spend, not realistic payments. Testnet-10 only. tKAS has no value.
- Only stp halts.

Write the file, commit it on both repos, and stop. Do not start senders.
