# Quote Group Management

Organize quotes into named groups for easy management. Supports persistence across sessions.

## Use Cases

> Create a group for tech stocks and manage them together
> Save quote configuration and restore after restart
> Clean expired options from a group

## Examples

```bash
# Create group and add symbols
qadd tech AAPL MSFT NVDA GOOG

# Clean expired options
qclean tech

# Save config for next session
qsave tech_config
```

## Gotchas

> **`qclean` regularly**: Remove expired options to keep groups clean.
>
> **`qsnapshot` vs `qsave`**: Snapshot is for subscriptions (auto-saved), save is for user config files.

## Commands

| Command | Description |
|---------|-------------|
| `qadd` | Add symbols to a named group |
| `qremove` | Remove symbols from group |
| `qlist` | List all/specific quote groups |
| `qsnapshot` | Save current subscriptions (auto-loaded on restart) |
| `qclean` | Remove expired options from group |
| `qdelete` | Delete entire quote group |
| `qsave` | Persist config to file |
| `qrestore` | Restore config from file |
| `qloadsnapshot` | Load saved contract snapshot |

Use `search` for command details.
