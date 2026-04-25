# Live Market Quotes

Real-time market quotes, including subscription, quick queries, market depth, and option chains.

## Use Cases

> Monitor NVDA quotes in real-time
> Pre-cache NVDA option chain eligibility for the next 15 days
> Get option chain for specific expiration date

## Examples

```bash
# Subscribe to NVDA quotes
add NVDA

# Get NVDA option chain for May 1st
chains NVDA 05-01

# One-time quote with volatility
qquote NVDA260501C00175000
```

## Gotchas

> **`chains` returns strikes only**: List of strike prices, not call/put details. Use `qquote` with OCC format for details.
>
> **Pre-cache before 0DTE**: Use `prequalify` before trading same-day expiring options.
>
> **`add` auto-saves**: Each `add` auto-calls `qsnapshot` to persist state.

## Commands

| Command | Description |
|---------|-------------|
| `add` | Add symbols to live watchlist (supports brace expansion) |
| `remove` | Remove symbols from watchlist |
| `qquote` | One-time quote with volatility (IV/HV) |
| `depth` | Market depth (multi-level BID/ASK) |
| `chains` | Option chain strikes by expiration |
| `prequalify` | Pre-cache contract eligibility |
| `oadd` | Add symbols from pending orders |
| `align` | Batch add ATM straddle/strangle/spread quotes |
| `range` | Generate OCC symbols for price range |

Use `search` for command details.
