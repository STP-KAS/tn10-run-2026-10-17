# Recommendations from the TN10 run of Sat 17 Oct 2026

From the Sat 17 Oct 2026 run. Testnet-10 only. The evidence is in [logs/bot/2026-10-17/SUMMARY.md](../logs/bot/2026-10-17/SUMMARY.md). None of this changes the frozen 17 Oct plan or GO files. Each item goes into a run plan only once Kaspa Pulse (@gokugalax, kaspapulse.com) agrees.

## 1. stp's decision: a second machine (ASUS UX501V laptop) with its own TN10 public node, "share the load"

**Decided by stp on 17 Oct 2026, 18:35 CEST.** stp will start a second machine running its own TN10 public node to share the load, and keep it running from now on.

**Machine (stp's choice, 17 Oct): the ASUS UX501V laptop**, picked over the tower because the tower makes odd noises. It only needs a new charger (19 V, 6.32 A, 120 W).

Conditions:
- **ASUS UX501V specifics:** SSD with at least 100 GB free; 16 GB RAM preferred; a **USB-Ethernet adapter** for the wired link, since the laptop has no RJ45 port. Keep it on the charger and set it to never sleep, never sleep with the lid closed, and never hibernate.
- **Always-on:** wired network, SSD, 16 GB+ RAM (preferred), about 100 GB free disk.
- **Synced and tested before any run.** Several of the earlier problems came from nodes that had not finished syncing.
- **Sends and monitors only; does not mine** (the same rule as n3).
- **Kept private:** its wRPC stays on the LAN or behind a tunnel. No IP, port or tunnel URL goes into git.
- **An extra, independent path.** The public nodes stay in the plan; this node adds to them and does not replace them.
- **Goes into the plan only after Kaspa Pulse agrees.**

Why: today the five public nodes were about three independent backends (electron, neutrino and boson look like one). A fault there would take out most of the sending paths at once. Our own node on separate hardware is a truly independent path, and it is not exposed to the box VM pauses (item 2). Once it is up, the bot can check from outside that it stays synced, so it is proven well before any run.

## 2. Don't run the bot sender on the box VM alone

The box VM was paused by its host five times during sending: about 25 minutes of sending lost and 3,068 of the 3,102 rejects. The keep-awake watcher reduced the pauses but did not prevent the 5th one (16:22Z, which came with host memory reclaim). Run the bot's sender on stp's machines or the new desktop, or keep the box as a **backup sender only**. If the box does send, keep the watcher active nonstop until the hard stop, and label every gap as a box stall.

## 3. Keep the node-dead fix

A dead node comes back after a **cool-off plus a fresh healthy read**. Never use an error counter that only resets on recovery: that deadlocked all five nodes after freeze 1 and cost about 4.7 min of sending. Keep a test for this: simulate a 2-minute stall and check that all nodes come back with no restart.

## 4. Spread lanes across independent backends

Today, "five nodes" were about three backends (muon, vector, electron/neutrino/boson). Spread lanes by **backend**, not by name: count an alias group as one path. Add the spares **proton-10** and **alpha-10**, but only after checking that they are independent, i.e. that their DAA score, mempool and fee estimate differ from the others over at least 30 minutes of reads.

## 5. Mining for Run B

- Use the **wRPC SDK miner on the public nodes** (kaspa-miner can't connect because gRPC is closed on them). The new node is an option only if Pulse agrees, and n3 never mines.
- **Make the Node wRPC SDK miner the standard Run B package** (the one Build used today; a 60 s test gave 13 of 13 blocks accepted). Don't plan on kaspa-miner for public nodes. **Do the 60 s test mine before any run**, not on run day: today it was found and packaged with only a few hours to go.
- **Measure the share on-chain** (blocks paying the miner's own TN10 address ÷ all blocks in the window, counted from a node, with only counts in git), so that B0 can be verified instead of depending on a reported number.
- **Start the miners at least 1 hour before B0**, so the share has a full window.
- **Decide B0 with time to spare:** measure the share on-chain from 15:00Z and decide by 15:30Z (for a 15:45Z B0 slot). Today there was no measured share at 15:45Z and the call slipped to 16:00Z.
- **Plan enough time after B0 for B1, B2 (up to 25 min) and B3 before the hard stop.** Today B0 slipped to 16:00Z and there was no room left.

## 6. Fee-read gaps on single nodes: minor, but log why

Per-node fee reads were sometimes empty (29 to 37 per node in the run window) while the sender still used the node. Treat these as minor, but log a reason per empty read (timeout, closed, not synced) so a read gap can't be mistaken for a node drop.

## 7. Re-test the pre-check routine end to end

Today's pre-check and logging routine was cut off before it finished (the sender, fee reads and hard stop survived, but one hand-off signal had to be sent by hand). Run the whole routine once end to end before any run, on the machine that will actually send.

## 8. For Kaspa Pulse

- Send the per-minute CSV (`logs/bot/2026-10-17/pulse-minute.csv`).
- Give the freeze gaps a clear label: 14:21:31–14:26:10, 14:48:07–14:58:08, 15:03:34–15:07:14, 15:12:35–15:15:53 and 16:22:09–16:23:30 UTC are **bot box stalls, not network drops**. No rows means not measured.
- Mention that electron, neutrino and boson look like one backend, so counts per node are not independent.

## 9. A stall kill must not write STOP

Only an operator STOP may block restarts. Today the KNS lanes' shutdown handler wrote STOP after the 15:03Z box freeze set off its stall kill, and that blocked their own auto-restart for the rest of the run.

## 10. Every lane runner needs a supervisor

- **Every lane runner needs a supervisor** (bot sender, KNS lanes, Build's senders and miners, and any other lane runner) that restarts it after a box freeze or an unexpected exit, and logs the exit code. The supervisor checks for an operator STOP before each restart and honours the hard stop.

## 11. Test node availability on the real path

The node check must test what is actually used: a wRPC connect, sync status, wRPC `getBlockTemplate`, and for mining one solved-block submit. Closed gRPC ports or an HTTP/2 curl result don't count. Today, an HTTP/2 curl test against the websocket gave a false 502 ("down"), and closed gRPC ports were first read as "public nodes can't be mined". In fact every node answered over wRPC, and solved blocks were accepted. Those false reads cost hours on run day.

## 12. Every helper gets the sender's hard stop

The fee reader, the keepalive watcher and the loggers all take the same hard-stop time as the sender and stop themselves at it (plus at most a short, fixed tail for a final read). Today the sender stopped at 16:30:00Z, but the fee reader kept reading the public nodes until about 16:35Z and the keepalive loop ran until 16:35Z, on separate timers.

## 13. Generate chain-count, steps and confirm files automatically

`chain-count.csv`, `steps.csv` and `confirm.csv` (shapes in LOGGING.md) were never produced today, and the `ntp.csv` end row is missing. Write `steps.csv` and `confirm.csv` from the sender as each step ends, add the `ntp.csv` end row at the hard stop, and run `chain-count.csv` by script right after the hard stop. Don't rely on doing it by hand after the run.

## 14. Build logs his own desk sender and miner numbers each minute

The bot has no data on Build's desk sender or his miners, so today's summary only has his reported numbers. Build writes one row per minute to a file: sent, accepted, rejects by reason, miner workers, hashrate, and blocks found / accepted / rejected. Counts only, no addresses. The file goes into `logs/build/` after the run.

