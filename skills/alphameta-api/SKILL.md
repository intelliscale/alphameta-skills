---
name: alphameta-api
description: IBKR trading API - query positions/orders/fills/balance, place/modify/cancel orders, real-time quotes/option chains/market depth, manage quote groups/scheduled tasks/conditional triggers. Use when working with options trading, portfolio management, automated conditional orders, IBKR API integration, margin calculation, leverage ratio, order placement, market quotes, or option chains.
---

# AlphaMeta-API

## Quick Start

1. **Start service (if not running)**:
   ```bash
   alphameta start
   ```

2. **Check service status**:
   ```bash
   curl "http://127.0.0.1:18080/health"
   # → {"status": "ok", "ib_connected": true, "account_id": "U12345678"}
   ```

3. **Search available commands**:
   ```bash
   curl "http://127.0.0.1:18080/api/v1/search?query=balance"
   # → {"commands": [{"name": "balance", "description": "...", ...}, ...]}
   ```

4. **Execute a command**:
   ```bash
   curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
     -H "Content-Type: application/json" \
     -d '{"cmd": "<from Command Index ONLY>"}'
   ```
  ⚠️ Never put invented commands here！

## API Endpoints

| Endpoint          | Method | Description                         |
| ----------------- | ------ | ----------------------------------- |
| `/health`         | GET    | Health check                        |
| `/api/v1/search`  | GET    | Search commands `?category=&query=` |
| `/api/v1/execute` | POST   | Execute command `{"cmd": "..."}`    |

## Command Index

| Category                                             | Commands                                                                                                                                       | Use For                                         |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| [Portfolio](references/ref-portfolio.md)             | balance, executions, ls, orders, positions, report                                                                                             | Leverage ratio, margin check, position analysis |
| [Order Management](references/ref-orders.md)         | buy, cancel, evict, fast, limit, modify, scale, straddle                                                                                       | Place/modify/cancel orders                      |
| [Multi-Leg Orders](references/ref-multi-leg.md)     | bto, sto, btc, stc (leg identifiers); roll, spread, straddle, condor                                                                            | Roll options, vertical spreads, straddles, iron condors, butterflies |
| [Live Market Quotes](references/ref-live-quotes.md)  | add, align, chains, depth, info, oadd, prequalify, qquote, range, remove, rm                                                                   | Real-time quotes, chains, contract details      |
| [Quote Management](references/ref-quote-mgmt.md)     | colorset, colorsload, qadd, qclean, qdelete, qlist, qloadsnapshot, qremove, qrestore, qrm, qsave, qsnapshot                                    | Quote groups, snapshots                         |
| [Utilities](references/ref-utilities.md)             | advice, alert, alias, calendar, cash, clear, daydumper, details, expand, math, meta, paper, qualify, reconnect, reporter, say, simulate, unset | Calculator, alerts, TTS (calendar econ/earnings/ipo)                         |
| [Predicate Management](references/ref-predicate.md)  | auto, ifclear, ifgroup, iflist, ifls, ifrm, ifthen                                                                                             | Conditional triggers, automation                |
| [Schedule](references/ref-schedule.md)               | sadd, scancel, slist                                                                                                                           | Timed tasks, reminders                          |
| [Task Management](references/ref-task.md)            | taskcancel, tasklist                                                                                                                           | Async batch operations                          |
| [Technical Indicators](references/ref-indicators.md) | dex, dge, gex, maxpain                                                                                                                         | Max pain, gamma exposure, volatility            |
## Important Notes

**Option OCC format** of 4 parts: `SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE`  

1. Root symbol of the underlying stock or ETF
2. Expiration date, 6 digits in the format yymmdd
3. Option type, either P or C, for put or call
4. Strike price: price in dollars × 1000, front-padded with 0s to 8 digits

Examples:

```
# WRONG
GLD260501P00044000
│ │       │      └─ 440×100=44000 → WRONG! strike should be ×1000
│ │       └─ P (C|P, correct)
│ └─ 260501 (YYMMDD, correct)
└─ GLD (SYMBOL, correct)

# CORRECT
GLD260501P00440000
│ │       │      └─ 440×1000=440000 → pad to 8 digits → 00440000 ✓
│ │       └─ P (C|P, correct)
│ └─ 260501 (YYMMDD, correct)
└─ GLD (SYMBOL, correct)
```

The above symbol represents a put on GLD, expiring on 05/01/2026, with a strike price of $440.

## Multi-Leg Order Syntax

**Syntax**: `buy "<legs>" <qty> AF @ <net_price>`

| Field | Meaning |
|-------|---------|
| `<legs>` | Leg identifiers: `bto`, `sto`, `btc`, `stc` + ratio + OCC |
| `<qty>` | External quantity (ratio × qty = actual contracts) |
| `AF` | Algo: Adaptive Fast (recommended) |
| `<net_price>` | Net credit (+) or debit (-) per base unit |

**Quick Examples**:

```bash
# Roll: btc 1 leg + sto 1 leg, ratio 1:1, qty 8
buy "btc 1 GLD260501P00044000 sto 1 GLD260618P00044000" 8 AF @ 5.35

# Butterfly: ratio 1:2:1, qty 100 → actual 100:200:100
buy "bto 1 AAPL260716C00155000 sto 2 AAPL260716C00160000 bto 1 AAPL260716C00165000" 100 AF @ 5.00
```

For full documentation, see [Multi-Leg Orders](references/ref-multi-leg.md).