---
name: alphameta-watchlist
description: |
  Named grouping of symbols (stocks, options, futures) persisted locally via AlphaMeta. Read your watchlist groups, create new groups, add/remove symbols, delete groups. All data is stored locally — no remote state changes, no broker involvement. Supports full workflow: get symbols from a group → route to alphameta-quote for batch quotes, or alphameta-portfolio for position checks.

  Triggers: "我的自选股", "自选股有哪些", "我关注的股票", "我的分组", "把 X 加到自选", "添加到自选", "创建分组", "删除自选", "删除分组", "自選股", "關注的股票", "分組", "把 X 加到自選", "新增至自選", "建立分組", "刪除自選", "刪除分組", "watchlist", "my watchlist", "favorited stocks", "watch groups", "add to watchlist", "remove from watchlist", "create group", "delete group", "rename group".
---

# AlphaMeta Watchlist

Persistent named groups of securities, stored locally via AlphaMeta's quote group system. Think of it as bookmarks for symbols — stocks, options, futures, indices. Data lives in local diskcache, not on any broker server. No network required for CRUD; only live quote lookups need a running AlphaMeta service.

## When to Use

- "我的自选股有哪些？" / "Show me my watchlist" — list every group and its symbols
- "我的 Tech 分组里有什么？" / "What's in my Tech group?" — filter by group name
- "显示所有有实时报价的分组" / "Show me all groups with live quotes" — list groups, check `live` field
- "把 NVDA 加入我的自选" / "Add NVDA to my watchlist" — create or append symbols to a group
- "从自选里移除 AAPL" / "Remove AAPL from my watchlist" — delete a symbol from a group
- "删除我的 test 分组" / "Delete my test group" — remove an entire group
- "批量查看自选股行情" / "Watchlist batch check" — get symbols then route to `alphameta-quote` or `alphameta-portfolio`

## Quick Start

```bash
# Start service (if not running)
alphameta start

# List all watchlist groups
curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
  -H "Content-Type: application/json" \
  -d '{"cmd": "qlist"}'

# Add symbols to a group (creates group if needed)
curl -X POST "http://127.0.0.1:18080/api/v1/execute" \
  -H "Content-Type: application/json" \
  -d '{"cmd": "qadd tech-stocks NVDA AAPL MSFT"}'
```

## Workflow

### Reading

1. Call `qlist` to get all groups.
2. `qlist` returns `{"groups": {"<name>": {"symbols": [...], "live": [...]}}}`.
   - `symbols`: all symbols saved in the group
   - `live`: subset of symbols currently streaming live quotes
3. Filter by group name (LLM-side) if user asks for a specific group.
4. To batch-check prices, pass the symbols to `alphameta-quote`:
   ```
   Extract symbols → curl POST /api/v1/execute {"cmd": "quote <symbols>"}
   ```

### Writing (one-shot, no two-step protocol)

All mutations are local (diskcache). No remote side effects.
- **Describe** what you are about to do in the user's language.
- **Execute** immediately — no explicit confirmation gate needed.
- **Exception**: for `qdelete` (irreversible), briefly confirm with the user before executing.

| Action | Command | Notes |
|--------|---------|-------|
| Create group + add symbols | `qsave <group> <symbol...>` | If no symbols given, saves currently streaming quotes into the group |
| Add symbols to existing group | `qadd <group> <symbol...>` | Creates group if it doesn't exist |
| Remove specific symbols | `qremove <group> <symbol...>` | Supports glob patterns (`qremove mygroup AAPL*`) |
| Remove all symbols from group | `qremove <group>` | Clears the group but keeps it |
| Delete entire group | `qdelete <group...>` | Can delete multiple at once. **Briefly confirm before executing.** |

### Chained Workflows (common)

| User asks | Flow |
|-----------|------|
| "My watchlist stocks' gainers today" | `qlist` → extract symbols → `alphameta-quote` (batch quote) |
| "Are any of my watchlist options ITM?" | `qlist` → extract symbols → `alphameta-technical` (greeks/IV) |
| "Check if these positions are in my portfolio" | `qlist` → extract symbols → `alphameta-portfolio` (positions filter) |
| "Save my current quotes as a watchlist group" | `qsave <name>` (no symbols → saves live quotes) |

## Common Rationalizations

| Rationalization | Reality |
|----------------|---------|
| "I'll just use the raw JSON output" | The `qlist` response is already structured. Present it as a readable table — group name as header, symbols as a comma-separated list. |
| "I'll rephrase qlist as watchlist since it's what the user understands" | **Correct.** The backend calls them "quote groups"; the skill layer should call them "watchlist groups". Translate terminology for the user. |
| "I need to run two-step confirmation for all mutations" | **Wrong.** These are local writes — no broker state change. Only `qdelete` warrants a quick check. |
| "I'll hardcode `qadd` → `qsave` mapping" | `qadd` appends to an existing group (or creates if missing). `qsave` overwrites. Use `qadd` for "add to", `qsave` for "create new group". |

## Red Flags

- Using `qsave` when user says "add" — `qsave` creates/replaces, `qadd` appends. GET IT RIGHT.
- Calling `qdelete` without a brief confirmation — the group is gone permanently.
- Forgetting to route symbols to the display skill — listing symbols without prices is only useful for inventory. If user asks "how are they doing", immediately chain to `alphameta-quote`.
- Group name collisions — if `qadd` target doesn't exist, it creates it. If user said "add to group X" and X doesn't exist, warn them it'll be created.
- Treating `client-{id}` groups as user watchlists — these are auto-generated system snapshots. `qadd`/`qremove` edits will be overwritten. `qdelete` breaks startup auto-restore. Do not manage them manually.
- Assuming `global` is the primary restore source — `client-{id}` snapshot takes priority on startup. `global` is only a fallback.

## Output

Present watchlist data as markdown tables.

### List All Groups

```
qlist → {"groups": {"tech": {"symbols": ["NVDA","AAPL","MSFT"], "live": ["NVDA","AAPL"]}, ...}}
```

| Group | Symbols | Live |
|-------|---------|:----:|
| **tech** | NVDA, AAPL, MSFT | NVDA ✓, AAPL ✓ |
| **hk-stocks** | 0700.HK, 9988.HK | — |

When `live` is non-empty, mark symbols that are actively streaming with a checkmark.

### Single Group Detail

| Group: tech | Symbols |
|-------------|---------|
| NVDA | ✓ (live) |
| AAPL | ✓ (live) |
| MSFT | — |

### After Adding Symbols

```
qadd tech GOOGL → {"added": ["GOOGL"], "failed": []}
```

> Added GOOGL to **tech**. ✓ Success.
> *(optional chained query)* → "Would you like to see the current price of GOOGL?"

### After Removing Symbols

```
qremove tech AAPL → null (success)
```

> Removed AAPL from **tech**.

## Prerequisites

```bash
# Start the AlphaMeta service
alphameta start

# Check health
curl "http://127.0.0.1:18080/health"
# → {"status": "ok", "ib_connected": true}
```

**No prior service login needed for CRUD.** Watchlist data is stored locally in diskcache and is available even when IBKR is disconnected. Only the live quote routing step (`alphameta-quote`) requires a connected service.

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Health check with IB connection status |
| `/api/v1/execute` | POST | Execute command `{"cmd": "..."}` |

**No `/api/v1/search` needed** — the command set is fixed and documented below.

## Command Index

| Action | Command | Example | Returns |
|--------|---------|---------|---------|
| List all groups | `qlist` | `{"cmd": "qlist"}` | `{"groups": {"name": {"symbols": [...], "live": [...]}}}` |
| Filter groups | `qlist <name...>` | `{"cmd": "qlist tech hk-stocks"}` | Same shape, filtered |
| Create / replace group | `qsave <name> <symbol...>` | `{"cmd": "qsave tech NVDA AAPL"}` | `{"added": [...], "failed": [...]}` |
| Add to group | `qadd <name> <symbol...>` | `{"cmd": "qadd tech GOOGL"}` | `{"added": [...], "failed": [...]}` |
| Remove from group | `qremove <name> <symbol...>` | `{"cmd": "qremove tech AAPL"}` | `null` (success) |
| Delete group(s) | `qdelete <name...>` | `{"cmd": "qdelete old-group"}` | `null` (success) |

The gateway wraps responses as:
```json
{"success": true, "result": <above>, "request_id": "...", "execution_time_ms": N}
```

## Key Concepts

### Local Storage

Watchlist groups are stored in AlphaMeta's diskcache under keys `("quotes", "<group_name>")` → `set(<symbols>)`. This means:

- **Persistence**: survives restarts
- **Speed**: instantaneous mutations (no network round-trip)
- **No broker dependency**: works even when IBKR is disconnected
- **No sharing**: groups are local to this machine

### Group Semantics

| Concept | Detail |
|---------|--------|
| Group names | Free-form strings (avoid special characters) |
| Symbols | Any valid IBKR symbol: `NVDA`, `AAPL260515P00200000`, `/ESM6`, `I:VIX` |
| Empty groups | Show as `(empty)` in listings |
| Symbol casing | Case-sensitive. `AAPL` and `aapl` are different. |
| `live` field | Subset of symbols that are currently streaming real-time quotes |

### System Groups

Two group types are managed by the system, not created by the user:

**`client-{id}` (e.g. `client-0`, `client-2`) — Auto-generated live quote snapshot**

- Created automatically by `qsnapshot` whenever quotes are added/removed (`add`, `remove`, `oadd`, `align`).
- Stores full IBKR Contract objects (not plain symbols) — `{"contracts": [...]}`.
- On startup, AlphaMeta restores from this snapshot first. Only if absent does it fall back to `global` group or built-in defaults (`SPY`, `QQQ`, `AAPL` + futures + indices).
- **Not a user watchlist.** `qadd`/`qremove` edits are overwritten on the next quote change. Do not `qdelete` unless you intend to reset saved state.
- Stored in diskcache under key `("quotes", f"client-{clientId}")`.

**`global` — Shared default fallback group**

- Plain symbol-set group (`set("AAPL", "MSFT")`) created via `qsave`/`qadd`.
- Loaded only when no `client-{id}` snapshot exists on startup.
- Legacy fallback mechanism — still works but `client-{id}` takes priority.

| Feature | `client-{id}` | User groups |
|---------|---------------|-------------|
| Created by | `qsnapshot` (auto) | `qadd` / `qsave` (manual) |
| Data format | `{"contracts": [Contract, ...]}` | Plain symbol strings |
| Updated | Every add/remove (auto overwrite) | Only on manual edit |
| Restore priority | 1st (on startup) | N/A (or `global` as 2nd fallback) |

### Relationship to Other Commands

The q* commands (qlist, qadd, qsave, qremove, qdelete) are the same system used by AlphaMeta's quote stream management. When you `qsave` or `qadd`, you're writing into the same quote-group namespace used by the live quote view. This means a watchlist group is also a "quote group" — it can be loaded into the live stream via `qrestore`.

## Error Handling

| Situation | Reply |
|-----------|-------|
| Service not running | `alphameta start` to start the service |
| `qlist` returns empty groups | "No watchlist groups yet. Create one with `qadd <name> <symbols>`." |
| `qadd` returns `failed: [...]` | "Could not qualify: AAPL. Check symbol spelling." |
| `qdelete` on non-existent group | "Group not found. Run `qlist` to see available groups." |
| `qremove` no matching symbols | "No matching symbols found in that group. Use `qlist <group>` to check contents." |
| Permission error (403) | "This command requires a PRO-tier API key. Set `ALPHAMETA_API_KEY`." |
| Other API error | Surface the error message verbatim. |

## Related Skills

- "What's the current price of my watchlist?" → `alphameta-quote`
- "How are my watchlist options performing?" → `alphameta-technical`
- "Are any watchlist symbols in my portfolio?" → `alphameta-portfolio`
- "Set an alert if a watchlist symbol drops below X" → `alphameta-predicate`

