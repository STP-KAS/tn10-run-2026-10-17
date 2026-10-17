# Final report, TN10 run Sat 17 Oct 2026

> **Final report, TN10 run Sat 17 Oct 2026. Build reviews this on Tuesday.**

Testnet-10 only. tKAS has no value. Counts only: no addresses, txids, IPs, seeds or keys. All times UTC unless marked CEST (UTC+2). The details are in [FINAL-RECOMMENDATIONS.md](FINAL-RECOMMENDATIONS.md), the [bot SUMMARY](../../logs/bot/2026-10-17/SUMMARY.md) and the [Build SUMMARY](../../logs/build/2026-10-17/SUMMARY.md). The earlier files [RECOMMENDATIONS.md](../RECOMMENDATIONS.md) and [RECOMMENDATIONS-BUILD.md](../RECOMMENDATIONS-BUILD.md) are kept as they were.

Window: logging from 13:30Z, sends from 14:00Z, hard stop 16:30Z. All senders and miners stopped at the hard stop.

## 1. Results

### Bot sender (five public nodes, fee 400, target 300 tx/s)

Summed from `logs/bot/2026-10-17/pulse-minute.csv`, which matches the two sender processes' end events added together.

| | Count |
|---|---|
| Sent | 2,254,489 |
| Accepted | 2,251,387 (83.4 % of the 2,700,000 target) |
| Rejects | 3,102: rpc_closed 2,039, orphan 864, double_spend 100, rpc_timeout 99; fee, mempool-full, mass and other all 0 |
| From freeze recoveries | 3,068 of the 3,102 |
| Peak mempool (public nodes) | 1,979 at 16:02Z |

### Build (desk node and miners)

From `logs/build/2026-10-17/`.

| | Count |
|---|---|
| Desk sender | 1,084,080 sent, 1,084,080 accepted, 0 rejects, fee 200. First submit at 14:29:49Z, 3,614 logged seconds at a mean of 299.97 tx/s. In 83 of 151 minutes no second was logged, because the sender loop got stuck (Build's O3 answer). |
| Desk mempool peak | 991 at 16:20Z |
| Miners (public nodes, wRPC) | 20 workers at 3.78–3.86 MH/s until 14:33:21Z, then 16 workers at a median of 3.25 MH/s; 16,255 found, 16,254 accepted, 0 rejected |
| Share (Build's probe method) | 16.72 % at 15:15Z, 18.18 % at 15:40Z |

The two senders are counted separately and not added together.

### KNS (high level only)

The KNS lanes ran from 14:15:20Z to 15:03:34Z. They stopped early when the 15:03Z box freeze set off their stall kill, and the shutdown handler then wrote a STOP file that blocked the restart. 797 of 801 attempts were confirmed. There were 11 rejects (6 RPC timeouts on their side, whose names landed anyway, and 5 orphans), and 0 fee, mass or double-spend rejects. The full results are kept privately.

### Box freezes

Six stalls of the bot's box, all paused from outside by the hosting platform (F27). They are not network drops.

| # | Stall (UTC) | Effect |
|---|---|---|
| pre | ~13:37:56–13:45:30 | none (sender on hold) |
| 1 | ~14:21:31–14:23:47 | ~4.7 min lost (the node-dead bug kept sending at 0 until 14:26:10), 948 rejects |
| 2 | ~14:48:07–14:58:08 | ~10.6 min lost, 530 rejects |
| 3 | ~15:03:34–15:07:14 | ~4.3 min lost, 530 rejects; KNS lanes stopped here |
| 4 | ~15:12:35–15:15:53 | ~4.0 min lost, 530 rejects |
| 5 | ~16:22:09–16:22:51 | ~1.4 min lost, 530 rejects; the keep-awake watcher was active |

That's about 25 min of bot sending lost in total. The node-dead fix at 14:26Z let all nodes recover on their own after freezes 2–5.

### B0 and Run B

B0 **would have passed**: Build's measured share was 16.72 % and 18.18 %, both above 15 %. TN10 ops called it **skip** at 16:00Z because the share never reached the bot before 15:45Z, and by then there wasn't enough time left before the hard stop. Run B did not run. Run A ran on both sides, but **A2/A3/A4 were never applied**, so the stop-and-catch-up part was not measured.

### Data gaps

- No rows during the box freezes; those minutes are not measured.
- Build's desk sender logged no seconds in 83 minutes.
- `chain-count.csv`, `steps.csv`, `confirm.csv` and the `ntp.csv` end row were not produced. Build's `FOR-TN10-OPS-result-1017.txt` was not received.
- No measured on-chain share from the bot side; only Build's.
- Some per-node fee reads were empty (minor).
- Kaspa Pulse's own counts are not in yet. Next: compare with them once received, then fine-tune.

## 2. Recommendations F1–F29

The short form is below. The full text, which is the reference, is in [FINAL-RECOMMENDATIONS.md](FINAL-RECOMMENDATIONS.md). O1–O4 there are all resolved.

| # | Recommendation | Status |
|---|---|---|
| F1 | Our own node: ASUS UX501V laptop as a TN10 public node, running 24/7 | Agreed (both); decided by stp (was #1) |
| F2 | The bot sender doesn't run on the box VM alone | Agreed (both) (was #2) |
| F3 | Keep the node-dead fix: cool-off plus a fresh healthy read | Agreed (both) (was #3) |
| F4 | Spread lanes by independent backend, not by hostname | Agreed (both) (was #4) |
| F5 | Run B mining | Agreed (both), Build's version adopted (was #5) |
| F6 | Fee-read gaps on single nodes are minor, but log the reason | Agreed (both) (was #6) |
| F7 | Re-test the pre-check routine end to end before any run | Agreed (both) (was #7) |
| F8 | What Pulse is told: two separate labels | Agreed (both), Build's version adopted (was #8) |
| F9 | A stall kill must not write STOP | Agreed (both) (was #9) |
| F10 | Supervisor: watch progress, not just the process | Agreed (both), Build's version adopted (was #10) |
| F11 | The node-availability check tests the real path | Agreed (both) (was #11) |
| F12 | Every helper gets the sender's hard stop | Agreed (both) (was #12) |
| F13 | chain-count, steps and confirm files, and the ntp end row, written automatically | Agreed (both) (was #13) |
| F14 | Build's minute file, written live as each minute closes | Agreed (both), Build's version adopted (was #14) |
| F15 | Coins read before G1 | Agreed (both) (was Build-only) |
| F16 | CPU brake: sender first | Agreed (both) (was Build-only) |
| F17 | Keep the fee fixed until a measured reject says otherwise | Agreed (both) (was Build-only) |
| F18 | The A2 call has to reach the session that holds the desk node | Agreed (both) (was Build-only) |
| F19 | Run B mining back to normal: kaspa-miner over gRPC to our own laptop node through a tunnel | Agreed; decided by stp (17 Oct, 19:46 CEST) |
| F20 | One shared run clock and gate file | Agreed (stp, 19:47 CEST) |
| F21 | A small count service on our own node | Agreed (stp, 19:53 CEST) |
| F22 | Senders, miners and loggers as self-restarting OS services | Agreed (stp, 19:53 CEST) |
| F23 | Our own node: the laptop only | Agreed (stp, 19:53 CEST; changed by stp, 20:14 CEST) |
| F24 | A simple live status page | Agreed (stp, 19:53 CEST) |
| F25 | Post-run report generated from the logs | Agreed (stp, 19:53 CEST) |
| F26 | The scripts in their own repo | Agreed (stp, 19:53 CEST) |
| F27 | Cause of the box freezes | Agreed (stp, 20:07 CEST) |
| F28 | How the bots work | Agreed (stp, 20:07 CEST) |
| F29 | Box disk | Agreed (stp, 20:07 CEST) |

## 3. All commits in this repo (oldest to newest)

The author field is the same account for every commit. The "By" column comes from the chat record: bot = TN10 ops, Build = Build. "Not recorded" means it wasn't noted at the time. A few messages are shortened so they don't carry future-run dates. The commit that adds this report comes after the last one listed.

| Commit | Time (UTC, CEST) | By | Message |
|---|---|---|---|
| [`c4beb74`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/c4beb74) | 2026-10-12 16:35Z (18:35 CEST) | not recorded | Sat 17 Oct TN10 run plan (DRAFT, NOT ARMED), checked against 11 Oct best practice |
| [`832e4ae`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/832e4ae) | 2026-10-12 16:35Z (18:35 CEST) | not recorded | Drop stale 'not pushed' line |
| [`a6b481b`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/a6b481b) | 2026-10-12 18:46Z (20:46 CEST) | not recorded | Pulse confirmed 14:00 UTC; IBD check, floor-case drift, skip B if share small, lowio later (credited) |
| [`6fd8d4f`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/6fd8d4f) | 2026-10-12 22:31Z (00:31 CEST) | not recorded | Plan: target-gated method like 11 Oct; each step advances only when its target is reached, no time-based auto-advance; anchors 13:30/14:00/16:30 UTC kept |
| [`7242501`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/7242501) | 2026-10-12 22:33Z (00:33 CEST) | not recorded | Add 'Why targets, not clock times' (10 Oct vs 11 Oct) to README and PLAN |
| [`22ada7e`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/22ada7e) | 2026-10-13 06:24Z (08:24 CEST) | not recorded | Add go/stp/GO-STP.md (manual KaChat/KNS checks, target-based rounds) and app-checks.csv; link from README and PLAN |
| [`ca8ad05`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/ca8ad05) | 2026-10-16 07:03Z (09:03 CEST) | not recorded | Nodes 16 Oct: bot on five public nodes only; n3 = stp desk node, non-mining; Build desk node counts our side |
| [`32d92c9`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/32d92c9) | 2026-10-16 07:08Z (09:08 CEST) | not recorded | Revert "Nodes 16 Oct: bot on five public nodes only; n3 = stp desk node, non-mining; Build desk node counts our side" |
| [`986e2d3`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/986e2d3) | 2026-10-16 07:44Z (09:44 CEST) | not recorded | Add the Saturday plan sheet, Build GO, and the bot prompt |
| [`d86cec1`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/d86cec1) | 2026-10-16 07:46Z (09:46 CEST) | not recorded | Add a possible alternative run sheet (not used) |
| [`63fbff2`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/63fbff2) | 2026-10-16 09:33Z (11:33 CEST) | not recorded | Saturday 17 Oct stays the plan. The alternative sheet is not used. |
| [`cb285b7`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/cb285b7) | 2026-10-16 09:44Z (11:44 CEST) | not recorded | Drop the alternative sheet. |
| [`e33e1fa`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/e33e1fa) | 2026-10-16 09:49Z (11:49 CEST) | not recorded | Raise Saturday load 20 percent. |
| [`fcd67a9`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/fcd67a9) | 2026-10-16 09:55Z (11:55 CEST) | not recorded | Write the Saturday GO in the 10 and 11 Oct shape. |
| [`6e91a36`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/6e91a36) | 2026-10-16 11:43Z (13:43 CEST) | not recorded | go: add the target-first operating principle (keep going, but fix first) |
| [`631098f`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/631098f) | 2026-10-16 12:31Z (14:31 CEST) | not recorded | Build GO: congestion ramp from 300 tx/s, miner off for Run A. |
| [`a73b44c`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/a73b44c) | 2026-10-16 13:14Z (15:14 CEST) | not recorded | Freeze the Saturday GO after Pulse agreement 16 Oct: modest Run A/B (~720 tx/s), n3 never mines, B2 pause, ramp marked for later, per-minute CSV for Pulse |
| [`b5eace3`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/b5eace3) | 2026-10-16 13:14Z (15:14 CEST) | not recorded | LOGGING: pulse-minute.csv header matches the sender |
| [`7fff9cc`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/7fff9cc) | 2026-10-16 13:16Z (15:16 CEST) | not recorded | Saturday load 300/300 (~600 tx/s, stp 16 Oct); rebuild plan sheet: Run A/B, miner off in A, Run B likely skipped, B2 pause, per-minute CSV |
| [`5d48a4a`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/5d48a4a) | 2026-10-16 18:12Z (20:12 CEST) | not recorded | Wording: desk miner off through Run A and at the hard stop if Run B is skipped; n3 is Build's one desk node; precheck marked superseded |
| [`63af995`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/63af995) | 2026-10-16 20:02Z (22:02 CEST) | not recorded | App checks: KaChat and KNS by hand by Kaspa Pulse; stp gives GO and stop only; roles table matches n3 = Build's desk node, miner off through Run A |
| [`221d9ad`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/221d9ad) | 2026-10-17 10:34Z (12:34 CEST) | Build | 17 Oct: Run B miners are on a public node. n3 never mines. |
| [`fcbd9f7`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/fcbd9f7) | 2026-10-17 16:39Z (18:39 CEST) | bot | 17 Oct post-test: bot logs, summary, recommendations (second public node) |
| [`3ddd461`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/3ddd461) | 2026-10-17 16:40Z (18:40 CEST) | bot | 17 Oct post-test: KNS high-level result, six stalls, ASUS UX501V node, stall-kill and supervisor recommendations |
| [`b4ba139`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/b4ba139) | 2026-10-17 16:43Z (18:43 CEST) | bot | Recommendations: wRPC miner as standard Run B package, real-path node check, shared hard stop for helpers, auto step/confirm/chain-count files, Build per-minute log, earlier B0 |
| [`85830ac`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/85830ac) | 2026-10-17 16:56Z (18:56 CEST) | Build | Log Build counts for the 17 Oct TN10 run. |
| [`e288e11`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/e288e11) | 2026-10-17 17:15Z (19:15 CEST) | Build | Add Build's answer to the 17 Oct recommendations. |
| [`8a46cdd`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/8a46cdd) | 2026-10-17 17:37Z (19:37 CEST) | bot | Final recommendations draft v1 (merged with Build's answer, Build counts, 24/7 laptop node); bot summary points to Build logs |
| [`3aedd30`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/3aedd30) | 2026-10-17 17:42Z (19:42 CEST) | Build | Build check of the recommendations draft. |
| [`ad21674`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/ad21674) | 2026-10-17 17:48Z (19:48 CEST) | bot | Final recommendations v2 (base): F19 kaspa-miner over gRPC to own laptop node via tunnel; O1-O3 resolved, O4 yes (stp); fine-tune after Kaspa Pulse's counts |
| [`a19c87d`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/a19c87d) | 2026-10-17 17:49Z (19:49 CEST) | bot | Final recommendations: F20 item later removed, F21 shared run clock and gate file (agreed by stp) |
| [`f1079fa`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/f1079fa) | 2026-10-17 17:50Z (19:50 CEST) | bot | Recommendations: remove that item and future-run dates; shared gate file renumbered F20 |
| [`aff5a60`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/aff5a60) | 2026-10-17 17:56Z (19:56 CEST) | bot | Final recommendations: F21-F26 (count service, self-restarting services, two own nodes, live status page, generated report, scripts repo), agreed by stp (F23 later changed to laptop only) |
| [`1c5e964`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/1c5e964) | 2026-10-17 17:57Z (19:57 CEST) | bot | F26: scripts kept in a separate private repo (no name or link) |
| [`ee50e93`](https://github.com/STP-KAS/tn10-run-2026-10-17/commit/ee50e93) | 2026-10-17 18:08Z (20:08 CEST) | bot | Final recommendations: F27 box-freeze cause (platform pauses), F28 how the bots work, F29 box disk; Build reviews on Tuesday |

## 4. Other material

Some notes (the box-freeze write-up and the scrubbed scripts) are kept in a separate private repo.
