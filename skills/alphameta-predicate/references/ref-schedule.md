# Schedule Management

Schedule tasks to automatically execute at specified times. Persists across restarts.

## Use Cases

> 10 minutes before market close, buy 100 shares of AAPL
> At 10:00, hedge my NVDA position
> Daily quote reminder at specific times

## Examples

```bash
# Schedule BTC quote at 09:30
sched-add btc-morning 09:30 "qquote C:BTC"

# List scheduled tasks
sched-list

# Cancel a task
sched-cancel btc-morning
```

## Gotchas

> **Time must be in future**: Cannot schedule times in the past.
>
> **Wrap command in quotes**: Arguments must use double quotes, e.g., `"qquote AAPL"`
>
> **Single execution only**: No daily repetition, auto-deletes after run.

## Commands

| Command | Description |
|---------|-------------|
| `sched-add` | Add scheduled task |
| `sched-list` | List all scheduled tasks |
| `sched-cancel` | Cancel scheduled task |

Use `search` for command details.
