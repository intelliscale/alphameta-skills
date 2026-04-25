# Utilities

Helper commands for information query, alerts, TTS, calculations, and system management.

## Use Cases

> Calculate option Max Pain for specific expiration
> Set price alerts for symbols
> Get contract details with Greeks
> Export historical K-line data

## Examples

```bash
# Get contract metadata
info AAPL

# Calculate max pain
maxpain NVDA 05-01

# Set price alert
alert AAPL > 200

# Text-to-speech announcement
say "AAPL is up 2%"
```

## Gotchas

> **`advice` hangs**: Use `reporter` instead for market strength scores.
>
> **`meta` CLI bug**: Use HTTP API, not direct CLI.
>
> **IV/RV requires market data**: Without subscription, implied volatility may be unavailable.

## Commands

| Command | Description |
|---------|-------------|
| `cash` | Account cash info |
| `calendar` | US stock trading calendar |
| `math` | Calculator (+-*/ sqrt() sin() cos()) |
| `details` | Market data with Greeks (requires subscription) |
| `info` | Contract metadata (ID, type, exchange) |
| `maxpain` | Calculate max pain for expiration |
| `alert` | Price/condition alerts |
| `say` | Text-to-speech |
| `qualify` | Cache contract eligibility |
| `meta` | Command system metadata |
| `reporter` | Market strength score report |
| `simulate` | Simulate events for testing |
| `paper` | Switch/execute paper trading |
| `reconnect` | Reconnect IBKR Gateway |
| `clear` | Clear terminal display |
| `advice` | Market strength score (⚠️ hangs, use `reporter`) |
| `daydumper` | Export historical K-line data |

Use `search` for command details.
