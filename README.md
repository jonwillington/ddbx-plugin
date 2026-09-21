# ddbx plugin (ChatGPT + Codex)

An [Agent Plugins 1.0](https://developers.openai.com/plugins/build/plugins) package
that puts ddbx insider dealings into ChatGPT and Codex. It bundles:

- **The ddbx MCP server** (`https://api.ddbx.uk/mcp`): read-only, no auth, thin
  tier only. The server lives in `ddbx-data/worker/mcp.ts`; this repo only points at it.
- **Three skills** that teach the assistant how to use the tools well:
  - `company-insiders`: insider activity at one company (UK, US, SE, NL, Congress)
  - `market-scan`: the notable dealings across a market over a period
  - `daily-recap`: ddbx's published daily summary for the UK or US

```
.agents/plugins/marketplace.json   repo marketplace, so Codex can add this repo directly
plugins/ddbx/
  plugin.json                      root manifest, with the com.openai interface block
  mcp.json                         the remote MCP server
  skills/<name>/SKILL.md           workflows
  skills/<name>/agents/openai.yaml display name + MCP dependency per skill
  assets/icon.png, logo.png        from ddbx-site/public/ios-app-icon-uk.png
SUBMISSION.md                      listing copy and test cases for the submission portal
```

## Contract with ddbx-data

The skills name the MCP tools (`search_dealings`, `get_dealing`, `get_company`,
`get_daily_summary`, `search`, `fetch`) and their arguments. Renaming a tool or an
argument in `worker/mcp.ts` breaks this plugin as well as any cached connector:
change it additively, then update the skills here.

The skills also restate the thin-tier posture: the analysis prose is not served,
so the assistant links to each filing's `url` and never invents a reason for a
rating. If the MCP ever serves analysis behind auth, update the Rules sections.

## Test in Codex

```bash
codex plugin marketplace add ./          # from this repo root
codex plugin marketplace list
```

Then enable it in `~/.codex/config.toml`:

```toml
[plugins."ddbx@ddbx"]
enabled = true
```

Try: "What did UK directors buy today?", "Any insider buying at Dunelm?",
"What has Congress traded in NVDA?".

Once this repo is on GitHub: `codex plugin marketplace add jonwillington/ddbx-plugin`.

## Test in ChatGPT

1. Settings → Security and login → turn on **Developer mode**.
2. [ChatGPT Plugins](https://chatgpt.com/plugins) → **+** → server URL
   `https://api.ddbx.uk/mcp`, authentication **None**.
3. Copy the `plugin_asdk_app_…` id from the browser URL.
4. To bundle the skills with it, add `plugins/ddbx/.app.json`:

   ```json
   { "apps": { "ddbx": { "plugin_asdk_app_<id>": {} } } }
   ```

   and `"apps": "./.app.json"` inside `extensions.com.openai` in `plugin.json`.
5. Test in a Work chat with `@ddbx`.

## Publish

- **Your workspace**: ChatGPT Plugins → Personal → the plugin's ⋯ menu → Publish.
  This does not list it publicly.
- **Public directory**: the [submission portal](https://developers.openai.com/plugins/deploy/submission).
  Needs a verified developer identity, and the domain challenge served at
  `https://api.ddbx.uk/.well-known/openai-apps-challenge` (a ddbx-data change).
  Listing copy and the required test cases are in `SUBMISSION.md`.
