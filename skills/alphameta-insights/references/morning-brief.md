# alphameta-morning-brief

Produces a structured daily morning briefing (晨会纪要) covering overnight market moves, pre-market signals, watchlist highlights, today's key catalysts, and a concise trading agenda.

## Workflow

1. Identify the user's watchlist symbols (`qlist`; see [alphameta-watchlist](../../alphameta-watchlist) skill). Ask if none found; accept up to 30.
2. Determine today's date and relevant markets (US, CN).
3. **Overnight RTH recap**: use `kline` for index and watchlist daily bars (RTH close-to-close changes), and fetch intraday capital flow.
4. **Pre-market signals**: use `quote` for current price vs previous close (non-RTH direction).
5. Fetch today's calendar events, earnings calendar, and economic calendar (WebSearch for economic data).
6. Check for recent news on each watchlist symbol.
7. Synthesize into a morning brief (see Output section).

## Commands

For execution syntax, see the [alphameta](../alphameta) skill. 

### Overnight RTH data (kline)

Positional args: `kline <SYMBOL> <period> <count> [adj]`. Use `day` period, count=3 for 3 daily bars.

| Command                | Data                        |
| ---------------------- | --------------------------- |
| `kline I:SPX day 3`    | S&P 500 daily bars          |
| `kline I:COMP day 3`   | Nasdaq Composite daily bars |
| `kline I:INDU day 3`   | Dow Jones daily bars        |
| `kline I:VIX day 3`    | VIX daily bars              |
| `kline <SYMBOL> day 3` | Watchlist symbol daily bars |

The RTH change is `last_bar.close - prev_bar.close` (not `quote.last - quote.close`).

### Pre-market signals (quote)

`quote` returns current data for each symbol:

| Command | Data |
|---------|------|
| `quote <SYMBOL1> <SYMBOL2> ...` | Batch quotes |

`quote.last` = current price (may be pre-market), `quote.close` = last RTH close.  
Pre-market change = `quote.last - quote.close`.

### Calendar, news & more

| Command | Notes |
|---------|-------|
| `calendar earnings` | Earnings today (requires FINNHUB_KEY) |
| `news <SYMBOL>` | Latest news for symbol |
| `preview <SYMBOL>` | Pre-earnings preview |
| `capital-flow <SYMBOL>` | Capital flow data |

For economic calendar, use WebSearch: "this week economic calendar high impact".

## Output

Structure the morning brief in six concise sections (keep total length under 600 words):

**1. Overnight RTH recap** (2-3 bullets, use `kline`)

- Index levels (SPX, COMP, DJI, VIX) — RTH close-to-close change
- HK / CN market close if data available
- Other macro (currencies, commodities, yields)

**2. Pre-market signals** (use `quote`)

- Index futures direction (current vs previous RTH close)
- Watchlist pre-market moves

**3. Watchlist highlights** (table)

| Symbol | RTH Change | Pre-market | Key Event |
|--------|-----------|------------|-----------|

RTH Change = `kline[-1].close - kline[-2].close`. Pre-market = `quote.last - quote.close`.

**4. Today's catalysts** (bulleted)

- Earnings releases today (company, before/after market)
- Economic data (CPI, FOMC, NFP, etc.) with consensus
- Policy / regulatory events

**5. Capital flow signals** (if data available)

- Sectors or names with notable inflow/outflow

**6. Trading agenda** (2-4 bullets)

- Specific names or themes to watch
- Suggested entry/exit levels or key levels to monitor (use analyst targets from consensus if available)

Close with: _Data sourced from AlphaMeta / 数据来源：AlphaMeta_

## Error handling

| Situation | Response |
|---|---|
| Server not running | Tell the user to start AlphaMeta server |
| No symbols provided | "Please provide your watchlist symbols or ask about specific stocks" |
| No calendar data | "No major events scheduled for today" |
| Other errors | Surface verbatim |
