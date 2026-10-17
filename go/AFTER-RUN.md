> **FROZEN after Pulse agreement 16 Oct.** Experimental. Testnet-10 only. tKAS has no value. [Rule book](README.md).

# After the hard stop

Hard stop is Sat 17 Oct 2026, 16:30 UTC (18:30 CEST). Senders off. The public-node miners stay off if Run B was skipped, as in GO-BUILD. n3 never mines. The desk node stays up. Do not wipe its data.

## Within the next hour, before pruning

1. `chain-count.csv` for 13:30–16:30 UTC, both sides, columns in [LOGGING.md](LOGGING.md). Empty cells where the desk node was down. Do not add the sides together.
2. `steps.csv` and `confirm.csv` for every step that ran.
3. `ntp.csv` end row.
4. Build writes `FOR-TN10-OPS-result-1017.txt` and gives it to stp. stp passes it to the bot. Keys, one per line:

```
window_utc: 2026-10-17T13:30:00Z/2026-10-17T16:30:00Z
mean_sent: <n>
mean_submit_ok: <n>
rejects: <n>
fee_a1_priority: <n>
fee_a4_priority: <n>
ibd: yes | no
run_b: ran | skipped
miner_back_within_5pct: yes | no | skipped
deviations: <short>
```

5. Bot replaces `logs/bot/SUMMARY.md` with the final summary. Build writes `logs/build/SUMMARY.md`.
6. One page per question. Our numbers beside Pulse's. Counts only. Nothing is published until stp says so.

## Leak-scan before any push

No key, seed, txid, IP, host, port, tunnel URL, wallet address, or local path. Public node names are allowed. No force-push, no tags, no releases.
