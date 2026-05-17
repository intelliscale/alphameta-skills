---
name: alphameta
description: 'Guides agents through AlphaMeta server setup and command execution. All CLI commands run via `POST /api/v1/execute` on a local server. Use when another AlphaMeta skill references this for server setup or command execution syntax, or when the user asks about server status, startup, or how to run commands.'
---

# AlphaMeta

Base reference for the AlphaMeta server. Other AlphaMeta skills assume this server is running — this document covers setup and the common command execution pattern.

## Server

Start the server:

```bash
alphameta start
```

The server listens on `http://localhost:18080`.

## Command Execution

All AlphaMeta commands run via `POST /api/v1/execute`:

```bash
curl -X POST http://localhost:18080/api/v1/execute \
  -H "Content-Type: application/json" \
  -d '{"cmd": "<command [args]>"}'
```

A successful response returns the result inside a `result` field, wrapped with `success`, `request_id`, and `execution_time_ms`. On error, the response includes an `error` object with `code` and `message`.

Health check:

```bash
curl http://localhost:18080/health
```

## Convention

Commands are invoked by name with space-separated arguments. For example:

| Request Body | Meaning |
|---|---|
| `{"cmd": "consensus AAPL"}` | Consensus data for AAPL |
| `{"cmd": "kline AAPL 1d"}` | Daily kline for AAPL |
| `{"cmd": "positions"}` | All open positions |

To discover available commands:

| Query                                                                     | Purpose                                        |
| ------------------------------------------------------------------------- | ---------------------------------------------- |
| `curl http://localhost:18080/api/v1/search`                               | List all commands grouped by category          |
| `curl "http://localhost:18080/api/v1/search?category=Live+Market+Quotes"` | List commands in a specific category           |
| `curl "http://localhost:18080/api/v1/search?query=consensus"`             | Search for a command and see its usage details |

Categories: `Live Market Quotes`, `Order Management`, `Portfolio`, `Predicate Management`, `Quote Management`, `Technical Indicators`, `Utilities`, `Connection`, `Task Management`.
