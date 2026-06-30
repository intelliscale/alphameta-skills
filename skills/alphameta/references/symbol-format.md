# AlphaMeta Symbol Format Reference

## Stocks & ETFs

Plain ticker symbol (no market suffix). AlphaMeta resolves the correct instrument from the IBKR contract database.

| Asset | Format | Example |
|---|---|---|
| US Stock/ETF | Ticker | `AAPL`, `NVDA`, `SPY`, `QQQ` |
| US Index | Ticker | `SPX`, `VIX` |

> If symbol resolution fails, try qualifying via `{"cmd": "qualify <symbol>"}` first.

## Options — OCC Symbol Format

```
SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE
```

| Part | Meaning | Example |
|---|---|---|
| `SYMBOL` | Underlying ticker | `NVDA` |
| `YYMMDD` | Expiration date | `260501` → May 1, 2026 |
| `C` / `P` | Call or Put | `C` = Call, `P` = Put |
| `8-DIGIT_STRIKE` | Strike × 1000, zero-padded to 8 digits | `00175000` → $175.00 |

### Examples

| Description | OCC Symbol |
|---|---|
| NVDA May 1 2026 $175 Call | `NVDA260501C00175000` |
| AAPL Jun 19 2026 $200 Put | `AAPL260619P00200000` |
| SPY Dec 31 2026 $450 Call | `SPY261231C00450000` |

**Always run `chain <SYMBOL> <MM-DD>` to discover valid OCC symbols before using them in strategies or orders.**

## Finding Option Chains

```bash
# Get available strikes for NVDA May 1 expiration
curl -X POST http://localhost:18080/api/v1/execute \
  -d '{"cmd": "chain NVDA 05-01"}'

# Then query Greeks for a specific contract
curl -X POST http://localhost:18080/api/v1/execute \
  -d '{"cmd": "info NVDA260501C00175000"}'
```
