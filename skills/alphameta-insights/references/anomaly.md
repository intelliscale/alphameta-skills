# Anomaly / 异动检测

Detects unusual price movements, volume spikes, or abnormal market activity.

## Partial AlphaMeta Coverage

AlphaMeta does not have a dedicated anomaly detection scanner. The closest available signals:

| Signal | AlphaMeta Command |
|---|---|
| Market-level strength signals | `advice` (VWAP distances, EMA crossovers) |
| Capital flow intensity | `capital-flow <symbol>` (MFI, net flow direction) |
| Price and volume | `kline <symbol> day 20` (compare recent bars for volume spikes) |

## Workflow

1. Identify the symbol or market to scan.
2. Use `advice` for broad market signals, `capital-flow` for flow anomalies.
3. Compare recent kline volume against historical average as a simple volume anomaly check.
