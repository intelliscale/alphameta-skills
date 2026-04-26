# Live Market Quotes

Real-time market quotes, including subscription, quick queries, market depth, and option chains.

## Use Cases

> Monitor NVDA quotes in real-time
> Pre-cache NVDA option chain eligibility for the next 15 days
> Get option chain for specific expiration date

## Examples

```bash
# Subscribe (required for info)
add NVDA AAPL
add NVDA260501C00175000  # OCC: NVDA | 260501 | C | $175

# Brace expansion - add multiple strikes at once
add NVDA260501P00{150,175,200}000  # Puts: $150, $175, $200

# Quick quotes (slow, supports multiple symbols)
qquote NVDA AAPL MSFT

# Option chain
chains NVDA 05-01

# Detailed info (fast, requires add first)
info NVDA
info NVDA260501C00175000
```

## Gotchas

> **`qquote`**: Slower one-time request, supports multiple symbols. Returns bid/ask/last/volume.
>
> **`chains`**: Returns strikes only. Use `info`/`qquote` with OCC format for details.
>
> **`add` auto-saves**: Each `add` auto-calls `qsnapshot` to persist state.

## Commands

| Command      | Description                                                          |
| ------------ | -------------------------------------------------------------------- |
| `add`        | Add symbols to live watchlist (supports brace expansion)             |
| `remove`     | Remove symbols from watchlist                                        |
| `qquote`     | One-time quote with volatility (IV/HV)                               |
| `depth`      | Market depth (multi-level BID/ASK)                                   |
| `chains`     | Option chain strikes by expiration                                   |
| `prequalify` | Pre-cache contract eligibility                                       |
| `oadd`       | Add symbols from pending orders                                      |
| `align`      | Batch add ATM straddle/strangle/spread quotes                        |
| `range`      | Generate OCC symbols for price range                                 |
| `info`       | Contract details with Greeks, IV/HV, ATR, RSI (requires `add` first) |

Use `search` for command details.
