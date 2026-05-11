---
name: alphameta-utilities
description: |
  AlphaMeta utility commands — quote group management, snapshots, economic/earnings/IPO calendar, text-to-speech announcements, calculator, alerts, paper trading, and market strength reporter. Use when: "行情组", "快照", "日历", "经济日历", "财报日历", "IPO", "TTS", "语音播报", "alert", "提醒", "计算器", "paper trading", "模拟交易", "reporter", "market strength", "calendar".
---

# AlphaMeta Utilities

> **Response language**: match the user's input language (Simplified Chinese / Traditional Chinese / English).

## Quick Start

```bash
alphameta start
curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
  -H "Content-Type: application/json" \
  -d '{"cmd": "<command>"}'
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
| [Quote Management](references/ref-quote-mgmt.md) | `qadd`, `qremove`, `qlist`, `qsnapshot`, `qclean`, `qdelete`, `qsave`, `qrestore`, `qloadsnapshot` | Quote groups & snapshots |
| [Utilities](references/ref-utilities.md) | `calendar`, `details`, `alert`, `say`, `reporter`, `paper`, `reconnect`, `math`, `clear`, `daydumper`, `meta`, `qualify`, `simulate` | Calendars, alerts, TTS, calculator |

## Key Concepts

### Quote Groups

Persist subscriptions across sessions:
```bash
qadd tech AAPL MSFT NVDA GOOG  # Add to group
qsnapshot                      # Auto-save subscriptions
qsave tech_config              # Persist to file
qclean tech                    # Remove expired options
```

### Calendars

```bash
calendar              # Economic calendar (next 7 days)
calendar earnings     # Earnings calendar
calendar ipo          # IPO calendar
```

### Market Strength Reporter

> ⚠️ **`advice` hangs** — use `reporter` instead.

```bash
reporter              # Market strength score
```

### Paper Trading

```bash
paper on              # Enable paper trading
paper off             # Disable and switch to live
paper                 # Check current mode
```

For full reference, see [references/ref-quote-mgmt.md](references/ref-quote-mgmt.md) and [references/ref-utilities.md](references/ref-utilities.md).