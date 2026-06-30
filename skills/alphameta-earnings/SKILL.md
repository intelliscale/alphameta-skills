---
name: alphameta-earnings
description: 'Post-earnings analysis skill — generates institutional-grade earnings update reports (8–12 page DOCX) and structured conversation summaries for companies under coverage. Covers beat/miss analysis, segment breakdown, margin trends, guidance assessment, updated estimates, and valuation. Supports US, HK, and A-share markets. Use this skill whenever the user wants a post-earnings analysis or quarterly-results writeup, even if they do not say "earnings update" verbatim. Triggers: "earnings update", "quarterly results", "Q1/Q2/Q3/Q4 results", "earnings report", "post-earnings analysis", "beat/miss", "guidance update", "财报分析", "业绩更新", "季度业绩", "季报", "年报", "盈利分析", "财报点评".'
---

# AlphaMeta Earnings

> **Response language**: match the user's input language — Simplified Chinese / English. Both the DOCX report body and the in-chat summary follow the user's language; chart labels, axis titles, and file names always stay in English.

## When to Use

| Trigger                 | Example                                            |
| ----------------------- | -------------------------------------------------- |
| Post-earnings analysis  | *"Analyze TSLA latest earnings"* / *"帮我分析特斯拉最新财报"* |
| Specific quarter update | *"MSFT Q3 earnings update"* / *"微软Q3业绩更新"*         |
| Quarterly results       | *"Q1/Q2/Q3/Q4 results for [company]"*              |

**Do not trigger if:** user wants an initiation report.

## Data Sources

Priority: **CLI (primary) → Web Search (supplement)**

Use the AlphaMeta CLI for all market data. See the [alphameta](../alphameta) skill for server setup and command execution syntax.

| Data Needed                                  | AlphaMeta Command                 |
| -------------------------------------------- | --------------------------------- |
| Filings & reports (10-Q/10-K)                | `filing <symbol>`                 |
| Financial statements (IS/BS/CF)              | `financial-report <symbol> IS p8` |
| Consensus, EPS history, beat rate, valuation | `consensus <symbol>`              |
| Valuation percentile (PE/PB history)         | `calc-index <symbol>`             |
| Multi-quarter operating KPIs                 | `operating <symbol> p8`           |
| Price history                                | `kline <symbol>`                  |
| Current quote                                | `quote <symbol>`                  |
| Analyst ratings & targets                    | `consensus <symbol>` (aggregated) |
| News                                         | `news <symbol>`                   |
| Segment data                                 | `sec <symbol> p8`                 |

Web Search supplements: earnings call transcripts, consensus estimates vintage, M&A precedent data, industry peer multiples, analyst upgrade/downgrade details.

## Execution Workflow

**Step 1 — Identify the latest earnings period**
Use `filing <symbol>` to find the latest 10-Q/10-K filing and extract the period end date. Confirm the period with the user.

**Step 2 — Collect data & analyze**
See [references/workflow.md](references/workflow.md) for: beat/miss analysis, segment breakdown, margin analysis, guidance assessment, estimate revision.

**Step 3 — Update valuation**
See [references/valuation-methodologies.md](references/valuation-methodologies.md) for DCF, trading comps, and precedent transactions.

**Step 4 — Generate DOCX report**
See [references/report-structure.md](references/report-structure.md) for page-by-page templates. Use `scripts/generate_report.py`.

**Step 5 — Output conversation summary**
See [references/summary-card-spec.md](references/summary-card-spec.md) for the 8-module format.

## Output

1. **DOCX report**: `[SYMBOL]_Q[N]_[YEAR]_Earnings_Update.docx` (8-12 pages, 8-12 charts)
2. **Conversation summary**: 8-module structured output directly in chat

**IMPORTANT**: Do NOT append a Sources section to the conversation output. All citations belong in the DOCX only.

## Related Skills

This skill is the heaviest in the family (institutional-grade 8–12 page DOCX). For lighter or differently-framed asks, defer to a sibling:

| If the user wants ... | Use |
|-----------------------|-----|
| Pre-earnings preview (before the report) | [`alphameta-earnings-preview`](../alphameta-earnings-preview) |
| Analyst consensus deep-dive | [`alphameta-consensus`](../alphameta-consensus) |
| Financial statements & KPIs | [`alphameta-fundamental`](../alphameta-fundamental) |
| SEC filings & XBRL | [`alphameta-sec-filings`](../alphameta-sec-filings) |
| Valuation percentile (PE/PB history) | `calc-index` command |
| Multi-period operating KPIs | `operating` command |
| Price chart & technicals | [`alphameta-kline`](../alphameta-kline) |
| Stock news | `news` command |
| Live quote & fundamentals | [`alphameta-market-data`](../alphameta-market-data) |

If the user wants the full earnings DOCX *plus* one of the above (e.g. "earnings update on TSLA and how its valuation compares"), do this skill first, then chain to the other.

## Reference Files

| File | Contents | When to Read |
|------|----------|--------------|
| [workflow.md](references/workflow.md) | Data collection steps, beat/miss framework, segment/margin/guidance analysis | Before analysis |
| [valuation-methodologies.md](references/valuation-methodologies.md) | DCF, trading comps, precedent transactions — full methodology | During valuation |
| [report-structure.md](references/report-structure.md) | Page-by-page DOCX templates, table and chart formatting | Before generating report |
| [summary-card-spec.md](references/summary-card-spec.md) | 8-module conversation summary format with examples | When outputting summary |
| [best-practices.md](references/best-practices.md) | Quality checklist, common mistakes, headline examples | Quality check |

## File Layout

```
alphameta-earnings/
├── SKILL.md
├── commands/
│   └── earnings.md             # /earnings <SYMBOL> slash command
├── references/
│   ├── workflow.md             # 17-step data collection + analysis
│   ├── valuation-methodologies.md  # DCF + comps + precedent
│   ├── report-structure.md     # 12-page DOCX template
│   ├── summary-card-spec.md    # 8-module conversation summary
│   └── best-practices.md       # Quality checklist
└── scripts/
    └── generate_report.py      # Report generator — 8 charts + DOCX
