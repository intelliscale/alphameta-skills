---
name: alphameta-insights
description: |-
  Market intelligence: strategy screener, market scanning, top movers with news correlation, quote anomalies, morning briefings, catalyst monitoring for watchlist, event-driven strategies, sector rotation, supply chain analysis, industry overviews, and disruptive innovation analysis.
  Triggers: "筛选", "策略筛选", "异动", "行情扫描", "晨报", "早报", "催化剂", "事件驱动", "板块轮动", "产业链", "行业概览", "创新分析", "ARK", "screener", "anomaly", "top movers", "morning brief", "catalyst", "event strategy", "sector rotation", "supply chain", "disruptive innovation", "market intelligence", "市场强度", "资金流向", "事件日历", "今天财报"
---

# AlphaMeta Insights

Market intelligence hub — screening, scanning, briefing, and thematic research via AlphaMeta.

> **Response language**: match the user's input language — Simplified Chinese / English.

See the [alphameta](../alphameta) skill for server setup and command execution syntax.

## When to use

Trigger when user asks about: strategy screening, top movers / market scanning, quote anomalies / unusual movements, morning market briefing, catalyst monitoring, event-driven strategies and event opportunities, sector rotation signals, supply chain analysis, industry overview reports, or disruptive innovation diagnostics.

For raw data (quotes, kline, calendar) refer to the respective dedicated skills.

## Sub-topic Routing

| User intent | Load references file |
|---|---|
| Strategy screener | references/screener.md |
| Top movers / market scanning | references/top-movers.md |
| Quote anomalies / unusual moves | references/anomaly.md |
| Morning briefing / 晨报 | references/morning-brief.md |
| Market pulse / 行情扫描 | references/market-pulse.md |
| Catalyst radar / watchlist scan | references/catalyst-radar.md |
| Event radar / 事件日历 | references/event-radar.md |
| Event-driven strategy | references/event-strategy.md |
| Event opportunity | references/event-opportunity.md |
| Sector rotation signals | references/sector-rotation.md |
| Sector monitor | references/sector-monitor.md |
| Supply chain analysis | references/supply-chain.md |
| Industry overview report | references/industry-overview.md |
| Disruptive innovation / ARK | references/ark-analysis.md |

## CLI Commands

All commands run via AlphaMeta's `POST /api/v1/execute`. Discover available commands:

```bash
curl http://localhost:18080/api/v1/search
```

Check the relevant reference file for specific commands per insight type.

## Frameworks

### Morning Brief
Pre-market summary: overnight moves, watchlist highlights, today's catalysts, and trading agenda. See [references/morning-brief.md](references/morning-brief.md).

### Market Pulse
Market strength assessment and capital flow scanning using `reporter`, `advice`, and `capital-flow`. See [references/market-pulse.md](references/market-pulse.md).

### Event Radar
Earnings calendar, economic calendar, IPO calendar, and pre-earnings previews. See [references/event-radar.md](references/event-radar.md).

### Catalyst Radar
Multi-dimension catalyst scanning across watchlist: news, insider trades, earnings, analyst revisions. See [references/catalyst-radar.md](references/catalyst-radar.md).

### Screener
Stock screening by valuation, consensus, and financial metrics. See [references/screener.md](references/screener.md).

### Event-Driven Strategy
NLP sentiment scoring, merger/restructuring, buyback, management change signals. See [references/event-strategy.md](references/event-strategy.md).

### Event Opportunity
Corporate event opportunity capture — pricing dislocations from M&A, index changes, lockup expiries. See [references/event-opportunity.md](references/event-opportunity.md).

### Sector Rotation
Macro cycle positioning, sector momentum and capital flow signals. See [references/sector-rotation.md](references/sector-rotation.md).

### Supply Chain Analysis
Upstream/downstream value chain mapping, bottleneck identification, margin analysis. See [references/supply-chain.md](references/supply-chain.md).

### Industry Overview
Competitive landscape, core players, theme trends — full sector report. See [references/industry-overview.md](references/industry-overview.md).

### ARK-Style Innovation Analysis
TAM sizing, Wright's Law cost curves, 3-scenario 5-year target (Bull/Base/Bear). See [references/ark-analysis.md](references/ark-analysis.md).

## Auth requirements

All CLI commands: Public — no login required.

## Error handling

| Situation | Response |
|---|---|
| Server not running / connection refused | Tell the user to start AlphaMeta server: `alphameta --ibkr` |
| Command not found | Run `curl http://localhost:18080/api/v1/search` to list available commands |
| Empty data returned | "No data available for the requested symbol or time range" |
| Other errors | Surface verbatim |

## MCP fallback

If AlphaMeta server is unavailable, use WebSearch to supplement market intelligence data.

## Related skills

| User wants | Use |
|---|---|
| Real-time quotes, K-line, depth | `alphameta-quote` |
| Candlestick / OHLCV data | `alphameta-kline` |
| Analyst consensus / ratings | `alphameta-consensus` |
| Insider trades / 13F holdings | `alphameta-flows` |
| SEC filings | `alphameta-sec-filings` |
| Financial statements | `alphameta-financial-report` |
| Calendar / utilities | `alphameta-utilities` |
| Technical indicators / Greeks | `alphameta-technical` |

## File layout

```
alphameta-insights/
├── SKILL.md
└── references/
    ├── morning-brief.md · market-pulse.md · event-radar.md
    ├── catalyst-radar.md · screener.md · anomaly.md
    ├── top-movers.md · event-strategy.md · event-opportunity.md
    ├── sector-rotation.md · sector-monitor.md
    ├── supply-chain.md · industry-overview.md
    └── ark-analysis.md
```
