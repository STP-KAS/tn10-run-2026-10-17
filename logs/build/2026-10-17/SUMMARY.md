# Build side: TN10 run Sat 17 Oct 2026

Testnet-10 only. tKAS has no value. Times are UTC. Counts only: no addresses, txids, IPs, seeds, or keys.

Window: logging from 13:30, Run A start 14:00, hard stop 16:30. The desk sender and the public miners both stopped at 16:30:00.

## Files

| File | What it is |
|---|---|
| `desk-sender.csv` | One row per minute, 14:00 through 16:30. `sent` and `accepted` are submits the desk node took. `seconds_logged` is how many seconds of that minute the sender actually wrote. |
| `miners.csv` | One row per minute, same window. Workers, mean hashrate, and blocks found, accepted, and rejected in that minute. |

An empty cell would mean not measured. Every minute in this window has a count. `seconds_logged` 0 means the sender wrote no second that minute, and the minute counter also recorded 0 submits.

`accepted` here is submits the desk node accepted. It is not the network-wide unique count, and it is not added to the bot's count.

## Totals, desk sender

One sender process. Fee fixed at 200 sompi/gram on both tiers. Desk node only. First submit 14:29:49. Last second 16:30:00, exit 0.

| | Count |
|---|---|
| Sent | 1,084,080 |
| Accepted | 1,084,080 |
| Rejects | 0 |
| Logged seconds | 3,614 |
| Mean on those seconds | 299.97 tx/s |

Logged mass was 1,690 grams on every second, so the fee was 338,000 sompi per accepted submit. Across 1,084,080 accepts that is 3,664.1904 tKAS.

Rejects by reason are all 0: orphan, double-spend, mempool-full, fee-too-low, mass, rpc-timeout, rpc-closed, other.

14:00 through 14:28 are real zeros. The sender was on hold for the G1 call, then in the coin read. G1 pass was applied at 14:25:30. The coin read finished at 14:28:02. The first submit was 14:29:49 (that minute: 3,300 sent, 11 seconds).

57 minutes have at least 55 logged seconds (1,014,300 sent). 11 minutes are partial because they sit on a gap or on the hard stop. 83 minutes have `seconds_logged` 0.

Two logged seconds were 240 tx/s instead of 300: 15:00:51 and 15:31:04. Every other logged second was 300.

## Desk mempool

Peak minute-max was **991 at 16:20** (17,700 sent that minute). Median minute-max across minutes that logged at least one send second was 754. Median across all 151 minutes, including the idle ones, was 91. These are the desk node's own mempool. There was no mempool-full reject.

## Gaps inside the one sender

The process stayed up and the desk node kept answering mempool reads. The sender wrote no seconds in these gaps, then resumed with 0 rejects.

| From | To | Seconds |
|---|---|---|
| 14:37:43 | 14:38:18 | 35 |
| 14:39:09 | 15:00:51 | 1,302 |
| 15:01:15 | 15:01:56 | 41 |
| 15:02:22 | 15:15:08 | 766 |
| 15:15:32 | 15:16:01 | 29 |
| 15:16:26 | 15:31:04 | 878 |
| 15:31:28 | 15:40:01 | 513 |

From 15:40:01 through 16:30:00 the second log is continuous. About 3,564 seconds of the send window are those gaps. Logged seconds are 3,614 of the 7,211 seconds from the first submit to the hard stop.

## A2 / A3

No A2 or A3 step was applied. The desk node was not stopped and was not started again. The fee stayed 200. There is no A2 hold and no A4 resume on this side.

## n3 lag and the CPU brake

n3 did not mine. One desk node stayed up through the hard stop. Miners used public nodes only.

Virtual DAA was sampled 70 times from 14:00 to 16:30. The public read minus the desk read stayed between -4 and +1. None of those samples were unsynced. No sink-lag-over-60s event was recorded. A2, which is the step that stops the desk node, did not run, so there was no planned catch-up lag.

CPU brake: with 20 public workers and the desk sender at 300 tx/s, probe samples were 91.1% (14:13), 92.7% (14:15), 91.9% (14:26), 93% (14:30), and 97% (14:32). The 20-worker process was stopped at 14:33:21. 16 workers were up at 14:34:04. Later probe samples in the same series stayed under 90. The sender was left at 300.

## Miners

Public wRPC workers. Not the desk node.

| Stretch | Workers | Blocks found | Accepted | Rejected | Hashrate |
|---|---|---|---|---|---|
| 14:00 to 14:33:21 | 20 | 3,955 | 3,955 | 0 | minute mean 3.78 to 3.86 MH/s, median 3.82 |
| 14:34:04 to 14:37:05 | 16 | 312 | 311 | 0 | new process, then it died |
| 14:37:22 to 16:30:00 | 16 | 11,988 | 11,988 | 0 | after 14:40, minute mean 2.96 to 3.26 MH/s, median 3.25 |

Minute rows sum to 16,255 found, 16,254 accepted, 0 rejected. The one-block gap is the 14:37 life: it exited with 312 found and 311 accepted, so one found block was not accepted before the process died. The rejected counter stayed 0. A found count and an accepted count in `miners.csv` can also differ by one inside a minute when the accept lands in the next minute. The life totals above are the check.

Share, accepted blocks divided by all network blocks in the prior hour:

- 15:15: 16 workers, 3,210,398 H/s, found 3,746, accepted 3,746, rejected 0. Hour 5,868 / 35,099 = **16.72%**.
- 15:40: 16 workers, 3,264,357 H/s, found 6,569, accepted 6,569, rejected 0. Hour 6,382 / 35,104 = **18.18%**.

No B2 or B3 step was applied. Miners stayed on until the 16:30 hard stop. The last life stopped itself there.

## What broke

- The desk sender went silent for 22 minutes, then 13, then 15, then 9, inside one process. It resumed at 300 tx/s with 0 rejects. Those minutes are zeros in `desk-sender.csv`, not guessed fills.
- The first 16-worker life died at 14:37:10 after 196 seconds with an access violation (exit -1073741819). A new 16-worker life was up at 14:37:22 and ran to the hard stop. An earlier 20-worker life had died the same way at 13:52:57, before this window, and had already been restarted.
- After the hard stop the miner launcher threw because the exit-code file was empty. The miners had already stopped. It did not start another generation.
- G1 was in hand at 14:25:30 and the first submit was still 14:29:49, because the coin read is slow.

## Deviations

- Sends did not start at 14:00. They started at 14:29:49, after the G1 paste and the coin read.
- Worker count was 20, then 16, on public wRPC. The frozen text said 15 miners on one public node. n3 still never mined.
- A2, A3, B2, and B3 were not run. No call for those steps was applied here.

## Recommendations for the next round

- Deliver the A2 call into the session that is holding the desk node. This run never stopped that node, so Run A's stop-and-catch-up was not measured.
- Finish the coin read before G1, so the first minute after the pass is a real 300 tx/s minute.
- If the desk sender writes no second for about 3 minutes while the node is still answering, restart it. A restart costs another coin read, so a short blip should not restart it. A 10 to 20 minute silence should.
- Keep the sender ahead of the miners. 20 workers plus 300 tx/s held the box over 90%. 16 workers kept the sender at 300. Do not mine on n3.
- Keep a restart loop around the wRPC miner. It can die with an access violation and an empty error log. Teach the loop to treat an empty exit-code file as a finished life, so a clean hard stop is not reported as a crash.
- Put the hour share where the other side can read it before 15:45. The measured shares were 16.72% at 15:15 and 18.18% at 15:40, both above 15%, with 0 rejected blocks.
- Leave the fee fixed. 200 sompi/gram produced 0 fee rejects, and the desk mempool peaked at 991.
