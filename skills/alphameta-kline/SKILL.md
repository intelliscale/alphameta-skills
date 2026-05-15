---
name: alphameta-kline
description: "Candlestick / OHLCV data and intraday minute series via AlphaMeta (IBKR REST API). Supports 1m/5m/15m/30m/1h/day/week/month periods, history by date range, and today's intraday curve. Triggers: \"K线\", \"K 线\", \"走势\", \"历史价格\", \"日K\", \"月K\", \"周K\", \"分时图\", \"近一周走势\", \"candlestick\", \"candles\", \"OHLCV\", \"intraday chart\", \"price history\", \"weekly chart\", \"monthly chart\", \"1-year chart\", \"前复权\", \"forward adjusted\"."
---
# AlphaMeta Kline

Fetch candlestick / OHLCV data via Interactive Brokers — latest N bars, history by date range, and today's intraday minute curve. Supports 1m to month periods with split/dividend-adjusted pricing.

## When to Use

- "NVDA 近一周走势" / "AAPL 1-week chart" — daily bars, 5 trading days
- "TSLA 近一个月" / "1-month chart" — daily bars, 22 trading days
- "TSLA 近一年日 K" / "AAPL 1-year chart" — daily bars, 252 trading days
- "TSLA 5 分钟 K 线 200 根" / "last 200 5m candles" — intraday bars with explicit count
- "AAPL 月 K 线" / "MSFT monthly chart" — monthly bars
- "NVDA 分时图" / "AAPL intraday today" — intraday mode
- "AAPL 2024 年日 K" / "price history Jan-Jun 2024" — history by date range
- "前复权日 K" / "forward adjusted chart" — add `adj` flag

## Workflow

1. **Resolve symbol** — use standard ticker (`NVDA`, `AAPL`, `SPY`). AlphaMeta handles contract resolution via its position/quote database.

2. **Determine mode**:
   - Explicit "intraday" or "today" → intraday mode
   - Date-like first argument (YYYY-MM-DD, "today", "yesterday") → history mode
   - Otherwise → latest N candles mode

3. **Map time windows** — translate natural-language ranges to (`period`, `count`). Use trading-day counts (1 year ≈ 252 trading days, 1 month ≈ 22):
   - "1-week" → `day, 5`
   - "1-month" → `day, 22`
   - "3-months" → `day, 66`
   - "6-months" → `day, 126`
   - "1-year" → `day, 252`
   - "today / intraday" → intraday mode

4. **Execute command** via REST API:
   ```bash
   curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
     -H "Content-Type: application/json" \
     -d '{"cmd": "kline <SYMBOL> <ARGS>"}'
   ```
   Concrete examples:
   ```bash
   # Latest 100 daily bars
   curl ... -d '{"cmd": "kline AAPL day 100"}'

   # Last 200 5m candles with split+dividend adjustment
   curl ... -d '{"cmd": "kline NVDA 5m 200 adj"}'

   # History by date range (daily)
   curl ... -d '{"cmd": "kline AAPL 2025-01-01 2025-06-30 day"}'

   # Today's intraday 1m chart (includes pre/after-market data)
   curl ... -d '{"cmd": "kline NVDA intraday"}'
   ```

5. **Interpret results** — summarize the OHLCV data: range high/low, close-to-close net move, volume patterns. Use ▲/▼ for direction. Calculate net move as `(last_close - first_close) / first_close`. Cite data source as AlphaMeta / Interactive Brokers.

## Common Rationalizations

| Rationalization                                    | Reality                                                                                         |
| -------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| "`day 30` is roughly one month"                    | 30 trading bars ≈ 6 calendar weeks. For one calendar month use `day 22`                         |
| "Net move = (last open − first open) / first open" | Always use close-to-close: `(last_close − first_close) / first_close`                           |
| "I'll pass the raw JSON to the user"               | Raw JSON is unreadable. Must translate to prose: range high/low, net change, volume, trend      |
| "Count = calendar days"                            | Count = number of bars, not days. `day 5` = 5 daily bars covering ~7 calendar days              |
| "Intraday works for any past date"                 | Intraday mode only supports today. For historical minute data use history mode with `1m` period |
| "Count is the exact number of bars returned"       | Count is a **minimum** — for longer periods (week/month) IBKR may return more bars than requested |

## Red Flags

- **Intraday is today-only** — calling intraday on a past date returns empty data. For historical minute bars use `kline <SYMBOL> <START> <END> 1m`
- **Data availability varies by instrument** — options, some ETFs, and crypto may return empty or very short histories. Verify with `quote` first
- **All modes use `useRTH=False` (hardcoded)** — the backend `reqHistoricalDataAsync` is called with `useRTH=False` for all three modes (`_kline`, `_history`, `_intraday`). The command does NOT accept a `useRTH` flag; any `useRTH` token in the command string is silently ignored. All results include pre-market and after-hours data. Mention this if the user expects strict trading-session data.
- **`adj` only affects price fields** — open/high/low/close are adjusted, volume is not
- **History mode may return bars before the start date** — IBKR includes the bar containing the start timestamp, which can be one bar earlier than expected. E.g. `kline AAPL 2026-05-01 2026-05-07 day` may include a bar dated `2026-04-30`. Always compute the actual date range from the returned data.

## Output

The API returns a JSON envelope. The candlestick data lives in `result.bars`:

```json
{
  "success": true,
  "request_id": "req-xxx",
  "result": {
    "symbol": "AAPL",
    "period": "day",
    "count": 5,
    "adjust": "none",
    "bars": [
      {
        "date": "2026-05-08",
        "open": 287.86,
        "high": 294.76,
        "low": 287.5,
        "close": 293.86,
        "volume": 39300184,
        "average": 293.118,
        "barCount": 258450
      }
    ]
  },
  "execution_time_ms": 2558
}
```

Variations by mode:
- **history**: `result` has `start`, `end` instead of `count`
- **intraday**: `date` is ISO 8601 datetime (e.g. `2026-05-14T09:30:00`), no `adjust` field

Always read from `response.result.bars[]`. Never pass raw JSON to the user — present results as prose with markdown formatting:

```
**{Symbol} — {Period} ({N} bars, {date range})**
Range: ${high} ~ ${low}  |  Close-to-close: ${first_close} → ${last_close} ({sign}{X.Y%})
Volume: avg {X} shares/day — {volume pattern note}
Trend: {1-2 sentence summary of price action, key levels, and pattern}
```

### Examples

```
**NVDA — Daily (5 bars, May 6–12)**
Range: $196.16 ~ $223.75  |  Close-to-close: $207.26 → $219.48 (▲ +5.9%)
Volume: avg 126M shares/day — elevated throughout
Trend: Strong uptrend from $196 support, 5 consecutive green candles with above-average volume.
```

```
**NVDA — 1m Intraday (111 bars, May 14)**
Range: $229.36 ~ $233.22  |  Open: $231.34 → Now: $230.26 (▼ -0.5%)
Volume: heavy in first 2 minutes (1.4M shares), then tapered to ~10K/min
Trend: Opened with a spike to $233.22, then gradual selloff through the morning. Intraday low of $229.36 at 09:39, minor bounce to $230.26.
```

**Intraday output** should mention the opening spike/drop, session high/low, and whether volume confirms the trend. For multi-day history, highlight the directional bias, key support/resistance levels, and any volume anomalies.

Always attach ▲/▼ direction arrows to net move. Cite source: AlphaMeta / Interactive Brokers.

## Prerequisites

```bash
# Start the AlphaMeta service (if not running)
alphameta start

# Verify service health and IB connection
curl "http://127.0.0.1:18080/health"

# List available kline commands
curl "http://127.0.0.1:18080/api/v1/search?query=kline"
```

## API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/health` | GET | Health check with IB connection status |
| `/api/v1/search` | GET | Search commands `?category=&query=` |
| `/api/v1/execute` | POST | Execute command `{"cmd": "..."}` |

## Key Concepts

### Price Adjustment

- **Default** (`TRADES`): split-adjusted only
- **`adj`** (`ADJUSTED_LAST`): split + dividend adjusted (flag syntax only — `--adjust` prefix is NOT supported)

### Period Mapping

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

### Data Source

Data is sourced from Interactive Brokers (IBKR) historical data feed. Quality depends on the exchange and contract type. For options, futures, and non-stock instruments, data availability may vary.

## Error Handling

On failure the API returns a JSON error object instead of `result`:

```json
{
  "success": false,
  "error": {
    "code": "COMMAND_ERROR",
    "message": "Symbol not found: INVALID999999"
  }
}
```

Surface `error.message` to the user — never silently retry.

| Situation                                | Reply                                                     |
| ---------------------------------------- | --------------------------------------------------------- |
| Service not running (health check fails) | Start the service: `alphameta start`                      |
| `error.code == "COMMAND_ERROR"`          | Surface `error.message` verbatim. Common messages: `Symbol not found`, `Start must be before end` |
| No data returned for range               | Expand the date range or switch to a shorter period       |
| Network / timeout                        | Retry; if persistent, check IBKR connection via `/health` |

## Related Skills

- "What's this stock's current price?" → `alphameta-quote`
- "What are the Greeks or technical indicators?" → `alphameta-technical`
- "What's the option chain for this symbol?" → `alphameta-quote`

