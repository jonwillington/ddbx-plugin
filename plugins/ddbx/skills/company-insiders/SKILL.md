---
name: company-insiders
description: Look up insider share dealings at one company. Use when the user names a company or ticker and asks whether its directors, executives, insiders or members of Congress have been buying or selling its shares, or wants the recent insider activity behind a stock.
---

Answer with the insider dealings ddbx has on record for one issuer, using the `ddbx` MCP tools.

## 1. Pin down the company and market

ddbx covers five markets: the UK, the US, Sweden, the Netherlands and the US Congress. For a company listed only elsewhere (Japan, Germany, France and so on), say ddbx does not cover that market and stop.

- UK-listed (LSE, AIM, a `.L` ticker, "plc"): market `UK`. US-listed (NYSE, Nasdaq, SEC Form 4): market `US`. Swedish or Dutch listing: `SE` or `NL`.
- If the user gives only a name, call `search` with the name. Each result id starts with its market (`UK:`, `US:`), and the title carries the ticker.
- Some companies list in both places (Shell, BP, Unilever, AstraZeneca). If the user did not say which, check both markets and report each separately. Never add GBP and USD figures together.
- `search` covers UK companies, and US companies only where an insider has made an open-market purchase. If it returns nothing and you know the ticker, go straight to `get_company`. If you do not know the ticker, ask for it. Never substitute a similar company.

## 2. Fetch

- UK or US: call `get_company` with `market` and `ticker`. It returns the business description (`about`), the company page `url` and recent `dealings`, newest first.
- SE or NL: call `search_dealings` with `market`, `ticker` and `signal_only: false`.
- Congress trades in the stock, when asked for or when the user says "politicians": call `search_dealings` with `market: "USG"`, the ticker and `signal_only: false`.
- To read one filing in full, call `get_dealing` with its `id`.
- If `get_company` says there are no surfaced dealings, report exactly that: ddbx has not surfaced any for this ticker. It does not prove no insider has traded.

## 3. Read the rows correctly

- `side` is buy or sell. For US rows read `transaction` as well: only "Open-market purchase" and "Open-market sale" are discretionary trades. Grants, option exercises and shares withheld for tax are pay mechanics, so mention them as such and never present them as conviction buying or selling.
- `value` and `price` are in the row's `currency`, in major units (GBP for UK, USD for US and Congress, SEK or EUR for SE and NL). Congress rows have no exact figure, only `value_range`; quote the range.
- `rating`, strongest first: significant, noteworthy, minor, routine. `null` means not rated (screened out, or still in progress). "Signal" means significant, noteworthy or minor.
- `cluster` means other insiders bought the same company around the same time: `insiders` people within `window_days` days. Clusters are among the strongest patterns, so call them out.

## 4. Answer

1. Lead with one sentence on what happened: who bought or sold, how much, and when. Shape: "Two <company> directors bought £<amount> of shares between <date> and <date>."
2. Then a short table, newest first: trade date, insider and role, buy or sell, value, rating, and a link to the filing's `url`.
3. Note any cluster, and whether the activity is mostly buying or mostly selling.
4. End with the company page `url` from `get_company`.

## Rules

- Link every filing you mention to its `url`. ddbx's written analysis of each filing lives on that page and is not served by these tools.
- Never invent a reason for a rating, a motive for a trade, or a price move. If the user asks why a filing was rated the way it was, say the reasoning is on the linked page.
- Report what insiders did. Do not tell the user to buy or sell a stock, and do not predict prices.
- Plain declarative prose with specific numbers, dates and names. No em dashes. Do not mention models, pipelines or how ddbx works internally.
