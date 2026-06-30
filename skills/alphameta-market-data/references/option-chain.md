# Option Chain — Strikes & Greeks

Discover option chain strikes and retrieve contract metadata with Greeks, IV, and ATR for US equity options via AlphaMeta (IBKR).

## OCC Symbol Format

```
SYMBOL + YYMMDD + C|P + 8-DIGIT_STRIKE

Example: NVDA260501C00175000
  NVDA    → Underlying
  260501  → May 1, 2026
  C       → Call
  00175000 → $175.00 (strike × 1000, 8 digits)
```

## Workflow

1. **Get the chain** — find available strikes for an expiry
2. **Select contract** — identify the OCC symbol the user wants
3. **Get details** — subscribe + fetch Greeks

```bash
# Step 1: discover strikes for NVDA May 1 expiry
chain NVDA 05-01

# Step 2: subscribe to the contract (required before info)
add NVDA260501C00175000

# Step 3: get Greeks, IV, ATR
info NVDA260501C00175000
```

## Commands

| Command | Use For |
|---|---|
| `chain <SYMBOL> <MM-DD>` | List available strikes for an expiry |
| `add <OCC_SYMBOL>` | Subscribe to a contract (required before `info`) |
| `info <OCC_SYMBOL>` | Greeks (delta/gamma/vega/theta/rho), IV, ATR, contract metadata |
| `prequalify <OCC_SYMBOL>` | Check if a contract is tradable |

Always discover current flags:

```bash
curl "http://localhost:18080/api/v1/search?query=chain"
```

## Greeks Reference

| Greek | Meaning |
|---|---|
| Delta | Price sensitivity ($1 change → delta change) |
| Gamma | Delta change rate |
| Vega | IV sensitivity (per 1% IV change) |
| Theta | Time decay (per day) |
| Rho | Interest rate sensitivity |

## Important Notes

- **`add` is required before `info`** — you must subscribe to the contract first to get Greeks
- **Strike = price × 1000** — $175 → `00175000` (zero-padded to 8 digits)
- **Always run `chain` first** to discover valid OCC symbols before using them in orders or strategies
- For strategy recommendations based on chain data, see `alphameta-trading`

## Error Handling

| Situation | Reply |
|---|---|
| `chain` returns empty | No option chain available; stock may not have listed options |
| `info` returns no Greeks | Verify the OCC format and that `add` was called first |
| Symbol not found | Verify ticker and expiry format with the user |
