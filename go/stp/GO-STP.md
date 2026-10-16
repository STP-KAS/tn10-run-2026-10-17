> **FROZEN after Pulse agreement 16 Oct.** Experimental. Testnet-10 only, tKAS has no value.

# GO-STP: stp's go sheet, Saturday 17 Oct 2026

Part of [plan/PLAN.md](../../plan/PLAN.md). Same method as the run: **check rounds are tied to targets and step changes, not to the clock.**

## Purpose

stp gives the **GO** and the **stop** (the only halt). That is the whole sheet for stp.

The KaChat and KNS app checks are done **by hand by Kaspa Pulse (@gokugalax, kaspapulse.com)** (Pulse, 16 Oct: "i do the kachat and kns checks by hand"): baseline before START (14:00 UTC / 16:00 CEST), at each Run A / Run B step change, and about every 10 min while load is on. They confirm or deny the claim that the apps "go down" under load (transactions stuck in the mempool). His results are compared to our counts by UTC second. **Nothing is published until they match and are confirmed.** stp has no app-check task.

## Optional log

[app-checks.csv](app-checks.csv) stays as an optional log where stp can note Pulse's findings or his own tries. Columns: `utc`, `app` (`kachat` or `kns`), `action`, `submitted`, `error`, `confirmed_utc`, `seconds_to_confirm`, `note`. **No addresses, seeds or txids in the log.** Testnet only, never mainnet.

## Stop rules

- **Only stp halts** the run (11 Oct halt order).

## After the run

Pulse's app-check results and any optional notes are matched to the mempool and accepted counts (`fee-reads.csv`, `minute.csv`, `pulse-minute.csv`, Kaspa Pulse's counts) by UTC second. Only if stuck tries line up with load does the claim count as confirmed, and then the "Why we storm TN10" line is drafted for stp's OK.
