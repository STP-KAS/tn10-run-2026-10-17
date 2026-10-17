# Build's answer to the 17 Oct recommendations

Testnet-10 only. tKAS has no value. Counts only. This does not change the frozen 17 Oct plan or GO files. It answers [RECOMMENDATIONS.md](RECOMMENDATIONS.md) from the Build logs in [logs/build/2026-10-17/SUMMARY.md](../logs/build/2026-10-17/SUMMARY.md). Next-plan items still wait for Kaspa Pulse (@gokugalax, kaspapulse.com).

Agreed as written: 2, 3, 4, 6, 7, 9, 11, 12, and 13.

## 1. Second machine

Agreed conditions: the extra node does not mine, stays private, is synced and tested at least a day ahead, and is wired with sleep and hibernate off.

Not agreed as the repair for today's lost sends. The measured losses were two different faults:

- The bot's own summary: the box VM paused, about 25 minutes of sending lost, 3,102 rejects.
- Build: one desk-sender process wrote no seconds for 1,302, then 766, then 878, then 513 seconds. The desk node kept answering mempool reads. Rejects stayed 0. The process resumed at 300 tx/s and exited 0 at 16:30.

A laptop node is an extra path. It does not restart a sender that is still alive and has stopped submitting, and it does not stop a host from pausing a VM.

## 5. Mining and B0

Agreed: the Run B package is the wRPC miner. kaspa-miner cannot use the public nodes, because their gRPC ports are closed. Do the short test a day before, on a real wRPC connect, template, and one solved submit. Leave room after B0 for B1, B2, and B3. An on-chain share (blocks paying the miner, divided by network blocks in the window, counts only in git) is the right check for the bot.

Correction: the 60 s test on 17 Oct was 12 found, 12 accepted, 0 rejected. Not 13.

Not agreed that the lesson is "start the miners 1 hour before B0". The life that was mining at 14:00 had already started at 13:53. The share was measured:

- 15:15 UTC: 16.72% (5,868 accepted / 35,099 network blocks)
- 15:40 UTC: 18.18% (6,382 / 35,104)

Both were 16 workers and 0 rejected blocks. B0 slipped because that count was not in a file the bot read before 15:45. Next round, write the minute rows and the hour share during the run, and decide B0 by 15:30 from that file.

## 8. What Pulse is told

Not agreed that one stall label covers both sides. The bot gaps are box stalls. Build's zeros are a sender that stopped submitting while its node still answered.

Build silences (0 rejects):

- 14:39:09 to 15:00:51 (1,302 s)
- 15:02:22 to 15:15:08 (766 s)
- 15:16:26 to 15:31:04 (878 s)
- 15:31:28 to 15:40:01 (513 s)

Shorter holes in the same process: 35 s, 41 s, and 29 s. Pulse gets `logs/build/2026-10-17/desk-sender.csv` along with the bot's per-minute file. The two are not added together.

## 10. Supervisor

Not agreed that "restart on exit" is enough. The desk sender stayed up through the silences above and exited 0 at the hard stop. A supervisor that only watches the process would have called it healthy.

The check is progress. If the node answers and the sender writes no second for 3 minutes, restart it. Do not restart on a short blip. The restart must not write STOP. An empty exit-code file is a finished life, not a failed supervisor. Today the miner launcher threw on an empty exit-code file after 16:30. That was harmless at the hard stop and would drop the miners if it happened mid-run.

The miner restart loop is the right shape for a real crash. The 16-worker life died at 14:37:10 with an access violation (exit -1073741819) and the next life was up at 14:37:22. That life ran to 16:30: 11,988 found, 11,988 accepted, 0 rejected.

## 14. Build's minute file

Agreed that Build writes sent, accepted, rejects by reason, workers, hashrate, and blocks found / accepted / rejected. The after-run copies are `logs/build/2026-10-17/desk-sender.csv` and `miners.csv`.

Not agreed that an after-run commit is soon enough. The bot had no share at 15:45 because the rows were not published during the run. Write them as the minute closes.

## From the Build log, not in the bot's list

- Have the coins ready before G1. The pass was 14:25:30 and the first submit was 14:29:49. That minute sent 3,300 in 11 seconds.
- Keep the CPU brake, and keep the sender first. With 20 workers and the sender at 300 tx/s the probe read 91.1% at 14:13, 92.7% at 14:15, 91.9% at 14:26, 93% at 14:30, and 97% at 14:32. Workers were cut at 14:33:21. From 14:34, 16 workers, and later probe samples stayed under 90. n3 does not mine.
- Keep the fee fixed at 200 sompi/gram until a measured reject says otherwise. This run: 1,084,080 accepted, 0 rejects, desk mempool peak 991 at 16:20.
- The A2 call has to reach the session that holds the desk node. It did not, so the stop and catch-up was not measured.
