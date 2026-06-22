# Screener / 策略筛选

Stock screening — filtering and ranking stocks by valuation, consensus, and financial metrics.

## Partial AlphaMeta Coverage

AlphaMeta does not have a bulk stock screener command. Use these commands for single-symbol screening checks:

| Screening Need | AlphaMeta Command |
|---|---|
| Valuation percentile (PE/PB) | `calc-index <symbol>` |
| Consensus / ratings | `consensus <symbol>` |
| Financial KPIs | `financial-report <symbol>` |
| Operating trends | `operating <symbol>` |

## Workflow

1. Ask the user for specific screening criteria (valuation, sector, market cap, etc.).
2. If a specific symbol is given, run the relevant single-symbol checks.
3. For multi-stock screening, advise that bulk screening is not available via AlphaMeta and suggest using WebSearch or dedicated screening platforms.
