# Final recommendations for the next TN10 round (Sun 25 Oct 2026)

> **Status: Draft v1 from TN10 ops, awaiting Build check.** Merged from the bot's [RECOMMENDATIONS.md](../RECOMMENDATIONS.md) and Build's answer [RECOMMENDATIONS-BUILD.md](../RECOMMENDATIONS-BUILD.md). Both files stay as they are. Evidence: [bot SUMMARY](../../logs/bot/2026-10-17/SUMMARY.md) and [Build SUMMARY](../../logs/build/2026-10-17/SUMMARY.md) with `desk-sender.csv` and `miners.csv`.

Testnet-10 only. tKAS has no value. Counts only. This does not change the frozen 17 Oct plan or GO files. Every item goes into the 25 Oct plan only once Kaspa Pulse (@gokugalax, kaspapulse.com) agrees.

**Labels:** **Agreed (both)** = the bot and Build both hold this position (Build: in its answer; bot: in this draft). **Bot-only** = the bot proposes it and Build hasn't answered yet. **Build-only** = Build proposes it; the bot's view is noted. `(was #n)` refers to the bot's numbering in RECOMMENDATIONS.md.

## Last adjustments from Build's counts

| | 17 Oct, from the logs |
|---|---|
| Bot sender (5 public nodes) | 2,254,489 sent, 2,251,387 accepted, 3,102 rejects (3,068 from box-freeze recoveries) |
| Build desk sender (desk node) | 1,084,080 sent, 1,084,080 accepted, 0 rejects; first submit 14:29:49Z; 3,614 logged seconds at a mean of 299.97 tx/s; 83 of 151 minutes with no logged second; desk mempool peak 991 at 16:20Z |
| Bot mempool peak (public nodes) | 1,979 at 16:02Z |
| The two senders | Kept separate, never added together (different nodes, different counting) |
| Miners | 20 workers at 3.78–3.86 MH/s to 14:33:21Z, then 16 workers at 2.96–3.26 MH/s (median 3.25) to 16:30Z; 16,255 found, 16,254 accepted, 0 rejected |
| Run B share (Build) | **16.72 % at 15:15Z** (5,868 / 35,099), **18.18 % at 15:40Z** (6,382 / 35,104) |
| B0 | **Would have passed.** Both shares are above 15 %. The 16:00Z skip happened because the share was not in a file the bot could read by 15:45Z, not because it was too low. (The bot's other skip reason, too little time before the hard stop, followed from that delay.) |
| Run A | G1 passed; both sides sent at their fixed fee with 0 fee or mempool-full rejects. **A2/A3/A4 were never applied**, so the stop-and-catch-up part of Run A was not measured. |
| Correction to the bot's earlier summary | At 16 workers the miner rate was about 3.25 MH/s, not 3.7–3.9 (that was the 20-worker rate). |

## Items

### F1. Second machine: ASUS UX501V laptop as our own TN10 public node, running 24/7. Agreed (both); decided by stp (was #1)

- **Decided by stp:** a separate machine, the **ASUS UX501V laptop** (it needs a new 19 V 6.32 A 120 W charger; the tower makes odd noises), runs **only the TN10 public node**. It runs **continuously, 24/7, not just on run days**, so it stays synced and ready.
- **24/7 settings:** never sleep; nothing happens on lid close; battery charge limit around 60–80 % if the laptop supports it; wired through a **USB-Ethernet adapter** (no RJ45 port); hibernate off.
- **Hardware:** SSD with at least 100 GB free; 16 GB RAM preferred.
- Synced and tested at least a day before the run. It sends and monitors only and **never mines** (like n3). Kept private: wRPC on the LAN or behind a tunnel, with no IP, port or tunnel URL in git.
- **Adopted from Build:** it is an **extra independent path**, not the repair for 17 Oct's lost sends. Those were two different faults: the box VM pauses (bot, F2) and the desk sender going silent (Build, F10). A node machine fixes neither of them. The public nodes stay in the plan.

### F2. The bot sender doesn't run on the box VM alone. Agreed (both) (was #2)
Run it on stp's machines or the laptop, or keep the box as a backup sender only. On the box, the keep-awake watcher is a mitigation, not a fix: freeze 5 hit with it running.

### F3. Keep the node-dead fix: cool-off plus a fresh healthy read. Agreed (both) (was #3)

### F4. Spread lanes by independent backend, not by hostname. Agreed (both) (was #4)
There were 3 backends on 17 Oct (muon, vector, and electron/neutrino/boson). Add proton-10 and alpha-10 only after a 30-minute independence check.

### F5. Run B mining. Agreed (both), Build's version adopted (was #5)
- The standard Run B package is the **Node wRPC SDK miner**, since kaspa-miner can't use the public nodes (gRPC closed). Do the short test **a day before**, on the real path (F11). n3 never mines.
- **Adopted from Build:** the bot's "start miners at least 1 hour before B0" is dropped. On 17 Oct the miners were running from 13:53Z and the share was measured (16.72 % / 18.18 %). The real failure was that the share never reached the bot.
- **Write the minute rows and the hour share to a file during the run** that the bot can read (F14). Count the share on-chain; the bot checks it independently from a node where it can. **Decide B0 by 15:30Z** from that file (for a 15:45Z B0 slot).
- Leave room after B0 for B1, B2 (up to 25 min) and B3 before the hard stop.
- Keep a restart loop around the miner (it died with an access violation at 14:37:10Z and was back at 14:37:22Z). An empty exit-code file counts as a finished life, not a crash.

### F6. Fee-read gaps on single nodes are minor, but log the reason. Agreed (both) (was #6)

### F7. Re-test the pre-check routine end to end the day before. Agreed (both) (was #7)

### F8. What Pulse is told: two separate labels. Agreed (both), Build's version adopted (was #8)
- **Bot gaps = box stalls** (host paused the VM): 13:37:56–13:45:30 (pre-run), 14:21:31–14:26:10, 14:48:07–14:58:08, 15:03:34–15:07:14, 15:12:35–15:15:53, 16:22:09–16:23:30 UTC.
- **Build gaps = the desk sender stopped submitting while its node still answered** (0 rejects): 14:39:09–15:00:51, 15:02:22–15:15:08, 15:16:26–15:31:04, 15:31:28–15:40:01, plus 35 s, 41 s and 29 s holes.
- Pulse gets `logs/bot/2026-10-17/pulse-minute.csv` and `logs/build/2026-10-17/desk-sender.csv`. The two are not added together. Also tell Pulse that electron, neutrino and boson look like one backend.

### F9. A stall kill must not write STOP. Agreed (both) (was #9)
Only an operator STOP blocks restarts.

### F10. Supervisor: watch progress, not just the process. Agreed (both), Build's version adopted (was #10)
- **Adopted from Build:** a process-only supervisor would have called the desk sender healthy through about 59 minutes of silence. The check is progress: if the node answers and the sender writes no second for **3 minutes**, restart it. Don't restart on a short blip. A restart never writes STOP. An empty exit-code file is a finished life.
- Applies to every lane runner: bot sender, Build desk sender, miners, and any other lane runner.
- **Bot addition (see Open O3):** the watchdog has to run **outside** whatever session or VM can be paused. A watchdog that is paused along with the sender won't fire.

### F11. The node-availability check tests the real path. Agreed (both) (was #11)
wRPC connect, sync, `getBlockTemplate`, and one solved-block submit. Not gRPC ports, and not HTTP/2 curl.

### F12. Every helper gets the sender's hard stop. Agreed (both) (was #12)
The bot's fee reader and keepalive ran about 5 min past 16:30Z.

### F13. chain-count, steps and confirm files, and the ntp end row, written automatically. Agreed (both) (was #13)

### F14. Build's minute file, written live as each minute closes. Agreed (both), Build's version adopted (was #14)
Fields: sent, accepted, rejects by reason, workers, hashrate, blocks found / accepted / rejected, and the rolling hour share. Write each row as its minute closes, where the bot can read it, not only in an after-run commit. That is what B0 needed on 17 Oct.

### F15. Coins read before G1. Build-only (bot agrees)
The G1 pass was at 14:25:30Z; the desk sender's first submit was 14:29:49Z, after the coin read. Finish the coin read before G1.

### F16. CPU brake: sender first. Build-only (bot agrees)
20 workers plus 300 tx/s held the desk CPU at 91–97 % (14:13–14:32Z). 16 workers kept it under 90 % with the sender at 300 tx/s. Plan for 16 workers on that PC, or move the miners to a machine that doesn't also send.

### F17. Keep the fee fixed until a measured reject says otherwise. Build-only (bot agrees)
Desk sender 200, bot 400, both with 0 fee-too-low rejects on 17 Oct.

### F18. The A2 call has to reach the session that holds the desk node. Build-only (bot agrees)
A2/A3 were never applied on 17 Oct, so Run A's stop-and-catch-up was not measured. For 25 Oct: the bot writes the FOR-BUILD block to a file Build's session polls, and Build writes an acknowledgement row. A step counts as applied only once it has been acknowledged.

## Open: needs Build/stp decision

- **O1. Hour-share numerator.** Build reports 5,868 accepted for the hour to 15:15Z and 6,382 for the hour to 15:40Z. Summing `miners.csv` gives 6,126 (14:15–15:14Z) and 6,328 (14:40–15:39Z), i.e. 17.45 % and 18.03 % against the same network counts. Either way the share was above 15 %, so B0 would still pass. But the 25 Oct gate needs one fixed definition (window edges, and whether "accepted" means the miner's own counter or an on-chain count). **Build: which source and window gave 5,868 and 6,382?**
- **O2. 60 s test count: 12 or 13?** Build's answer says the 17 Oct 60 s test was 12 found / 12 accepted. The bot's own test on the box (before the zip went to Build) reported 13 found / 13 accepted / 0 rejected, but its raw output was not kept as a file. Possibly two different tests (the bot's box test vs Build's 1-worker Windows test). Minor; both had 0 rejected. **Build: was your 12/12 the Windows 1-worker test?** If so, both numbers stand, each for its own test.
- **O3. Cause of the desk-sender silences.** The process stayed up, the node answered and rejects were 0, which fits a sender that was paused rather than one that failed. Two of the resumes (15:31:04Z and 15:40:01Z) fall within about a minute of other agent activity that day (the bot's 15:31Z and 15:40Z checks). That is a hint, not proof. **Build: does the desk sender run inside an agent or tool session that can be paused or throttled when idle?** If yes, it's the same fault class as the bot's box (F2), and a restart watchdog inside that session won't help (F10). Run the sender as a standalone OS process or service instead.
- **O4. Who measures the share for B0.** The bot proposes that Build's live file (F14) is the primary source and the bot cross-checks from a node. **stp: is a Build-reported share enough to pass B0 if the bot's cross-check isn't ready?**
