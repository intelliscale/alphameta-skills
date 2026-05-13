# AlphaMeta Skills

![Skills](https://img.shields.io/badge/Skills-7-green?style=flat-square)
[![WeChat](https://img.shields.io/badge/WeChat-Group-C5EAB4?style=flat&logo=wechat&logoColor=white)](https://github.com/kelvingao/.github/blob/main/profile/QR.png)

AlphaMeta makes your AI assistant:
- Fluent in your broker — ask about stock prices, your portfolio, positions, orders, and valuations in plain English or 中文, backed by real Interactive Brokers data
- Fluent in quant trading — ask about trend analysis, trading signals, backtesting, and algo trading strategies in plain English or 中文, powered by real-time market data and quantitative models

7 skills covering market data, order management, options strategies, portfolio analysis, technical indicators, conditional automation, and utilities across stocks / options / futures / crypto.

## Install

```bash
# Install everything globally (~/.claude/skills/)
npx skills add intelliscale/alphameta-skills -g

# Or just one skill, globally
npx skills add intelliscale/alphameta-skills -g --skill alphameta-orders
```

---

## Update

```bash
# Update all skills
npx skills update -g

# Update a single skill
npx skills update alphameta-orders -g
```

---

## What you can ask

Ask your AI assistant naturally (supports 中文 / English):

- *"我的 NVDA 持仓现在盈亏如何？"* / *"How is my NVDA position doing?"*
- *"帮我买入 10 手 SPY 260530 500 看跌期权"* / *"Buy 10 SPY May 30 500 puts"*
- *"计算 GLD260501P440 的 delta 和 gamma"* / *"What's the delta on GLD260501P440?"*
- *"当 TSLA 跌破 150 美元时平仓"* / *"Close my TSLA position if it drops below $150"*
- *"显示 GLD 期权链，IV 排序"* / *"Show me GLD option chain sorted by IV"*

---

## What's inside

| Group | Skills |
|---|---|
| **Live Market Data** | [`alphameta-quote`](skills/alphameta-quote/SKILL.md) — Real-time quotes, option chains, market depth, fundamental data |
| **Order Management** | [`alphameta-orders`](skills/alphameta-orders/SKILL.md) — Place/modify/cancel orders, multi-leg strategies, rolling, spread |
| **Portfolio** | [`alphameta-portfolio`](skills/alphameta-portfolio/SKILL.md) — Positions, P&L, balance, margin, leverage ratio |
| **Options Strategy** | [`alphameta-options-strategy`](skills/alphameta-options-strategy/SKILL.md) — Strategy recommendation based on market view and IV; covered call, straddle, spread, iron condor, butterfly |
| **Technical Analysis** | [`alphameta-technical`](skills/alphameta-technical/SKILL.md) — IV, Greeks (delta/gamma/vega/theta), max pain, gamma exposure |
| **Automation** | [`alphameta-predicate`](skills/alphameta-predicate/SKILL.md) — Conditional triggers, monitoring, auto-execution |
| **Utilities** | [`alphameta-utilities`](skills/alphameta-utilities/SKILL.md) — Calculator, calendar, TTS alerts, paper trading, simulate |

---

## Prerequisites

You need the AlphaMeta server running (for live quotes, your positions, orders):

```bash
# Install
curl -fsSL https://alphameta.app/install.sh | bash

# Start Server
alphameta start

# Check status
curl "http://127.0.0.1:18080/health"
# → {"status": "ok", "ib_connected": true, "account_id": "U12345678"}
```

---

## Architecture

```
User Query → AI Assistant → AlphaMeta Skills → REST API → IBKR Gateway
                                   ↓
                            AlphaMeta Server (port 18080)
```

AlphaMeta runs as a local service connecting to IBKR, exposing a clean REST API that your AI assistant can call using natural language.