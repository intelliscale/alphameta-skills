---
name: alphameta-technical
description: |
  Options technical indicators via AlphaMeta — Greeks (delta, gamma, vega, theta, rho), implied/experienced volatility (IV/HV), max pain, gamma exposure (GEX), margin requirements, and leverage ratio. Use when: "Greeks", "delta", "gamma", "vega", "theta", "隐含波动率", "IV", "历史波动率", "HV", "max pain", "gamma exposure", "保证金", "margin", "杠杆率", "leverage", "portfolio margin".
---

# AlphaMeta Technical

> **Response language**: match the user's input language (Simplified Chinese / Traditional Chinese / English).

## Quick Start

```bash
alphameta start
curl "http://127.0.0.1:18080/api/v1/search?query=technical"
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
| [Technical Indicators](references/ref-indicators.md) | `maxpain`, `gex` | Max pain, gamma exposure |
| [Utilities](references/ref-utilities.md) | `details`, `info` | Contract metadata with Greeks |

## Key Concepts

### Greeks (from `info` / `details`)

| Greek | Meaning |
|---|---|
| `Delta` | Price sensitivity (1 = stock equivalent) |
| `Gamma` | Delta change rate |
| `Vega` | IV sensitivity (per 1% IV change) |
| `Theta` | Time decay (per day) |
| `Rho` | Interest rate sensitivity |

### Volatility

| Term | Description |
|---|---|
| `IV` (Implied Volatility) | Market's expected future volatility |
| `HV` (Historical Volatility) | Actual past volatility |
| `IV/RV` | IV vs realized volatility ratio |

### Margin & Leverage

```
Leverage = Σ(dollarValue) / NetLiquidation
Margin % = Σ(marginReq) / NetLiquidation × 100
```

### Max Pain

Maximum pain strike = the price where maximum total option value expires worthless.

```
# Calculate max pain for NVDA May 1 expiration
maxpain NVDA 05-01
```

For full reference, see [references/ref-indicators.md](references/ref-indicators.md) and [references/ref-utilities.md](references/ref-utilities.md).