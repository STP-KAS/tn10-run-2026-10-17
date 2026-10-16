> **FROZEN after Pulse agreement 16 Oct.** Experimental. Testnet-10 only, tKAS has no value.

# GO-STP: stp's personal go sheet, Saturday 17 Oct 2026

Part of [plan/PLAN.md](../../plan/PLAN.md). Same method as the run: **check rounds are tied to targets and step changes, not to the clock.**

## Purpose

stp tests **KaChat** and **KNS** by hand on TN10 during the run, to confirm or deny the claim that they "go down" under load (transactions stuck in the mempool). The result feeds the "Why we storm TN10" statement. **Nothing is published until it is confirmed** by these tries matched against our counts.

## Prep (before 13:30 UTC / 15:30 CEST)

- [ ] Testnet wallet funded with a **small** tKAS amount (address kept out of git).
- [ ] KaChat and KNS open, **testnet (TN10) selected** in both. Check twice: no mainnet.
- [ ] Device clock synced (NTP); you'll need the UTC second.
- [ ] Log open: copy [app-checks.csv](app-checks.csv) or keep a note in the same columns.

## Check rounds (target-based)

| Round | When (trigger, not a clock time) |
|---|---|
| **Baseline** | Before START (14:00 UTC / 16:00 CEST): one KaChat message + one KNS action, with normal load only |
| **Step changes** | At each Run A / Run B step change (gate pass in `SUMMARY.md` / events: A1→A2, A2→A3, A3→A4, B0→B1, B1→B2, B2→B3, END) |
| **During load** | About every 10 min between step changes, while load is on |

Each round = **one KaChat message + one KNS action**.

## What to record per try

| Column | What |
|---|---|
| `utc` | UTC second you pressed send (e.g. `2026-10-17T14:12:05Z`) |
| `app` | `kachat` or `kns` |
| `action` | e.g. `message`, `register`, `transfer` |
| `submitted` | `y` / `n` (did the app accept it?) |
| `error` | Error text exactly as shown, or empty |
| `confirmed_utc` | UTC second it showed as confirmed, or empty |
| `seconds_to_confirm` | Seconds from `utc` to `confirmed_utc` |
| `note` | e.g. `stuck after 5 min`, current step, screenshot taken |

Screenshot optional; if you take one, crop out any address or txid. **No addresses, seeds or txids in the log.**

## Stop rules

- **Only stp halts** the run (11 Oct halt order).
- Small amounts only; never top up beyond the prep amount.
- **No mainnet**, ever. If an app shows mainnet, stop testing that app.
- A stuck try is a result, not a problem to fix: log `stuck after N min` and keep going with the next round.

## After the run

We match each try to the mempool and accepted counts (`fee-reads.csv`, `minute.csv`, `blocks-10s.csv`, Kaspa Pulse's counts) by UTC second. Only if stuck tries line up with load does the claim count as confirmed, and then the "Why we storm TN10" line is drafted for stp's OK.
