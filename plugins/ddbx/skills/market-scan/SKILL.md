---
name: market-scan
description: Scan a whole market for notable insider dealings over a period. Use when the user asks what insiders, directors or members of Congress have been buying or selling across the UK, US, Sweden, the Netherlands or Congress, for the strongest or biggest insider buys this week or month, or for clusters of insiders buying the same stock.
---

Survey recent insider dealings across one or more markets with the `ddbx` MCP tool `search_dealings`, and pick out what matters.

## 1. Choose markets and a window

- Markets: `UK` (directors of LSE companies), `US` (SEC Form 4 insiders), `SE` (Sweden), `NL` (Netherlands), `USG` (members of the US Congress). If the user names no market, use `UK` and `US` and report them separately.
- Turn the period into disclosure dates, YYYY-MM-DD. "Today" is today's date. "This week" starts on Monday. "This month" starts on the 1st. With no period given, use the last seven days and say so.
- Pass the start as `since`. To reach further back, repeat the call with `before` set to the oldest `disclosed_date` you have.

## 2. Fetch

- Call `search_dealings` once per market with `since` and `limit: 50`.
- Leave `signal_only` at its default (`true`) for "notable", "strongest", "worth knowing about" or "significant". Set it to `false` only when the user wants every filing, including unrated ones.
- Narrow with `ticker` only if the user names a stock. For one company in depth, use the company-insiders workflow instead.

## 3. Rank

1. Rating first: significant, then noteworthy, then minor.
2. Within a rating, buys that belong to a `cluster` (several insiders buying the same company within `window_days` days) come first, then larger `value`.
3. For US rows, only "Open-market purchase" and "Open-market sale" in `transaction` are discretionary trades. Keep grants, option exercises and tax withholding out of the headline picks.
4. Compare values only within one currency. UK is GBP, US and Congress are USD, SE and NL carry their own `currency`. Congress rows have only a `value_range`.

## 4. Answer

1. Lead with the one or two dealings that matter most, in a sentence each: company, insider and role, amount, date, rating.
2. A table of the rest, grouped by market: company (ticker), insider, buy or sell, value, rating, trade date, link to `url`.
3. Name any clusters explicitly ("three directors at X bought within 10 days").
4. If a market returned no rows for the window, say "no rated dealings disclosed since <date>". Do not describe an empty result as a quiet market unless you checked with `signal_only: false`.

## Rules

- Link every filing you mention to its `url`. ddbx's written analysis sits on that page; these tools do not return it.
- Never invent why a filing was rated, why an insider traded, or what the price did next.
- Describe what insiders did. Do not recommend buying or selling anything.
- Plain declarative prose with specific numbers, dates and company names. No em dashes. Do not mention models, pipelines or how ddbx works internally.
