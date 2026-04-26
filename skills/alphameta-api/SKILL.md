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
| [Live Market Quotes](references/ref-live-quotes.md)  | add, align, chains, depth, info, oadd, prequalify, qquote, range, remove, rm                                                                   | Real-time quotes, chains, contract details      |
| [Quote Management](references/ref-quote-mgmt.md)     | colorset, colorsload, qadd, qclean, qdelete, qlist, qloadsnapshot, qremove, qrestore, qrm, qsave, qsnapshot                                    | Quote groups, snapshots                         |
| [Utilities](references/ref-utilities.md)             | advice, alert, alias, calendar, cash, clear, daydumper, details, expand, math, meta, paper, qualify, reconnect, reporter, say, simulate, unset | Calculator, alerts, TTS                         |
| [Predicate Management](references/ref-predicate.md)  | auto, ifclear, ifgroup, iflist, ifls, ifrm, ifthen                                                                                             | Conditional triggers, automation                |
| [Schedule](references/ref-schedule.md)               | sadd, scancel, slist                                                                                                                           | Timed tasks, reminders                          |
| [Task Management](references/ref-task.md)            | taskcancel, tasklist                                                                                                                           | Async batch operations                          |
| [Technical Indicators](references/ref-indicators.md) | dex, dge, gex, maxpain                                                                                                                         | Max pain, gamma exposure, volatility            |
## Important Notes

**Option OCC format** of 4 parts: `SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE`  

1. Root symbol of the underlying stock or ETF, padded with spaces to 6 characters
2. Expiration date, 6 digits in the format yymmdd
3. Option type, either P or C, for put or call
4. Strike price: price in dollars × 1000, front-padded with 0s to 8 digits

Examples:

```
NVDA260501P00175000
```

The above symbol represents a put on NVDA, expiring on 05/01/2026, with a strike price of $175.