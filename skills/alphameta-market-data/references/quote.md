# Quote — Real-Time Prices

Real-time quote, market depth, and contract metadata for US equities and options via AlphaMeta (IBKR).

## Symbol Format

Stocks: plain ticker (`AAPL`, `NVDA`, `SPY`).
Options: OCC format — see [references/option-chain.md](option-chain.md).

## Workflow

1. Extract symbol(s) from the user's question
2. Choose the right command:
   - **Quick price check** → `quote <symbol>` (batch, multi-symbol)
   - **Full snapshot** → `quote` + `info` for Greeks
   - **Order book** → `depth <symbol>`
   - **Contract metadata** → `info <symbol>` (Greeks, IV, ATR)
3. Run via `POST /api/v1/execute`, translate JSON to prose

## Commands

Always discover current flags via the search endpoint first:

```bash
curl "http://localhost:18080/api/v1/search?query=quote"
curl "http://localhost:18080/api/v1/search?query=depth"
```

| Command | Use For |
|---|---|
| `quote <symbols...>` | Batch quotes for one or more symbols (slower but no subscription needed) |
| `depth <symbol>` | Multi-level BID/ASK order book |
| `info <symbol>` | Contract metadata with Greeks, IV, ATR |
| `range <symbol>` | Trading range |

### `quote` vs Real-Time Subscription

| | `quote` | `add` (subscription) |
|---|---|---|
| Speed | Slower (batch) | Fast (real-time push) |
| Multi-symbol | Yes | No (one at a time) |
| Requires subscription | No | Yes |

For most use cases `quote` is sufficient. Use `add` only when the user needs continuous real-time updates. See [references/subscriptions.md](subscriptions.md).

## Output

`quote` returns a JSON object with price, change, volume, bid/ask, open/high/low/prev-close, and market status.

Always translate to prose — do not return raw JSON:

```
**AAPL — $243.50 ▲ +2.15 (+0.89%)**
Bid/Ask: $243.48 × 100 / $243.52 × 200
Day range: $240.10 ~ $244.80  |  Volume: 18.2M
```

## Error Handling

| Situation | Reply |
|---|---|
| Service not running | Start the service: `alphameta --ibkr` |
| `error.code == "COMMAND_ERROR"` | Surface `error.message` verbatim |
| No data returned | Verify the ticker symbol with the user |
