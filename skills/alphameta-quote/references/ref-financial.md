# Live Market Quotes - Financial Data

Real-time fundamental financial data from IBKR, including financial summaries, ownership structure, analyst estimates, and key ratios.

## Use Cases

> Get EPS, Revenue, Dividend for AAPL
> Check analyst estimates and price targets
> Retrieve P/E, Market Cap, ROE, Beta ratios

## Examples

```bash
financial AAPL              # Financial summary (default)
financial AAPL summary     # Same as above
financial AAPL ownership   # Ownership structure
financial AAPL snapshot    # Financial overview
financial AAPL analyst     # Analyst estimates
financial AAPL ratios      # Key ratios (P/E, Market Cap, etc.)
```

## Report Types

| Type | Source | Description |
|------|--------|-------------|
| `summary` | reqFundamentalDataAsync | EPS, Revenue, Dividend |
| `ownership` | reqFundamentalDataAsync | ISIN, floatShares, Owner |
| `snapshot` | reqFundamentalDataAsync | CompanyInfo, Employees, Description |
| `analyst` | reqFundamentalDataAsync | EPS forecasts, recommendations |
| `ratios` | reqMktData (Tick 258) | P/E, Market Cap, ROE, Beta |

## Output Examples

### Summary (default)

```json
{
  "symbol": "AAPL",
  "report_type": "ReportsFinSummary",
  "data": {
    "EPSs": {"EPS": "12.020000"},
    "Dividends": {"Dividend": "0.1925000000"},
    "TotalRevenues": {"TotalRevenue": "258490000000.000000"}
  }
}
```

### Ratios (Tick 258)

```json
{
  "symbol": "AAPL",
  "report_type": "Tick258",
  "data": {
    "MKTCAP": 3974332,
    "PEEXCLXOR": 34.39295,
    "BETA": 1.075,
    "TTMGROSMGN": 47.32529,
    "TTMROEPCT": 151.394,
    "TTMREV": 435617
  }
}
```

## Ratios Available (Tick 258)

| Tag | Description |
|-----|-------------|
| MKTCAP | Market capitalization (millions) |
| PEEXCLXOR | P/E ratio excluding extraordinary items |
| TTMGROSMGN | Gross margin % |
| TTMROEPCT | Return on Equity % |
| BETA | Beta |
| TTMREV | Revenue (millions) |
| DIVGRPCT | Dividend growth % |
| TTMNIAC | Net income (millions) |
| TTMEBITD | EBITDA (millions) |
| NPRICE | Current price |
| NHIG | 52-week high |
| NLOW | 52-week low |

## Gotchas

> **`ratios`**: Only works during market hours. Returns null if market is closed.
>
> **`summary`**: Returns most recent fiscal period data. Period selection not supported.
>
> **Unavailable**: `CalendarReport` requires WSH subscription ($250/month) - returns error if used.
>
> **Unavailable**: `ReportsFinStatements` is deprecated by IBKR - returns error if used.

## Permissions

Requires Professional market data subscription (`@require_permission("PRO")`)

## See Also

- [IBKR reqFundamentalData documentation](references/ref-ibkr-fundamental.md)
