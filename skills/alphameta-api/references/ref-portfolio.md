# Portfolio

Query account status, positions, orders, and execution records.

## Use Cases

> Calculate account leverage ratio
> Check current positions and their dollar values
> View open orders and execution history

## Examples

```bash
# Get account balance
balance

# Get all positions
positions

# Calculate leverage: sum(dollarValue) / NetLiquidation
```

## Gotchas

> **Use `dollarValue` NOT `marketValue`**: `dollarValue` is notional value for leverage calculation.
>
> **`report` requires local logs**: You must first log opening trades via OrderMgr.

## Key Fields

| Field | Description |
|-------|-------------|
| `NetLiquidation` | Total equity |
| `dollarValue` | Position notional value (for leverage) |
| `BuyingPower` | Margin buying power |
| `AvailableFunds` | Available for new orders |

## Commands

| Command | Description |
|---------|-------------|
| `balance` | Account balance and net liquidation |
| `positions` | All positions with dollar values |
| `orders` | Open orders status |
| `executions` | Fill/execution history |
| `report` | Trading report (requires local logs) |

Use `search` for command details.
