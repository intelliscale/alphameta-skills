# AlphaMeta Server Setup & Authentication

## Installation

```bash
pip install alphameta
```

## Starting the Server

```bash
# Start with Interactive Brokers gateway
alphameta --ibkr
```

Server listens on `http://localhost:18080` by default.

## Health Check

```bash
curl http://localhost:18080/api/v1/health
# → {"status": "ok", "ib_connected": true, "account_id": "U12345678"}
```

## Command Execution

All AlphaMeta commands run via `POST /api/v1/execute`:

```bash
curl -X POST http://localhost:18080/api/v1/execute \
  -H "Content-Type: application/json" \
  -d '{"cmd": "<command [args]>"}'
```

### Response Format

**Success** — result wrapped in envelope:

```json
{
  "success": true,
  "result": { ... },
  "request_id": "...",
  "execution_time_ms": 123
}
```

**Error** — includes error detail:

```json
{
  "success": false,
  "error": { "code": "...", "message": "..." },
  "request_id": "..."
}
```

## Discovering Available Commands

The server exposes a command registry — use this to discover what's available at runtime:

| Query | Purpose |
|---|---|
| `curl http://localhost:18080/api/v1/search` | List all commands grouped by category |
| `curl "http://localhost:18080/api/v1/search?category=Live+Market+Quotes"` | List commands in a specific category |
| `curl "http://localhost:18080/api/v1/search?query=consensus"` | Search for a command and see its usage details |

### API Categories

`Live Market Quotes`, `Order Management`, `Portfolio`, `Predicate Management`, `Quote Management`, `Technical Indicators`, `Utilities`, `Connection`, `Task Management`.

## Authentication

AlphaMeta uses IBKR gateway authentication — no separate API key or token required. Ensure IBKR Gateway / TWS is running and configured before starting the server.

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| `ib_connected: false` | IBKR not running or not configured | Start TWS/Gateway, check port settings |
| Connection refused | Server not running | Start: `alphameta --ibkr` |
| `command not found: alphameta` | Not installed | `pip install alphameta` |

## Related

- **CLI command reference** → [references/cli.md](cli.md)
- **Symbol format guide** → [references/symbol-format.md](symbol-format.md)
