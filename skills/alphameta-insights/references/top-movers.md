# Top Movers / 异动扫描

Identifies stocks with significant price or volume movements and correlates them with recent news or events.

## Current Status

AlphaMeta does not have a dedicated top-movers scanner that correlates price movements with news. The closest available commands:

| Need | AlphaMeta Command |
|---|---|
| Market strength signals | `advice` |
| Capital flow for a symbol | `capital-flow <symbol>` |
| News for a specific mover | `news <symbol>` |

## Workflow

1. Identify a specific symbol from user input.
2. Run `advice` for broad market-level signals.
3. For specific symbols, run `capital-flow` to check flow intensity, `news` for recent catalysts.

**Note**: Automated top-mover discovery (scanning all symbols and ranking by movement) is not available via the current AlphaMeta API. Ask the user for a specific symbol to analyze.
