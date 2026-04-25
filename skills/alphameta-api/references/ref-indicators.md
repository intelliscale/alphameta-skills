# Technical Indicators

Calculate options-related technical indicators.

## Use Cases

> Calculate max pain for option chain
> Analyze gamma exposure (GEX)

## Gotchas

> **IBKR-hosted**: Results depend on IBKR's data provider, may return null if subscription insufficient.

> **IV/RV requires options market data**: Without proper subscription, implied volatility may be unavailable.

## Commands

| Command | Description |
|---------|-------------|
| `maxpain` | Calculate max pain strike for expiration |
| `gex` | Gamma exposure analysis |

Use `search` for command details.
