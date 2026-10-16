> **DRAFT, NOT ARMED.** Experimental. Testnet-10 only. tKAS has no value. [Rule book](README.md).

# LOGGING: same files, same columns, both sides

Copied from the [11 Oct logging spec](https://github.com/STP-KAS/tn10-storm-2026-10-11/blob/main/go/LOGGING.md) and the [10 Oct logging spec](https://github.com/STP-KAS/tn10-storm-2026-10-10/blob/main/go/LOGGING.md). Saturday changes: step names are the target gates, each side has one fee, the bot has no node, and Run A adds a fee file.

## Format

- CSV, UTF-8, comma, one header row, LF line ends.
- UTC, ISO 8601 with `Z`. A second is `2026-10-17T14:00:05Z`. A minute is `2026-10-17T14:00Z`, the same shape as Kaspa Pulse's CSV.
- `side` is `bot` or `build`.
- `step` is `PREP`, `G1`, `A1`, `A2`, `A3`, `A4`, `B0`, `B1`, `B2`, `B3`, or `COUNT`.
- An empty cell means **not measured**. Never write 0 for a read that did not happen. A sender that was ordered to hold writes `sent` 0, and that 0 is measured.
- Log the clock offset at the start and at the end (`ntp.csv`).
- Counts only in git. No txid, key, seed, host IP, port, or wallet address. The public node names are allowed.
- Logging runs from 13:30 UTC to the hard stop, even when a side sends nothing. The chain count runs after the hard stop.

## Files

| File | Written | Who | Answers |
|---|---|---|---|
| `sec.csv` | every second | both | What the senders did |
| `minute.csv` | every minute | both | The row that lines up with Pulse |
| `fee-reads.csv` | every minute, per node | bot: five public nodes. Build: the desk node | The Run A question |
| `public-nodes.csv` | every minute, per public node | both, for nodes they submitted to | Who took, refused, or failed |
| `rejects.csv` | every second with a reject | both | Why a submit failed |
| `miner.csv` | every 10 s | both. Bot stays `off`. | The one miner |
| `host.csv` | every 10 s | both | Is the machine the limit? |
| `node.csv` | every 10 s | Build only | The desk node. The bot has no node file. |
| `events.csv` | each event | both | What happened, in order |
| `steps.csv` | when a step ends | both | One row per step |
| `gates.csv` | each gate call | bot calls. Build writes the desk rows. | Pass, hold, fail, skip |
| `ntp.csv` | start and end | both | Clock offset |
| `chain-count.csv` | after the hard stop | both | 13:30–16:30 UTC, before pruning |
| `confirm.csv` | when a step ends | both | Submit to accept, by the side's fee |
| `SUMMARY.md` | bot every 15 min; Build once after the hard stop | both | Plain words |

A missing `logs/bot/node.csv` is expected. A missing file that this table says the side writes is a hole: say so in `SUMMARY.md`.

Header rows below are the shape. They are not results.

### sec.csv

```
utc,side,step,target_tx_s,senders_live,sent,submit_ok,rejects,node_accepted,mempool_size,sink_lag_s,synced,utxoindex,virtual_daa_score,sink_blue_score,feerate_quote,fee_low,fee_high,node_state
```

`target_tx_s` is 360 while that side should be sending, and 0 while a hold, a node-dead wait, or Run A step A2 (Build only) is on. `fee_low` and `fee_high` are both 400 on the bot and both 200 on Build. `node_state` is `ok`, `lag` (sink lag over 60 s), `dead`, or `syncing`. While the desk node is stopped, Build leaves the node columns empty.

### minute.csv

```
minute_utc,side,step,target_tx_s,sent,submit_ok,rejects,node_accepted,network_unique_accepted,blocks,tx_per_block,our_mined_blocks,mempool_max,sink_lag_max_s,synced_all_minute,cpu_pct_max,ram_free_gb_min,disk_free_gb_min,node_state
```

`network_unique_accepted` is every accepted transaction in that minute, each id once. It is never added across sides. The bot reads it from one named public node. Build reads it from the desk node, and leaves it empty while that node is down.

### fee-reads.csv

One row per node per minute. This is the file Run A is judged from.

```
utc,side,node,synced,sink_lag_s,virtual_daa,priority,normal0,low0,mempool_count,mempool_ready
```

`node` is `desk` or one of the five public names. `side` is who took the read. While the desk node is down, Build writes the minute with the node columns empty and `synced` empty, so the gap is visible.

### public-nodes.csv

```
minute_utc,side,node,submit_ok,rejects,errors,cooled_s,quote_skip
```

`quote_skip` stays 0. A non-zero `quote_skip` is a `deviation`.

### rejects.csv

```
utc,side,step,reason,count
```

`reason` is one of: `fee-too-low`, `mempool-full`, `orphan`, `double-spend`, `mass`, `rpc-timeout`, `rpc-closed`, `other`.

### miner.csv

```
utc,side,miner_state,threads,hashrate_hs,blocks_found,blocks_accepted,blocks_rejected
```

Bot: `miner_state` `off`, `threads` 0, for the whole run. Build: `on` through Run A, `off` during B2, `on` again from B3, and `on` at the hard stop.

### host.csv

```
utc,side,cpu_pct,kaspad_cpu_pct,ram_free_gb,kaspad_rss_gb,disk_free_gb,datadir_gb,net_in_kbs,net_out_kbs,peers_out,senders_up
```

The bot leaves `kaspad_cpu_pct`, `kaspad_rss_gb`, and `datadir_gb` empty.

### node.csv

Build only.

```
utc,side,synced,utxoindex,sink_lag_s,virtual_daa_score,sink_blue_score,header_count,block_count,mempool_size,kaspad_uptime_s,pruning_point_changed
```

### events.csv

```
utc,side,step,event,detail
```

| Event | When |
|---|---|
| `gate-pass` `gate-hold` `gate-fail` `skip` | the bot calls a gate |
| `node-stop` `node-restart` `node-synced` | Run A, with the UTC second |
| `ibd-start` `ibd-end` | only if the desk node's own log shows IBD |
| `node-dead` `node-back` | a public node is cooled, or the desk node fails a health read |
| `node-fallback` | Build moves relief senders while the desk node is up and cannot hold the rate |
| `miner-off` `miner-on` | Run B, and the hard stop if the miner is turned back on |
| `sender-start` `sender-relaunch` `sender-pause` `sender-stop` | sender life |
| `fix` | a problem found and fixed without advancing a gate |
| `disk-guard` | a disk limit acts |
| `coin-refresh` | coins added, amount only, no address |
| `halt` | stp's halt |
| `deviation` | anything not in this plan |

`fee-sit-out` and `node-quote-skip` are not in the plan. If they happen, log them as `deviation`.

### steps.csv

```
step,side,start_utc,end_utc,target_tx_s,mean_sent,mean_submit_ok,mean_node_accepted,rejects,zero_seconds,fee_low,fee_high,clean,reason
```

`clean` is `yes` only when that step's target in the rule book was met. The reason column says why a step ended on a safety cap.

### gates.csv

```
utc,side,gate,synced,utxoindex,sink_lag_s,public_nodes_up,result
```

`result` is `pass`, `hold`, `fail`, or `skip`, plus a short reason.

### ntp.csv

```
utc,side,when,offset_ms,source
```

`when` is `start` or `end`.

### chain-count.csv

Per minute, 13:30–16:30 UTC. Written after the hard stop, before pruning.

```
minute_utc,side,network_unique_accepted,accepted_tx_per_s,blocks,our_mined_blocks,complete_minute
```

Do not add the two sides. Minutes the desk node was down are empty on Build's file. The bot's file is the public-node read for those minutes. Pulse's file is the record.

### confirm.csv

```
step,side,fee_tier,n,p50_s,p90_s,max_s
```

`fee_tier` is 400 for the bot and 200 for Build. `n` is a sample count, not a list of transactions.

### SUMMARY.md

The bot's live summary, every 15 minutes from 13:30 UTC: current step and its target, last complete minute, submit against accepted, rejects by public node, fee reads, desk-node state as Build reported it, coins left as an amount, disk and RAM, last events. After the hard stop, replace it with the final summary. Build writes one summary after the hard stop: peak and median for its own sends, the fee path through A1–A4, the miner path through B, and every `deviation`.

## Words

- **sent**: the sender called submit. **submit_ok**: the node took it. A failed submit is a reject.
- **node_accepted**: this side's own transactions seen accepted on the node it reads.
- **network_unique_accepted**: all accepted transactions in that minute, each id once. The headline. Never summed across sides.
- **our_mined_blocks**: blocks the one desk miner found. The bot's column stays empty.

## How to read it

| Question | Open |
|---|---|
| Did the fee drift after the catch-up? | `fee-reads.csv` for A3 and A4, desk against the five public nodes |
| Did the desk node enter IBD? | `events.csv` `ibd-start` / `ibd-end`. No such row means it did not, and the write-up says so. |
| What did the network accept? | `chain-count.csv`, then Pulse's count |
| Did Run B run? | `gates.csv` row B0. A `skip` means the share was under 15%. |
| Which public node failed? | `public-nodes.csv`, `events.csv` |
| Was the machine the limit? | `host.csv` |
| What happened, in order? | `events.csv` |
