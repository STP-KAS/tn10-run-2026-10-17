# tn10-run-2026-10-17: FROZEN after Pulse agreement 16 Oct

> **FROZEN after Pulse agreement 16 Oct.** Final version for Sat 17 Oct 2026: logging 13:30 UTC, start 14:00 UTC, hard stop 16:30 UTC. Modest Run A and Run B, about 600 tx/s total. Agreed with Kaspa Pulse (@gokugalax, kaspapulse.com) on 16 Oct. Fri 16 Oct evening is cancelled. Changes after this are logged as `deviation`.

| Folder / link | What is there |
|---|---|
| [plan/PLAN.md](plan/PLAN.md) | Full plan for Sat 17 Oct 2026, same text as the plan repo |
| [plan/TN10-17-Oct-2026.pdf](plan/TN10-17-Oct-2026.pdf) | Plan sheet: what, why, how. Pre-test and real test in UTC and local time |
| [go/README.md](go/README.md) | Shared rule book, in the 10 Oct and 11 Oct shape |
| [go/LOGGING.md](go/LOGGING.md) | Same log columns for both sides |
| [go/AFTER-RUN.md](go/AFTER-RUN.md) | Chain count and summaries after the hard stop |
| [go/build/GO-BUILD.md](go/build/GO-BUILD.md) | Build's GO. It stands alone. It does not start the run |
| [go/bot/REVIEW-GO.md](go/bot/REVIEW-GO.md) | Review for the bot. Not a GO. The bot agrees or disagrees |
| [go/bot/PROMPT-GO-BOT.md](go/bot/PROMPT-GO-BOT.md) | Used only after stp accepts the review. The bot then writes GO-BOT.md |
| [logs/build/precheck-2026-10-16.md](logs/build/precheck-2026-10-16.md) | Build desk pre-check, 16 Oct 2026 |
| [go/stp/GO-STP.md](go/stp/GO-STP.md) | stp's go sheet: stp gives the GO and the stop. The KaChat and KNS app checks are done by hand by Kaspa Pulse (@gokugalax, kaspapulse.com); [app-checks.csv](go/stp/app-checks.csv) is an optional log |
| [tn10-storm-2026-10-11](https://github.com/STP-KAS/tn10-storm-2026-10-11) | Last successful storm: [go/ rules](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/README.md), [CHANGES-DURING-RUN](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/logs/bot/CHANGES-DURING-RUN.md), [COMBINED](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/logs/COMBINED.md) |
| [tn-master](https://github.com/STP-KAS/tn-master) | [FINDINGS](https://github.com/STP-KAS/tn-master/blob/main/FINDINGS.md) · [ADVICE](https://github.com/STP-KAS/tn-master/blob/main/ADVICE-TO-DEVELOPERS.md) · [LESSONS](https://github.com/STP-KAS/tn-master/blob/main/docs/LESSONS.md) |
| [rusty-kaspa PR #1147](https://github.com/kaspanet/rusty-kaspa/pull/1147) · [PR #1148](https://github.com/kaspanet/rusty-kaspa/pull/1148) | Upstream context, link only |
| [tn10-lowio-1148-notes](https://github.com/STP-KAS/tn10-lowio-1148-notes) | Our notes on #1148 (not used Saturday) |

## Intro

**FROZEN after Pulse agreement 16 Oct.** Experimental, Testnet-10 only, tKAS has no value. Two short TN10 runs on Saturday 17 Oct 2026, one question each, modest load, not a storm. This follows the advice of Kaspa Pulse (@gokugalax, kaspapulse.com): short runs with one clear question, announced a day ahead with a UTC window. Results will be counts only.

## Why targets, not clock times

- **Fixed time slots haven't worked.** [tn10-storm-2026-10-10](https://github.com/STP-KAS/tn10-storm-2026-10-10) ran clock-stepped 15-minute steps. Its best step median was about **2,642 tx/s**, below the 3,000 line, and steps moved on before the network had reached the state we wanted to measure.
- **Fixed targets have proven themselves.** [tn10-storm-2026-10-11](https://github.com/STP-KAS/tn10-storm-2026-10-11) ran to one target: **3,107 tx/s** median, and Kaspa Pulse (@gokugalax, kaspapulse.com) counted **163 minutes above 3,000**.
- **Method:** Grok keeps going and fixing until the step's target is met; only then does the next step start. Safety caps only hold or stop a step, never advance it.

## Method

**Each step has a target. The next step starts only when that target is reached.** If not, the step holds and retries within its safety cap, or the run stops and the reason is logged. No time-based auto-advance anywhere. Details: [plan/PLAN.md](plan/PLAN.md#method-target-gated-like-11-oct).

## Anchors

| Anchor | UTC | Brussels (CEST) |
|---|---|---|
| Announcement in TN10 channels | Fri 16 Oct 14:00 | Fri 16 Oct 16:00 |
| Pre-test. Read the GO. Be ready. Do not send. | Sat 17 Oct 12:00 | 14:00 |
| Logging and fee reads start | Sat 17 Oct 13:30 | 15:30 |
| **START**, Run A (if gate G1 reached) | **14:00** | **16:00** |
| Kaspa Pulse's counting window | 13:30-16:30 | 15:30-18:30 |
| Hard stop (safety cap) | 16:30 | 18:30 |

- **Run A, fees after a resync (#1147):** baseline → n3 off until ≥9,000 blocks behind → resync, IBD yes/no logged → watch until back within 2x for 10 min (floor-drift rule, mempool count logged).
- **Run B, a large miner drops out:** only if our hashrate share is ≥15% → baseline → the one desk miner off until block rate settles → back on until within 5% (likely skipped: n3 never mines in Run A).
- Load about 600 tx/s at fixed fees, no climbs, no quote-based skipping. Bot about 300 tx/s, Build about 300 tx/s.
- 11 Oct rules kept: node-dead rule, disk guards, freeze restart, count soon after the end, leak-scan, only stp halts. The one desk miner is off through Run A (n3 never mines). The bot has no miner.
