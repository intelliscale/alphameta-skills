# Order Management

Place, modify, and cancel orders. Supports stocks, options, and multi-leg combo contracts.

## Use Cases

> Place a limit order to buy 100 shares of AAPL at $150
> Sell 2 option contracts (negative quantity = sell)
> Place a butterfly spread with combo syntax

## Examples

```bash
# Buy 100 shares at market
buy AAPL 100 AF

# Sell 2 option contracts
buy NVDA260501C00175000 -2 MKT

# Butterfly spread
buy "bto 1 AAPL260716C00155000 sto 2 AAPL260716C00160000 bto 1 AAPL260716C00165000" 100 AF @ 5.00
```

## Gotchas

> **⚠️ Get confirmation before executing any orders!!!**
>
> **No `sell` command**: Use negative quantity. `buy AAPL -100` = sell 100 shares.
>
> **OCC strike must be 8 digits**: `00175000` not `175000`
>
> **`preview` to test first**: Verify margin impact before actual execution

## Commands

| Command | Description |
|---------|-------------|
| `buy` | Market/limit buy or sell (negative = sell) |
| `limit` | Limit order |
| `fast` | Fast market order (momentum scalping) |
| `evict` | Force close position at midprice |
| `expand` | Batch concurrent orders with brace expansion |
| `cancel` | Cancel order by ID or symbol |
| `modify` | Modify order price/quantity |
| `scale` | Scale-in order (multiple batches) |
| `straddle` | Straddle/strangle option quotes |

Use `search` for command details.
