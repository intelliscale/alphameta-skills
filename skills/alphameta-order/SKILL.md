---
name: alphameta-order
description: |
  Place, modify, and cancel orders via AlphaMeta (IBKR REST API). Supports stocks, options, and multi-leg combo orders (rolls, spreads, straddles, iron condors, butterflies). Use when: "下单", "买入", "卖出", "市价单", "限价单", "取消订单", "改单", "对冲", "roll", "spread", "straddle", "butterfly", "condor", "place order", "buy", "sell", "modify order", "cancel order", "options spread".
---

# AlphaMeta Order

> **Response language**: match the user's input language (Simplified Chinese / Traditional Chinese / English).

## ⚠️ Critical Rule

> **Always get confirmation before executing any orders!!!**

## Quick Start

```bash
# Start service
alphameta start

# Search commands
curl "http://127.0.0.1:18080/api/v1/search?query=order"

# Execute
curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
  -H "Content-Type: application/json" \
  -d '{"cmd": "buy AAPL 100 AF"}'
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
| [Order Management](references/ref-orders.md) | `buy`, `limit`, `fast`, `evict`, `expand`, `cancel`, `modify`, `scale` | Place/modify/cancel orders |
| [Multi-Leg Orders](references/ref-multi-leg.md) | `bto`, `sto`, `btc`, `stc` + ratios + OCC | Roll, spread, straddle, condor, butterfly |

## Key Concepts

### OCC Format (Critical)

Options OCC format: `SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE`

```
# WRONG: strike × 100
GLD260501P00044000  ← 440×100=44000 (wrong!)

# CORRECT: strike × 1000, pad to 8 digits
GLD260501P00440000  ← 440×1000=440000 → 00440000 ✓
```

### Buy vs Sell

> **No `sell` command** — use **negative quantity**.
> `buy AAPL -100` = sell 100 shares

### Preview Before Execution

Always preview to check margin impact:
```bash
buy NVDA260501C00175000 1 AF @ 5.00 preview
```

### Multi-Leg Syntax

```bash
buy "<leg1> <ratio1> <occ1> <leg2> <ratio2> <occ2>" <qty> AF @ <net_price>
```

| Leg | Meaning |
|---|---|
| `bto` | Buy to Open |
| `sto` | Sell to Open |
| `btc` | Buy to Close |
| `stc` | Sell to Close |

For full reference, see [references/ref-orders.md](references/ref-orders.md) and [references/ref-multi-leg.md](references/ref-multi-leg.md).