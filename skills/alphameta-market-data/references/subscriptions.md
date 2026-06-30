# Subscriptions — Real-Time Data Feed

Manage real-time WebSocket subscriptions for market data. Subscriptions persist within a server session and provide faster data than one-shot `quote` calls.

## When to Use

Use `add` instead of `quote` when:
- The user wants continuous real-time price updates
- The user explicitly asks to "subscribe" or "watch" a symbol
- You need Greeks from `info` (requires prior `add`)

## Commands

```bash
# Subscribe to a symbol (fast, persistent)
curl -X POST ... -d '{"cmd": "add AAPL"}'

# Unsubscribe
curl -X POST ... -d '{"cmd": "remove AAPL"}'

# Check current subscriptions (via search)
curl "http://localhost:18080/api/v1/search?query=add"
```

## `add` vs `quote`

| | `add` | `quote` |
|---|---|---|
| Speed | Fast (real-time push) | Slower (batch lookup) |
| Multi-symbol | No (one at a time) | Yes (space-separated) |
| Requires subscription | Yes | No |
| Use case | Continuous monitoring | One-time price check |

## Option Subscriptions

For option contracts, subscribe via `oadd` after discovering the OCC symbol via `chain`:

```bash
# Step 1: find strikes
chain NVDA 05-01

# Step 2: subscribe to specific contract
oadd NVDA260501C00175000

# Step 3: get Greeks
info NVDA260501C00175000
```

## Quote Groups

Persistent symbol groups can be saved across sessions. See `alphameta-watchlist` for `qadd`, `qlist`, `qsave`, and other group management commands.

## Error Handling

| Situation | Reply |
|---|---|
| Symbol not found | Verify ticker or OCC format with the user |
| `error.code == "COMMAND_ERROR"` | Surface `error.message` verbatim |
