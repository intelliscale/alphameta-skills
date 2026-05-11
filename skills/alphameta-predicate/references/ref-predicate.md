# Predicate Management

Conditional trigger engine for "if... then..." automated trading. Conditions persist until manually stopped or funds exhausted.

## Use Cases

> Sell 200 shares when CRCL RSI > 80
> Buy when AAPL crosses above EMA20
> EMA crossover strategy: long on up-crossover, short on down-crossover

## Examples

```bash
# Sell when RSI > 80
ifthen if CRCL rsi 300 > 80: sell CRCL 200

# EMA crossover strategy
ifthen if AAPL ema crossover up: buy AAPL 100 AF
ifthen if AAPL ema crossover down: sell AAPL 100 AF
```

## Gotchas

> **Conditions do NOT auto-expire**: After triggering, conditions continue monitoring repeatedly.
>
> **Stop conditions**: Manual `ifclear`/`ifrm` or funds exhausted.
>
> **`if` must be lowercase**: It's part of the DSL syntax.

## Commands

| Command | Description |
|---------|-------------|
| `ifthen` | Create conditional trigger (DSL) |
| `iflist` | List all running predicates |
| `ifgroup` | Manage predicate groups (OCA/OTO/OTOCO) |
| `ifclear` | Clear all predicates (⚠️ irreversible) |
| `ifrm` | Delete specific predicate |
| `auto` | Enable/stop predicate engine |

Use `search` for command details.
