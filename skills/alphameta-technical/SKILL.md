---
name: alphameta-technical
description: |
  Options technical indicators via AlphaMeta — Greeks (delta, gamma, vega, theta, rho), implied/experienced volatility (IV/HV), max pain, gamma exposure (GEX), margin requirements, and leverage ratio. Use when: "Greeks", "delta", "gamma", "vega", "theta", "隐含波动率", "IV", "历史波动率", "HV", "max pain", "gamma exposure", "保证金", "margin", "杠杆率", "leverage", "portfolio margin".
---

# AlphaMeta Technical

> **Response language**: match the user's input language (Simplified Chinese / Traditional Chinese / English).

See the [alphameta](../alphameta) skill for server setup and command execution syntax.

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

### OCC Format

`info` and `details` commands require OCC-format option symbols:

```
SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE

Example: NVDA260501C00175000
  NVDA    → Underlying
  260501  → May 1, 2026
  C       → Call
  00175000 → $175.00 (price × 1000, 8 digits)
```

For full reference on `info`/`quote` commands, see [`alphameta-market-data`](../alphameta-market-data).

For full reference, see [references/ref-indicators.md](references/ref-indicators.md) and [references/ref-utilities.md](references/ref-utilities.md).