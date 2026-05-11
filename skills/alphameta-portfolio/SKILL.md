---
name: alphameta-portfolio
description: |
  Query AlphaMeta IBKR account portfolio — positions, P&L, balance, leverage ratio, margin utilization, and execution history. Use when working with: "查持仓", "账户余额", "保证金率", "杠杆率", "多头", "空头", "盈亏", "账户概览", "positions", "portfolio", "balance", "leverage", "margin", "P&L", "net liquidation", "buying power".
---

# AlphaMeta Portfolio

> **Response language**: match the user's input language (Simplified Chinese / Traditional Chinese / English).

## Quick Start

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