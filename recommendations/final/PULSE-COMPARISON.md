# Kaspa Pulse count vs our logs, 13:30–16:30 UTC, 17 Oct 2026

Kaspa Pulse (@gokugalax, kaspapulse.com) shared his count of accepted transactions on TN10 for the run window. This file sets it next to our own logs. All times UTC.

## Pulse's count

- 13:30–16:30: 180 minutes, all complete; both of his nodes agree in every minute
- 3,968,327 accepted transactions, 367.4 tx/s on average
- Best minute: 666.6 tx/s at 16:18
- Base load before the run: 55.5 tx/s
- Our two sender gaps show up in his data as base load

## The dips he asked about

| Dip (UTC) | Bot sender | Desk sender (Build) | Reading |
|---|---|---|---|
| 15:03–15:07 | VM paused by host 15:03:34–15:07:14 | stuck in its own loop 15:02:22–15:15:08 | both down: base load plus a little recovery traffic |
| 15:12–15:16 | VM paused 15:12:35–15:15:53 | only short bursts: 7,500 at 15:15, 7,800 at 15:16 | mostly base load |
| 16:22–16:23 | VM paused 16:22:09–16:22:51 | sending normally | about 300 tx/s down, not down to base load |

## The plateau around 650 tx/s from 15:40

Bot and Build together, each about 300 tx/s, until the stop at 16:30.

- 15:40–16:29 accepted: bot 875,666, desk 890,400
- Excluding 16:22–16:23: 596.8 tx/s combined, plus 55.5 base load ≈ 652 tx/s

## Totals

| | Accepted |
|---|---|
| Bot | 2,251,387 |
| Desk (Build) | 1,084,080 |
| Ours combined | 3,335,467 |
| Base load, 180 min × 55.5 tx/s | 599,400 |
| Ours + base load | 3,934,867 |
| Pulse | 3,968,327 |
| Not placed | about 33,460 (≈3.1 tx/s, ≈0.8 %) |

The unplaced part could be other traffic, or base load running a bit above 55.5 tx/s.

At 16:18 Pulse's best minute was 666.6 tx/s; ours was 595.0 tx/s, so about 72 tx/s that minute was not ours.

## Notes

- "Accepted" in our files means submits the node took, not unique transactions confirmed on chain. A few duplicates can be in there.
- Re-checked against `logs/bot/2026-10-17/pulse-minute.csv` (submit_ok) and `logs/build/2026-10-17/desk-sender.csv` (accepted). Our totals above include the final stop minute 16:30 (bot 28, desk 300). Counting 13:30–16:29 only, the minute files give bot 2,251,359 and desk 1,083,780. The difference doesn't change the comparison.
- With the addresses we shared, Pulse will also check whether his data shows Build's share for the day.
