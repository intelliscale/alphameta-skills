---
name: alphameta-quote
description: |
  Real-time market quotes, option chains, market depth, and contract details via AlphaMeta (IBKR REST API). Provides live quotes, quick queries, multi-level BID/ASK depth, option chain strikes, contract metadata with Greeks/IV/ATR, and real-time subscription. Triggers: "现在多少钱", "股价", "期权链", "行权价", "隐含波动率", "IV", "market depth", "bid ask", "option chain", "AAPL quote", "NVDA chain", "SPY IV", "Greeks", "持仓", "多头", "空头", "杠杆", "保证金".
---

# AlphaMeta Quote

> **Response language**: match the user's input language (Simplified Chinese / Traditional Chinese / English).

## Quick Start

1. **Start service (if not running)**:
   ```bash
   alphameta start
   ```

2. **Check status**:
   ```bash
   curl "http://127.0.0.1:18080/health"
   ```

3. **Search commands**:
   ```bash
   curl "http://127.0.0.1:18080/api/v1/search?query=quote"
   ```

4. **Execute**:
   ```bash
   curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
     -H "Content-Type: application/json" \
     -d '{"cmd": "<from Command Index ONLY>"}'
   ```

## API Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/health` | GET | Health check |
| `/api/v1/search` | GET | Search commands |
| `/api/v1/execute` | POST | Execute command |

## Command Index

| Category | Commands | Use For |
|---|---|---|
| [Live Quotes](references/ref-live-quotes.md) | `add`, `remove`, `quote`, `depth`, `chains`, `prequalify`, `oadd`, `align`, `range`, `info` | Real-time quotes, option chains, market depth |
| [Financial Data](references/ref-financial.md) | `financial` | EPS, revenue, P/E, market cap, analyst estimates |

## Key Concepts

### Symbol & OCC Format

Stocks: `AAPL`, `NVDA`, `SPY`
Options: `SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE`

```
# Stock
AAPL    → AAPL

# Option: NVDA, May 1 2026, Call, $175
NVDA260501C00175000
```

Strike = price × 1000, zero-padded to 8 digits. Example: $175 → `00175000`

### `add` vs `quote`

| Command | Speed | Multi-symbol | Requires subscription |
|---|---|---|---|
| `add` | Fast | No (one at a time) | Yes (subscribe first) |
| `quote` | Slow | Yes (batch) | No (one-time) |

### Getting Option Chain

```bash
# Get strikes for NVDA May 1 expiration
chains NVDA 05-01

# Get detailed Greeks for specific contract (requires add first)
add NVDA260501C00175000
info NVDA260501C00175000
```

For full reference, see [references/ref-live-quotes.md](references/ref-live-quotes.md) and [references/ref-financial.md](references/ref-financial.md).