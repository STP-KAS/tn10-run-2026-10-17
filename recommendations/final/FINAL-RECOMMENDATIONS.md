# Final recommendations for the next TN10 round (Sun 25 Oct 2026)

> **Status: Final v2 (base): TN10 ops + Build checked, stp approved; to be fine-tuned after Kaspa Pulse's logs and answer.** F1–F18 agreed by both (Build check in 3aedd30). F19 decided by stp (17 Oct, 19:46 CEST). O1–O3 resolved with Build's answers. O4 decided by stp (17 Oct, 19:47 CEST). Merged from the bot's [RECOMMENDATIONS.md](../RECOMMENDATIONS.md) and Build's answer [RECOMMENDATIONS-BUILD.md](../RECOMMENDATIONS-BUILD.md). Both files stay as they are. Evidence: [bot SUMMARY](../../logs/bot/2026-10-17/SUMMARY.md) and [Build SUMMARY](../../logs/build/2026-10-17/SUMMARY.md) with `desk-sender.csv` and `miners.csv`.

**Next:** compare with Kaspa Pulse's own counts once they come in, then fine-tune this file before the Fri 23 Oct rehearsal.

Testnet-10 only. tKAS has no value. Counts only. This does not change the frozen 17 Oct plan or GO files. Every item goes into the 25 Oct plan only once Kaspa Pulse (@gokugalax, kaspapulse.com) agrees.

**Labels:** **Agreed (both)** = the bot and Build both hold this position. **Bot-only** = the bot proposes it and Build hasn't answered yet. **Build-only** = Build proposes it; the bot's view is noted. `(was #n)` refers to the bot's numbering in RECOMMENDATIONS.md.

## Last adjustments from Build's counts

| | 17 Oct, from the logs |
|---|---|
| Bot sender (5 public nodes) | 2,254,489 sent, 2,251,387 accepted, 3,102 rejects (3,068 from box-freeze recoveries) |
| Build desk sender (desk node) | 1,084,080 sent, 1,084,080 accepted, 0 rejects; first submit 14:29:49Z; 3,614 logged seconds at a mean of 299.97 tx/s; 83 of 151 minutes with no logged second; desk mempool peak 991 at 16:20Z |
| Bot mempool peak (public nodes) | 1,979 at 16:02Z |
| The two senders | Kept separate, never added together (different nodes, different counting) |
| Miners | 20 workers at 3.78–3.86 MH/s to 14:33:21Z, then 16 workers at 2.96–3.26 MH/s (median 3.25) to 16:30Z; 16,255 found, 16,254 accepted, 0 rejected |
| Run B share (Build) | Two numerators, see O1. Probe method: **16.72 % at 15:15Z** (5,868 / 35,099) and **18.18 % at 15:40Z** (6,382 / 35,104). Minute rows: 6,126 accepted for 14:15–15:14Z and 6,328 for 14:40–15:39Z. Both numerators are above 15 % of about 35,100 network blocks. |
| B0 | **Would have passed.** The 16:00Z skip happened because the share was not in a file the bot could read by 15:45Z, not because it was too low. (The bot's other skip reason, too little time before the hard stop, followed from that delay.) |
| Run A | G1 passed; both sides sent at their fixed fee with 0 fee or mempool-full rejects. **A2/A3/A4 were never applied**, so the stop-and-catch-up part of Run A was not measured. |
| Correction to the bot's earlier summary | At 16 workers the miner rate was about 3.25 MH/s, not 3.7–3.9 (that was the 20-worker rate). |

## Items

### F1. Second machine: ASUS UX501V laptop as our own TN10 public node, running 24/7. Agreed (both); decided by stp (was #1)

**Build: agree.** It is an extra path, not the repair for 17 Oct: the bot lost sends in VM pauses, and the desk sender logged 1,084,080 accepts and 0 rejects with 83 minutes that have no second.

- **Decided by stp:** a separate machine, the **ASUS UX501V laptop** (it needs a new 19 V 6.32 A 120 W charger; the tower makes odd noises), runs **only the TN10 public node**. It runs **continuously, 24/7, not just on run days**, so it stays synced and ready.
- **24/7 settings:** never sleep; nothing happens on lid close; battery charge limit around 60–80 % if the laptop supports it; wired through a **USB-Ethernet adapter** (no RJ45 port); hibernate off.
- **Hardware:** SSD with at least 100 GB free; 16 GB RAM preferred.
- Synced and tested at least a day before the run. The node itself never mines. For Run B it serves the miners' block templates over gRPC through a tunnel (**see F19**), so its uptime is part of Run B. Kept private: wRPC on the LAN or behind a tunnel, with no IP, port or tunnel URL in git.
- **Adopted from Build:** it is an **extra independent path**, not the repair for 17 Oct's lost sends. Those were two different faults: the box VM pauses (bot, F2) and the desk sender going silent (Build, F10). A node machine fixes neither of them. The public nodes stay in the plan.

### F2. The bot sender doesn't run on the box VM alone. Agreed (both) (was #2)

**Build: agree.** The 3,102 bot rejects, 3,068 of them on freeze recovery, are the bot's log; Build's desk sender had 0 rejects.

Run it on stp's machines or the laptop, or keep the box as a backup sender only. On the box, the keep-awake watcher is a mitigation, not a fix: freeze 5 hit with it running.

### F3. Keep the node-dead fix: cool-off plus a fresh healthy read. Agreed (both) (was #3)

**Build: agree.** The desk sender never hit that deadlock (0 rejects); the bot's cool-off plus a fresh read is the fix to keep.

### F4. Spread lanes by independent backend, not by hostname. Agreed (both) (was #4)

**Build: agree.** Build's miner used the spare hosts and that does not prove they are separate send backends, so the 30-minute check stays.

There were 3 backends on 17 Oct (muon, vector, and electron/neutrino/boson). Add proton-10 and alpha-10 only after a 30-minute independence check.

### F5. Run B mining. Agreed (both), Build's version adopted (was #5)

**Build: agree.** The mining life that covered 14:00Z had started at 13:53Z, and the 15:15Z and 15:40Z shares were already measured; B0 missed them because they were not in a file the bot read (O1).

- **Primary Run B mining is now F19:** kaspa-miner.exe over gRPC to our own laptop node through a tunnel (decided by stp).
- **Fallback:** the **Node wRPC SDK miner on the public nodes**, since kaspa-miner can't use the public nodes (gRPC closed). Keep it packaged and test it **a day before** too, on the real path (F11). n3 never mines.
- **Adopted from Build:** the bot's "start miners at least 1 hour before B0" is dropped. On 17 Oct the miners were running from 13:53Z and the share was measured (16.72 % / 18.18 %). The real failure was that the share never reached the bot.
- **Write the minute rows and the hour share to a file during the run** that the bot can read (F14). Count the share on-chain; the bot checks it independently from a node where it can. **Decide B0 by 15:30Z** from that file (for a 15:45Z B0 slot).
- Leave room after B0 for B1, B2 (up to 25 min) and B3 before the hard stop.
- Keep a restart loop around the miner (it died with an access violation at 14:37:10Z and was back at 14:37:22Z). An empty exit-code file counts as a finished life, not a crash.

### F6. Fee-read gaps on single nodes are minor, but log the reason. Agreed (both) (was #6)

**Build: agree.** The desk fee was fixed at 200 sompi/gram and fee-too-low rejects were 0; an empty read still needs a reason so it is not taken for a node drop.

### F7. Re-test the pre-check routine end to end the day before. Agreed (both) (was #7)

**Build: agree.** The slow coin read and the closed-gRPC reading were both found on run day, not the day before.

### F8. What Pulse is told: two separate labels. Agreed (both), Build's version adopted (was #8)

**Build: agree.** The labels stay separate: in the closed minutes inside Build's four long silences the miners still accepted 1,956, 1,272, 1,587, and 897 blocks.

- **Bot gaps = box stalls** (host paused the VM): 13:37:56–13:45:30 (pre-run), 14:21:31–14:26:10, 14:48:07–14:58:08, 15:03:34–15:07:14, 15:12:35–15:15:53, 16:22:09–16:23:30 UTC.
- **Build gaps = the desk sender stopped submitting while its node still answered** (0 rejects): 14:39:09–15:00:51, 15:02:22–15:15:08, 15:16:26–15:31:04, 15:31:28–15:40:01, plus 35 s, 41 s and 29 s holes.
- Pulse gets `logs/bot/2026-10-17/pulse-minute.csv` and `logs/build/2026-10-17/desk-sender.csv`. The two are not added together. Also tell Pulse that electron, neutrino and boson look like one backend.

### F9. A stall kill must not write STOP. Agreed (both) (was #9)

**Build: agree.** Only an operator STOP may block a restart; the miner's empty exit-code throw after 16:30Z is the separate bug in F5 and F10.

Only an operator STOP blocks restarts.

### F10. Supervisor: watch progress, not just the process. Agreed (both), Build's version adopted (was #10)

**Build: agree.** The desk sender stayed up through the silences and exited 0 at 16:30:00Z, so a watchdog that only sees process exit would have called it healthy.

- **Adopted from Build:** a process-only supervisor would have called the desk sender healthy through about 59 minutes of silence. The check is progress: if the node answers and the sender writes no second for **3 minutes**, restart it. Don't restart on a short blip. A restart never writes STOP. An empty exit-code file is a finished life.
- Applies to every lane runner: bot sender, Build desk sender, miners, and any other lane runner.
- **Bot addition, confirmed by Build's O3 answer:** the watchdog runs **outside** the process it watches, and outside any session or VM that can be paused. A watchdog inside a stuck or paused process won't fire.

### F11. The node-availability check tests the real path. Agreed (both) (was #11)

**Build: agree.** Closed gRPC and a plain HTTPS check both read the public nodes as down; the wRPC miner then found blocks and had 0 rejected on the Windows 60 s test (O2).

wRPC connect, sync, `getBlockTemplate`, and one solved-block submit. Not gRPC ports, and not HTTP/2 curl.

### F12. Every helper gets the sender's hard stop. Agreed (both) (was #12)

**Build: agree.** Build's desk sender and miners both stopped at 16:30:00Z; the bot's fee reader and keepalive ran about 5 minutes past that.

The bot's fee reader and keepalive ran about 5 min past 16:30Z.

### F13. chain-count, steps and confirm files, and the ntp end row, written automatically. Agreed (both) (was #13)

**Build: agree.** Build's 17 Oct folder has the sender and miner minute files and the summary, and it has no chain-count, steps, confirm, or ntp end row.

### F14. Build's minute file, written live as each minute closes. Agreed (both), Build's version adopted (was #14)

**Build: agree.** `desk-sender.csv` and `miners.csv` were written after the hard stop, so they could not feed the 15:45Z B0 call.

Fields: sent, accepted, rejects by reason, workers, hashrate, blocks found / accepted / rejected, and the rolling hour share. Write each row as its minute closes, where the bot can read it, not only in an after-run commit. That is what B0 needed on 17 Oct.

### F15. Coins read before G1. Agreed (both) (was Build-only)

**Build: agree.** G1 was applied at 14:25:30Z and the first submit was 14:29:49Z; that minute sent 3,300 in 11 seconds.

The G1 pass was at 14:25:30Z; the desk sender's first submit was 14:29:49Z, after the coin read. Finish the coin read before G1.

### F16. CPU brake: sender first. Agreed (both) (was Build-only)

**Build: agree.** At 20 workers the probe read 91.1 % at 14:13Z, 92.7 % at 14:15Z, 91.9 % at 14:26Z, 93 % at 14:30Z, and 97 % at 14:32Z; workers were cut at 14:33:21Z and later samples at 16 workers stayed under 90 %.

20 workers plus 300 tx/s held the desk CPU at 91–97 % (14:13–14:32Z). 16 workers kept it under 90 % with the sender at 300 tx/s. Plan for 16 workers on that PC, or move the miners to a machine that doesn't also send.

### F17. Keep the fee fixed until a measured reject says otherwise. Agreed (both) (was Build-only)

**Build: agree.** Desk fee 200 and bot fee 400 both had 0 fee-too-low rejects; the desk mempool peak was 991 at 16:20Z.

Desk sender 200, bot 400, both with 0 fee-too-low rejects on 17 Oct.

### F18. The A2 call has to reach the session that holds the desk node. Agreed (both) (was Build-only)

**Build: agree.** Build's log has no A2 or A3 acknowledgement, and the desk node was not stopped, so the catch-up was not measured.

A2/A3 were never applied on 17 Oct, so Run A's stop-and-catch-up was not measured. For 25 Oct: the bot writes the FOR-BUILD block to a file Build's session polls, and Build writes an acknowledgement row. A step counts as applied only once it has been acknowledged.

### F19. Run B mining back to normal: kaspa-miner over gRPC to our own laptop node through a tunnel. Agreed; decided by stp (17 Oct, 19:46 CEST)

- **Run B miners use kaspa-miner.exe over gRPC** to the 24/7 ASUS UX501V laptop node (F1), reached **through a gRPC tunnel** (the same kind as our bore tunnels).
- **Fallback:** the wRPC SDK miner on the public nodes (F5).
- **n3 still never mines.**
- **Expose only the gRPC port** through the tunnel. Never put tunnel addresses, hosts or ports in git.
- **Test a day before the run:** first check that the laptop node is synced, then test-mine through the tunnel for 60 s. Pass = all found blocks accepted, 0 rejected. Use the real path: a gRPC template from the laptop node and a solved-block submit through the tunnel (F11).
- Keep F16 (CPU brake: sender first) and the restart loop with the progress watchdog (F5, F10) for kaspa-miner.
- **Benefit:** the bot's B0 cross-check can read accepted blocks directly from our own node, using the O1 definition. That mostly removes the O4 problem.
- **Caveat, honestly:** miners on our own node are less independent of our own stack than the public nodes are. The laptop's uptime, its network link and the tunnel all become part of Run B. If the laptop node or the tunnel drops before or during Run B, switch to the F5 fallback and log the switch. Don't fall back to n3.

## Open items (all resolved)

### O1. Hour-share numerator. Resolved (Build's answer)

5,868 and 6,382 are not the minute-row sums. They are the miner's own accepted counter (`ok`) on a probe taken about every 2 minutes, differenced between probes. A drop in that counter (a new miner life) added nothing for the new life until the next probe.

- Hour to 15:15Z: baseline probe 14:13:20Z (`ok` 2,521, `blockCount` 1,316,027), last probe 15:13:46Z. Non-negative `ok` deltas sum to **5,868**. Two counter resets in that span were skipped. `blockCount` rose by **35,099**. That is 16.72 %.
- Hour to 15:40Z: baseline probe 14:39:14Z (`ok` 157, `blockCount` 1,331,362), last probe 15:39:41Z. No reset. Deltas sum to **6,382**. `blockCount` rose by **35,104**. That is 18.18 %.

`miners.csv` sums accepted blocks on closed minutes: **6,126** for 14:15–15:14Z and **6,328** for 14:40–15:39Z. The 15:15Z gap (6,126 − 5,868 = 258) is the blocks the probe method dropped across the two resets. The 15:40Z gap is 54 and is the different edge (14:39:14–15:39:41 versus the closed minutes 14:40–15:39). Putting 6,126 and 6,328 over the probe's 35,099 and 35,104 mixes those edges, so 17.45 % and 18.03 % are not a matched share.

**Fixed definition for B0, decision minute T:**

- Numerator: sum of `blocks_accepted` for the 60 closed minutes `[T−60, T−1]` in the live miner file. A counter reset inside the hour counts the new life's accepted blocks. This is the `miners.csv` rule.
- Denominator: `blockCount` on one public node at the close of minute `T−1`, minus `blockCount` on that same node at the close of minute `T−61`. If either read is missing, the share is not measured.
- The 17 Oct probe figures stay as history. They are not the 25 Oct gate.

### O2. 60 s test count. Resolved (Build's answer)

Yes. Build's **12 found / 12 accepted / 0 rejected** is the Windows test with **1 worker for 60 s** (logged 60.014 s, 294,555 H/s) on one public node. The bot's **13 / 13 / 0** is the earlier test on the box, before that package was run on Windows. Both stand, each for its own test.

### O3. Desk-sender silences. Resolved (Build's answer)

Yes, on 17 Oct the desk sender was a process started inside the agent tool session, not a service. No, the logs do not show that session paused or throttled. Through the four long silences the miners, started in the same session, kept accepting blocks (1,956, 1,272, 1,587, and 897 in the closed minutes inside those gaps), and the desk mempool reads kept changing. A paused session would have stopped those too.

The resumes at 15:15:08Z, 15:31:04Z, and 15:40:01Z sit next to other work in that session. That fits a sender whose own loop was stuck, not a host pause of the whole machine. It is not the same fault as the bot's VM pauses (F2).

Still run the sender as its own OS process, and keep the 3-minute progress watchdog outside that process (F10). A watchdog inside the stuck process will not fire.

### O4. Who measures the share for B0. Resolved: YES, decided by stp (17 Oct, 19:47 CEST)

The bot's proposal stands: Build's live file (F14), using the O1 definition, is the primary source, and the bot cross-checks from a node.

**stp's decision: YES.** Build's live share file (F14, O1 rule) is enough to pass B0 when the bot's cross-check is not ready. Record the missing cross-check in `gates.csv` and the events log, and don't fail the gate for it. With F19 the bot can count the miners' accepted blocks directly from our own laptop node, so the cross-check should normally be available.

**Build's view (matches the decision):** a share in that live file is enough to pass B0 when the bot's cross-check is not ready. Record the missing cross-check. Do not fail B0 for it. On 17 Oct the share was already above 15 % and the skip happened because the bot could not see the file.
