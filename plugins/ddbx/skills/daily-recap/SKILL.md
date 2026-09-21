---
name: daily-recap
description: Give ddbx's recap of a trading day's insider dealings. Use when the user asks what happened today, yesterday or on a given date in UK director dealings or US insider trading, or asks for the morning or end-of-day insider roundup.
---

Deliver ddbx's published daily summary for the UK or US market with the `ddbx` MCP tool `get_daily_summary`.

## 1. Fetch

- `market`: `UK` by default. `US` when the user mentions US stocks, Form 4, Wall Street or a US ticker. If they ask about both, call it once per market.
- `date`: omit it for "today" or "latest". The tool returns the most recent summary, which may be from an earlier day, so check the `date` it returns. Pass YYYY-MM-DD for a specific day.
- `session`: omit it unless the user asks. `afternoon` is the end-of-day recap; `morning` covers the opening session.

## 2. If there is no summary

- If the returned `date` is older than the day the user asked about, say plainly that the summary for that day has not been published yet, and give the latest one with its date.
- Summaries are published on weekdays only. For a Saturday or Sunday, give Friday's and say so.
- If the user wanted today's dealings specifically, follow up with `search_dealings` for that market with `since` set to today, and list what has been disclosed so far.

## 3. Answer

1. The `headline`, then the `body`. The body is ddbx's own published prose, so relay it faithfully and attribute it to ddbx. Do not add claims it does not make.
2. The `cited` filings as a short list: company (ticker), insider, buy or sell, value, rating, each linked to its `url`.
3. Mention how many filings the day covered (`filings_considered`).
4. State the date and session the summary is for.

## Rules

- Figures are in the market's currency in major units: GBP for UK, USD for US.
- Link every filing you mention to its `url`. The per-filing analysis lives there and is not served by these tools.
- Do not recommend buying or selling anything, and do not predict prices.
- No em dashes. Do not mention models, pipelines or how ddbx works internally.
