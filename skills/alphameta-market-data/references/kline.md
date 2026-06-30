# K-Line — Candlestick / OHLCV

Historical candlesticks and today's intraday minute curve for US equities and options via AlphaMeta (IBKR).

## Symbol Format

Stocks: plain ticker (`AAPL`, `NVDA`, `SPY`). Options are not supported for k-line.

## Modes

| Mode | Command | Use When |
|---|---|---|
| Latest N candles | `kline <SYMBOL> <PERIOD> <COUNT> [adj]` | "last 100 daily bars", "近一周走势" |
| History by date range | `kline <SYMBOL> <START> <END> <PERIOD>` | "NVDA 2024年日K", "Jan-Jun 2024" |
| Intraday today | `kline <SYMBOL> intraday` | "分时图", "today's intraday" |

Always discover current flags first:

```bash
curl "http://localhost:18080/api/v1/search?query=kline"
```

## Period Mapping

| Period | Aliases | Bar Size |
|---|---|---|
| `1m` | `minute` | 1 minute |
| `5m` | — | 5 minutes |
| `15m` | — | 15 minutes |
| `30m` | — | 30 minutes |
| `1h` | `hour` | 1 hour |
| `day` | `d`, `1d` | 1 day |
| `week` | `w` | 1 week |
| `month` | `m`, `1mo` | 1 month |

## Workflow

1. **Resolve symbol** — plain ticker
2. **Determine mode** — intraday vs history vs latest N
3. **Map time windows** — use trading-day counts:
   - "1-week" → `day, 5`
   - "1-month" → `day, 22`
   - "3-months" → `day, 66`
   - "6-months" → `day, 126`
   - "1-year" → `day, 252`
   - "today / intraday" → intraday mode
4. **Execute** via POST /api/v1/execute
5. **Interpret** — translate OHLCV to prose

## CLI Examples

```bash
# Latest 100 daily bars
curl ... -d '{"cmd": "kline AAPL day 100"}'

# Last 200 5m candles with split+dividend adjustment
curl ... -d '{"cmd": "kline NVDA 5m 200 adj"}'

# History by date range (daily)
curl ... -d '{"cmd": "kline AAPL 2025-01-01 2025-06-30 day"}'

# Today's intraday 1m chart
curl ... -d '{"cmd": "kline NVDA intraday"}'
```

## Output

The API returns `result.bars[]` with fields: `date`, `open`, `high`, `low`, `close`, `volume`, `average`.

**Intraday**: `date` is ISO 8601 datetime; no `adjust` field.

Always present as prose:

```
**NVDA — Daily (5 bars, May 6–12)**
Range: $196.16 ~ $223.75  |  Close-to-close: $207.26 → $219.48 (▲ +5.9%)
Volume: avg 126M shares/day — elevated throughout
Trend: Strong uptrend from $196 support, 5 consecutive green candles.
```

## Key Behaviours

- **Intraday is today-only** — for historical minute data use history mode with `1m` period
- **`adj` flag** — split + dividend adjusted prices (volume is never adjusted)
- **All modes include extended hours** — `useRTH=False` is hardcoded on the backend
- **Count is a minimum** — IBKR may return more bars than requested for week/month periods
- **History may include one extra bar** — IBKR includes the bar containing the start timestamp

## Error Handling

| Situation | Reply |
|---|---|
| Service not running | Start the service: `alphameta --ibkr` |
| `error.code == "COMMAND_ERROR"` | Surface `error.message` verbatim |
| No data for the range | Expand the date range or switch to a shorter period |
| Empty intraday for past date | Intraday is today-only; use `kline <SYMBOL> <START> <END> 1m` instead |
