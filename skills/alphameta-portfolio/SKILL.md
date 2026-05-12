---
name: alphameta-portfolio
description: |-
  Account-level analysis via IBKR (Interactive Brokers) — positions, P&L, balance, margin, leverage ratio, buying power, and execution history. Distinguishes long vs short positions, shows real-time unrealized/realized P&L, and computes account-level leverage ratio.

  Triggers: "查持仓", "账户余额", "保证金率", "杠杆率", "多头", "空头", "盈亏", "账户概览", "账户表现", "持仓明细", "我的仓位", "positions", "portfolio", "balance", "leverage", "margin", "P&L", "net liquidation", "buying power", "long positions", "short positions", "my account".
---

# AlphaMeta Portfolio

Retrieve and analyze your IBKR account — balance, positions, P&L, margin, and leverage ratio. Designed for account-level health checks, not per-symbol technical analysis.

## When to Use

- Account overview, balance, margin, buying power
- Position details (long/short, quantity, market value, P&L)
- Account-level leverage ratio
- Order and execution history

## Workflow

1. Verify the AlphaMeta service is running (`/health` endpoint).
2. Determine what the user wants — balance overview, positions detail, or full account report.
3. Run the relevant command(s) via `/api/v1/execute`:
   - `balance` for net liquidation, cash, buying power, available funds
   - `positions` for all positions with dollar values and P&L
   - `orders` and `executions` for open orders and trade history
   - `report` for trading report (requires local OrderMgr logs)
4. Compute leverage: `sum of all position dollarValue / NetLiquidation`.
5. Format the response using the template below.

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll skip computing leverage, user didn't ask" | Leverage is the key risk metric. Always compute it: `sum(dollarValue) / NetLiquidation`. |
| "marketValue is easier, I'll use that" | `dollarValue` is notional exposure. `marketValue` gives wrong leverage. Must use `dollarValue`. |
| "I'll just pass through the raw JSON" | Raw output is unreadable. Always format using the Output template. |

## Red Flags

- Single position > 30% of total notional — likely over-concentrated, flag to the user
- Leverage > 2.0x — high margin usage, highlight as a warning
- Using `marketValue` instead of `dollarValue` in leverage calculation — wrong result
- Missing currency labels or disclaimer — output is incomplete

## Output

Present results in markdown-native tables. Balance as a summary table, positions grouped by underlying symbol in a flat table where the **Group** column marks group boundaries (empty = continuation of same group).

### Balance

| Metric | Value |
|--------|------:|
| Net Liquidation | $XXX,XXX |
| Cash | $XXX,XXX |
| Buying Power | $XXX,XXX |
| Available Funds | $XXX,XXX |

### Positions (grouped by symbol)

| Group | Leg | Qty | Notional | Mkt Value | P&L | Theta | Return |
|-------|-----|:---:|---------:|----------:|----:|:-----:|:------:|
| **NVDA** (22.4%) | 5/15 190P | -1 | $386 | -$14 | +$300 | +7 | +95% |
| | 5/15 200P | -1 | $1,024 | -$36 | +$246 | +13 | +87% |
| | 6/18 185P | -6 | $14,972 | -$1,338 | +$2,825 | +51 | +68% |
| **MSFT** (19.4%) | 5/15 380P | -2 | $2,128 | -$42 | +$2,307 | +22 | +98% |
| | 5/15 390P | -1 | $2,516 | -$47 | +$1,442 | +21 | +97% |
| | 5/15 400P | -2 | $13,933 | -$286 | +$1,412 | +85 | +83% |
| **PLTR** (5.3%, 3s 3l) | 5/15 C160 | +1 | $333 | +$6 | -$530 | -6 | -99% |
| | 5/15 P130 | -1 | $2,435 | -$117 | +$217 | +26 | +65% |
| | 6/18 C135 | +1 | $7,890 | +$861 | -$235 | -12 | -21% |
| **DRAM** (1.2%, 1s 1l) | 6/18 P38 | +10 | $4,095 | +$807 | -$460 | -29 | -36% **bearish** |
| | 6/18 P42 | -10 | $7,363 | -$1,569 | +$874 | +47 | +36% |

Always label currency. Sort groups by absolute notional descending. Group header shows **% of total portfolio notional**. For groups with mixed long/short positions, append `Ns Ml` (N short legs, M long legs). Highlight the largest gainers and losers.

Default market direction is **bullish** (not labeled). Only label legs that are **bearish** with `**bearish**` — determined by: `(PC == 'P' AND position > 0)` for long puts, or `(PC == 'C' AND position < 0)` for short calls.

## Prerequisites

```bash
# Start service
alphameta start

# Check health
curl "http://127.0.0.1:18080/health"

# Execute command
curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
  -H "Content-Type: application/json" \
  -d '{"cmd": "<command>"}'
```

## API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/health` | GET | Health check with IB connection status |
| `/api/v1/search` | GET | Search commands `?category=&query=` |
| `/api/v1/execute` | POST | Execute command `{"cmd": "..."}` |

## Command Index

| Category | Commands | Use For |
|---|---|---|
| [Portfolio](references/ref-portfolio.md) | `balance`, `positions`, `orders`, `executions`, `report` | Portfolio analysis, margin check |

## Key Concepts

### Leverage Calculation

```
Leverage Ratio = Σ(dollarValue) / NetLiquidation
```

- Use `dollarValue` NOT `marketValue` for leverage (dollarValue is notional)
- `report` requires local logs from OrderMgr

### Key Fields

| Field | Description |
|---|---|
| `NetLiquidation` | Total equity |
| `dollarValue` | Position notional value (for leverage) |
| `BuyingPower` | Margin buying power |
| `AvailableFunds` | Available for new orders |

For full reference, see [references/ref-portfolio.md](references/ref-portfolio.md).

## Error Handling

| Situation | Reply |
|---|---|
| Service not running (health check fails) | Start the service: `alphameta start` |
| Empty positions | No open positions |
| Command returns error | Surface the error message verbatim |

## Related Skills

- "What's this stock's current price?" → `alphameta-quote`
- "What are the Greeks for this option?" → `alphameta-technical`
- "Close this position" → `alphameta-orders`
- "Set a stop-loss if X drops below Y" → `alphameta-predicate`
