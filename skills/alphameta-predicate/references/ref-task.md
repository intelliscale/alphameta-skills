# Task Management

Manage async tasks running in memory (created automatically by the system, disappear after restart).

## Use Cases

> Check which background tasks are currently running
> Cancel a stuck background task

## Examples

```bash
# List running tasks
tasklist

# Cancel stuck task
taskcancel 456
```

## Gotchas

> **Memory-only**: Tasks disappear after service restart.
>
> **System tasks**: Created automatically (e.g., position refresh after order).

## Commands

| Command | Description |
|---------|-------------|
| `tasklist` | List all running async tasks |
| `taskcancel` | Cancel a specific task by ID |

Use `search` for command details.
