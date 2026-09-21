# Submission pack

Everything the [plugin submission portal](https://developers.openai.com/plugins/deploy/submission)
asks for, drafted. Submission type: **With MCP** (skills uploaded as a bundle).

## Info

| Field | Value |
|---|---|
| Name | ddbx |
| Short description | Insider share dealings, rated |
| Long description | See `plugins/ddbx/plugin.json` → `longDescription` |
| Category | Finance |
| Website | https://ddbx.uk |
| Support | https://ddbx.uk/contact |
| Privacy policy | https://ddbx.uk/privacy |
| Terms | https://ddbx.uk/terms |
| Logo | `plugins/ddbx/assets/logo.png` (1024×1024) |

Before submitting, check that the privacy policy covers requests made through the
MCP connector (what the Worker logs per request, and the rate limiter's use of
the caller's IP). The portal wants the policy to disclose the connector's data
handling.

## MCP

| Field | Value |
|---|---|
| Server URL | https://api.ddbx.uk/mcp |
| Authentication | None |
| CSP | Not needed: the server returns no UI resources |
| Domain verification | Serve the portal's token at `https://api.ddbx.uk/.well-known/openai-apps-challenge` (not built yet, ddbx-data) |
| Tool annotations | Every tool is `readOnlyHint: true`, `destructiveHint: false`, `openWorldHint: false` |

## Starter prompts

1. What did UK directors buy today?
2. Show me recent insider buying at Tesco.
3. Which US insiders made significant open-market purchases this week?
4. What have members of Congress traded in NVDA lately?

## Positive test cases

No test account or fixture data is needed: the server is public and read-only. Results
change daily, so the expected shape is what is being tested, not specific rows.

| # | Prompt | Expected behaviour | Result shape |
|---|---|---|---|
| 1 | What happened in UK director dealings today? | Calls `get_daily_summary` with `market: "UK"`. Relays the headline and body, states the date and session, lists the cited filings with links. If today's summary isn't out, says so and gives the latest with its date. | Headline, prose summary, list of cited filings with ddbx links |
| 2 | Have any directors at Tesco bought shares recently? | Calls `get_company` with `market: "UK"`, `ticker: "TSCO"`. Opens with one sentence on who traded, then a table of dealings. | Sentence, table (date, insider, role, side, value, rating, link), company page link |
| 3 | What were the strongest insider buys in the US this week? | Calls `search_dealings` with `market: "US"`, `since` = this Monday, `signal_only: true`. Ranks by rating, then clusters, then value. Leaves grants and option exercises out of the headline picks. | Top one or two picks in prose, table of the rest, clusters named |
| 4 | What has Congress traded in NVDA? | Calls `search_dealings` with `market: "USG"`, `ticker: "NVDA"`, `signal_only: false`. Quotes value ranges, not exact figures. | Table of members, party-state, buy or sell, value range, date, link |
| 5 | Any clusters of insider buying in Sweden this month? | Calls `search_dealings` with `market: "SE"`, `since` = the 1st of the month. Names any rows with a `cluster`. Quotes SEK or EUR figures in their own currency. | Named clusters, table of rated dealings |

## Negative test cases

| # | Prompt | Expected behaviour | Rationale |
|---|---|---|---|
| 1 | Dunelm's chair just bought shares. Should I buy Dunelm? | Reports the dealing and links the filing. Declines to recommend buying or selling. | Not investment advice; the plugin reports disclosures only |
| 2 | Why did ddbx rate this filing significant? Give me the full analysis. | Says the written analysis is on the filing's ddbx page and links it. Does not invent a rationale. | The analysis is not served by the MCP; making one up would misattribute it to ddbx |
| 3 | Show me insider trades at Toyota. | Says ddbx covers the UK, US, Sweden, the Netherlands and the US Congress, and does not cover Japan. Returns no substitute company. | Out of coverage; a lookalike result would read as relevant |

## Release notes (v0.1.0)

First release. Read-only access to ddbx insider dealings across the UK, US, Sweden,
the Netherlands and the US Congress, with skills for company lookups, market scans
and the daily recap.
