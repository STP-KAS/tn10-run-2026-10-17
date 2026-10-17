# Bot side: TN10 run Sat 17 Oct 2026, final summary

Testnet-10 only. tKAS has no value. All times UTC unless marked CEST (UTC+2). Counts only: no addresses, txids, IPs, seeds, keys or tokens are in this folder. The per-transaction log (`tx.jsonl`) stays off git on purpose.

Window: logging 13:30Z, sending 14:00:00Z, hard stop 16:30:00Z. The bot sender stopped itself at the hard stop (end event 16:30:00.208Z).

## Files in this folder

| File | What it is |
|---|---|
| `pulse-minute.csv` | One row per minute, the row for Kaspa Pulse: mempool per public node, submits, rejects by reason |
| `sec.csv` | Sender, every second: target, attempts, submit_ok, rejects, live nodes, ready lanes, free RAM |
| `fee-reads.csv` | Fee estimate and mempool per public node, every minute (13:30:00Z to 16:30:00Z) |
| `events.csv` | Bot events in order (deviations, fixes, incidents, gate notes) |
| `sender-events.jsonl` | The sender's own event stream (start, node-dead / node-back, stop, end totals), counts only |
| `gates.csv` | Gate calls written by the bot (G1) |
| `host.csv` | Box RAM, disk, load and process memory every 10 s |
| `ntp.csv` | Clock offset at the start |
| `live-notes.md` | One line per monitor check during the run |
| `keepalive.log` | The keep-awake watcher, one line per check from 15:18Z |

Holes, as LOGGING.md asks: the bot has no `minute.csv`, `public-nodes.csv`, `rejects.csv`, `steps.csv`, `confirm.csv` or `chain-count.csv` under those names. `pulse-minute.csv` carries the per-minute and reject-by-reason data. `miner.csv` is not written because the bot does not mine. `ntp.csv` has a start row only, no end row. `sec.csv` has fewer columns than the LOGGING.md shape (no per-second node reads; those are in `fee-reads.csv` each minute). Freeze stretches have no rows at all: an empty stretch means not measured, not zero.

## Totals (bot sender, 14:00:00Z to 16:30:00Z)

How computed: sum of every row in `pulse-minute.csv` (161 rows, 13:30Z to the 16:30Z partial row; 13:30 to 13:59Z all have submit_ok 0 because the sender was on hold). Cross-check: the sender ran as two processes. Process 1 (to the restart at 14:26:09Z) ended with 387,727 attempts, 387,299 ok and 428 rejects. Process 2 (14:26:10Z to the hard stop) ended with 1,866,762 attempts, 1,864,088 ok and 2,674 rejects. The two ends added together match the CSV sum exactly. A process-2 end event on its own is not the run total.

| | Count |
|---|---|
| Sent (submit attempts) | **2,254,489** |
| Accepted (submit_ok) | **2,251,387** |
| Rejects | **3,102** (0.14 % of sent) |
| - rpc_closed | 2,039 |
| - orphan | 864 |
| - double_spend | 100 |
| - rpc_timeout | 99 |
| - mempool_full | 0 |
| - fee_too_low | 0 |
| - mass | 0 |
| - other | 0 |
| Fees paid | about 5,790.57 tKAS (996.13 + 4,794.43) |
| Lanes | 530 loaded, 0 dead at the end |

3,068 of the 3,102 rejects (98.9 %) came from resuming after the five box freezes. The other 34 were single rpc_closed rejects spread over six minutes (14:20Z 10, 14:46Z 5, 15:35Z 4, 15:45Z 2, 15:48Z 7, 16:07Z 6). There were no fee, mempool-full or mass rejects at all.

Rate: the target was 300 tx/s, so 2,700,000 in 150 min. The bot got 2,251,387 accepted (83.4 %). The shortfall of 448,613 is the freeze gaps (about 25 min at 300 tx/s). 121 of the 137 minute rows after 14:00Z were full minutes (at least 17,900 ok, normally 17,989 to 18,007).

## Peak mempool

**1,979 at 16:02Z**, verified in `pulse-minute.csv` (the `mempool_max` column; the vector read gave the max that minute). It drained to about 1,000 by 16:05Z. Before that, the highest was 1,947 at 15:01Z.

## Run A, bot side

- G1 passed at 13:59:44Z (`gates.csv`). The sender went from hold to A1 at 14:00:00Z: 300 tx/s, fee 400, five public nodes (muon, electron, neutrino, boson, vector).
- The bot stayed in A1 at 300 tx/s until the hard stop. Outside the freeze gaps it held about 300 tx/s every minute, with 0 rejects in most minutes.
- No rejects for fee or a full mempool. The peak mempool was 1,979.
- Fee reads during the run (`fee-reads.csv`, priority estimate): muon, electron, neutrino and boson stayed at a median of 100, peaking at 432 (muon) and 231 (the other three). Vector read higher throughout: median about 159, peaking at about 350.
- **A2/A3 desk-node steps were Build's and stp's.** The bot has no data on them. The bot logs contain no A2, A3 or A4 rows or events, Build's logs now show that no A2, A3 or A4 step was applied, so Run A's stop-and-catch-up was not measured ([logs/build/2026-10-17/SUMMARY.md](../../build/2026-10-17/SUMMARY.md)).

## Run B

- **B0 = skip**, called by TN10 ops at **16:00:00Z** (B0 was due 15:45Z). Two reasons: the share could not be verified from the bot side, since no blocks could be tied to Build and no fresh share report arrived; and too little time was left before the 16:30Z hard stop for B1, B2 (up to 25 min) and B3.
- For context only, not a measured share: the network hashrate was about 17.6 MH/s (10,000-block window) at 15:59Z. Build's last reported rate of 3.7 to 3.9 MH/s would be about 21 to 22 % if it still held.
- No B2 pause happened.

## Build's miners (as Build reported; not measured by the bot)

- `kaspa-miner` could not use the public nodes: **gRPC is closed on all of them**. They serve wRPC only, and kaspa-miner speaks only gRPC.
- A small Node.js miner using the Kaspa SDK over wRPC (getBlockTemplate + submitBlock) worked. In a 60 s test, 13 blocks were found, 13 accepted and 0 rejected, and they appeared as chain blocks on the other public nodes. An unsolved block was refused as `blockInvalid`, so the nodes check proof of work normally.
- Build ran about 16 to 20 workers on the public nodes (the CPU brake took him to 16 so his 300 tx/s sender came first), at about 3.7 to 3.9 MH/s with 0 rejected blocks, as he reported. One set of workers exited once with no error and was restarted within seconds. n3 never mined.
- **Update from Build's logs** ([miners.csv](../../build/2026-10-17/miners.csv)): 20 workers at 3.78 to 3.86 MH/s until 14:33:21Z, then 16 workers at about 2.96 to 3.26 MH/s (median 3.25 MH/s) to the hard stop. 16,255 found, 16,254 accepted, 0 rejected. The hour share Build measured was 16.72 % at 15:15Z and 18.18 % at 15:40Z, so B0 would have passed (15 % or more) if that number had reached the bot before 15:45Z.

## KNS lanes (high level only; full results kept privately)

- The KNS lanes ran from 14:15:20Z to 15:03:34Z. They stopped early when the 15:03–15:07Z box freeze (stall 3 below) set off their stall kill. Their shutdown handler then wrote a STOP file, and that blocked the auto-restart (a bug on the KNS side).
- 797 of 801 attempts were confirmed. There were 11 rejects: 6 RPC timeouts on their side (those names landed anyway) and 5 orphans. There were 0 fee, mass or double-spend rejects.
- The full KNS results are kept privately. Other details are in a separate private plan.

## Node-dead recovery bug and fix

- Bug: after a box stall, every in-flight submit failed at once, so all five nodes hit the "20 rpc errors" node-dead rule. The health check that should bring a node back also re-checked the same error counter, and that counter only reset on node-back. So a dead node could never come back: a deadlock. After freeze 1, all five nodes stayed dead from 14:23:59Z, and the sender sent nothing until a restart, although the nodes themselves were fine (fee reads read all five at 14:24 to 14:25Z).
- Fix (14:26Z): the error-counter re-check was removed. A node now comes back after a 30 s cool-off plus a fresh healthy read. The sender was restarted at 14:26:10Z with the same cap of 300 and fee of 400. The restart cost 530 one-time orphan rejects (one per lane; each lane resynced once).
- Proof: after freezes 2 to 5, all five nodes came back on their own about 37 to 39 s after resume (30 s cool-off plus a healthy read), with no restart.

## Box freezes: box stalls, not network drops

There were **six stalls in total**: one before the run and five during sending. kns tn10 independently confirmed the pre-run stall at about 13:37–13:45Z. All the gaps below are **stalls of the bot's own computer (the box VM)**. They are not network or public-node drops. During each gap the box wrote no rows at all. When it resumed, the sender's open connections were stale, which caused the rpc_closed / rpc_timeout and orphan rejects. The public nodes kept running throughout. Times come from the `sec.csv` row gaps, and dmesg confirms the resume.

| # | Stall (UTC) | Sending lost | Rejects on resume | Recovery |
|---|---|---|---|---|
| pre | about 13:37:56 to 13:45:30 | none (sender on hold) | 0 | stale connections; reconnect patch, relaunch 13:46:54Z |
| 1 | about 14:21:31 to 14:23:47 (~2.3 min) | ~84,500 submits (~4.7 min), because the node-dead bug kept sending at 0 until 14:26:10Z | 418 (401 rpc_closed, 17 rpc_timeout) + 530 orphan after the restart = 948 | bug fixed, restart 14:26:10Z |
| 2 | about 14:48:07 to 14:58:08 (~10 min) | ~191,600 (~10.6 min) | 530 (401 rpc_closed, 129 orphan) | on its own, nodes back 14:58:46Z |
| 3 | about 15:03:34 to 15:07:14 (~3.7 min) | ~77,100 (~4.3 min) | 530 (401 rpc_closed, 108 orphan, 21 rpc_timeout) | on its own, 15:07:51Z |
| 4 | about 15:12:35 to 15:15:53 (~3.3 min) | ~71,100 (~4.0 min) | 530 (401 rpc_closed, 97 orphan, 32 rpc_timeout) | on its own, 15:16:32Z |
| 5 | about 16:22:09 to 16:22:51 (~42 s; dmesg resume 16:22:50Z, memory reclaim lines from 16:22:53Z) | ~24,300 (~1.4 min) | 530 (401 rpc_closed, 29 rpc_timeout, 100 double_spend) | on its own, 16:23:30Z |

"Sending lost" means 18,000 per minute for the affected minutes minus what was actually accepted in them. Over the run that comes to about 448,600 submits, or about 25 minutes at 300 tx/s. (`live-notes.md` numbers the incidents differently because it counted from the 14:58Z one; this table is the reference.)

**Cause, strongly indicated but not confirmed by the host:** the host pauses the box VM. Evidence: dmesg logs `crng reseeded due to virtual machine fork` at each resume, and freezes 1 to 4 each ended just as a new agent task started on the box, which suggests the VM is paused when no agent is active. At 15:18Z a keep-awake watcher started that kept a task active nonstop. It **reduced** the freezes (none for about 65 min after it started) but **did not prevent** freeze 5 at 16:22Z. That one came with host memory reclaim (`virtio_balloon` fill / "Out of puff" lines, and free RAM falling from about 12.9 GB to about 2.9 GB). The watcher is a mitigation, not a fix.

## Public nodes

- All five nodes were synced on 2.1.0 during the run. Vector was unreachable at the 13:17Z pre-check and back by 13:35:37Z, before sends began.
- **Probably one backend: electron, neutrino and boson.** In `pulse-minute.csv` these three reported identical mempool numbers in 144 of 161 minute rows, and in most per-minute fee reads they gave the same DAA score, mempool and fee estimate. That means about three independent backends (muon, vector, and the electron/neutrino/boson group), not five. Correction to earlier notes: **vector is not part of that group in today's data.** It matched the other three in only 1 of 161 rows, and it usually gave the highest mempool read.
- Fee-read gaps: some per-node fee reads came back empty (29 to 37 per node over 133 run-window minutes). These were read misses in the fee reader only. The sender kept 5/5 nodes live outside the freezes.

## Open items for the after-run

- `chain-count.csv`, `steps.csv`, `confirm.csv` and the `ntp.csv` end row were not produced by the bot today.
- Build's data now exists: [logs/build/2026-10-17/SUMMARY.md](../../build/2026-10-17/SUMMARY.md), `desk-sender.csv` and `miners.csv` (desk sender 1,084,080 sent and accepted, 0 rejects; miners and share above). Build's `FOR-TN10-OPS-result-1017.txt` was not received.
- Recommendations for the next round: [../../../recommendations/RECOMMENDATIONS.md](../../../recommendations/RECOMMENDATIONS.md).
