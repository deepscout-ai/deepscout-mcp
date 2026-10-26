# DeepScout MCP server

Scrape product catalogs from any e-shop and match the same products across shops, with no EAN or GTIN needed. Works from Claude, Claude Code, Cursor, Codex, VS Code and any other MCP client.

DeepScout ([deepscout.ai](https://deepscout.ai/en/dev)) hosts the server, so you install nothing. You write what you want in plain language, and the agent calls the DeepScout API for you.

```
https://mcp.deepscout.ai/mcp
```

## What you can do with it

- **Scrape an e-shop.** Add a website, check the free sample, then scrape the full catalog or only a list of product URLs. DeepScout handles browser rendering, anti-bot protection, proxies and pagination.
- **Match products across shops.** Find the same product in your shop and in your competitors' shops, by name, parameters and images. Product matching works when the shops share no EAN, GTIN or SKU.
- **Use the data.** Download the exports and let the agent compare your prices with the market or find the products your competitors sell and you do not.

## Setup

The server uses the Streamable HTTP transport and OAuth 2.1 sign-in. Add the server URL to your client and start the sign-in. The client opens the DeepScout console. Sign in with Google or GitHub; your first sign-in creates your account.

### Claude Code

```bash
claude mcp add --transport http deepscout https://mcp.deepscout.ai/mcp
```

Start a session, run `/mcp`, select **deepscout**, then **Authenticate**.

### Claude (claude.ai and Claude Desktop)

Open **Settings → Connectors** and click **Add custom connector**. Enter the name `DeepScout` and the server URL, then click **Connect**.

### Cursor

Add to `~/.cursor/mcp.json` or `.cursor/mcp.json` in your project:

```json
{
  "mcpServers": {
    "deepscout": {
      "url": "https://mcp.deepscout.ai/mcp"
    }
  }
}
```

Open **Cursor Settings → MCP** and click **Connect** next to deepscout.

### Codex

```bash
codex mcp add deepscout --url https://mcp.deepscout.ai/mcp
```

If Codex does not open the sign-in, run `codex mcp login deepscout`.

### VS Code

Add to `.vscode/mcp.json` in your workspace:

```json
{
  "servers": {
    "deepscout": {
      "type": "http",
      "url": "https://mcp.deepscout.ai/mcp"
    }
  }
}
```

Click **Start** above the server in the file.

### Clients with local (stdio) servers only

```json
{
  "mcpServers": {
    "deepscout": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.deepscout.ai/mcp"]
    }
  }
}
```

`mcp-remote` needs Node.js 18 or later.

### With an API key instead of the sign-in

Create a key in the [console](https://console.deepscout.ai) under **Settings → API keys** and send it in the `Authorization` header:

```bash
claude mcp add --transport http deepscout https://mcp.deepscout.ai/mcp \
  --header "Authorization: Bearer $SCOUT_API_KEY"
```

A key for one project keeps the agent inside that project.

## Tools

| Tool | What it does |
|---|---|
| `scout_list_projects` | Lists your projects and the customer that pays for each one. |
| `scout_create_project` | Creates a project, e.g. for another client or market. |
| `scout_list_websites` | Lists the websites of a project with their current job and full scrape estimate. |
| `scout_analyze_website` | Adds a website to a project. The setup job analyzes it and scrapes free sample products. |
| `scout_get_sample` | Gives a temporary URL of the sample products of a website. |
| `scout_scrape_website` | Creates a scraping job for the full catalog or for a list of product URLs. |
| `scout_get_url_results` | Gives the result of each URL of a URL scrape. |
| `scout_match_websites` | Creates a matching job, or the free matching sample of the project. |
| `scout_list_jobs` | Lists the newest jobs of a kind in a project. |
| `scout_get_job` | Reads a setup, scraping or matching job: status, progress, estimate and charge. |
| `scout_approve_job` | Approves a scraping or matching job. This charges your credit. |
| `scout_cancel_job` | Cancels a job that waits for approval. Nothing is charged. |
| `scout_list_exports` | Lists the export files of a project. |
| `scout_get_export` | Gives a temporary URL of one export file. |
| `scout_get_balance` | Reads your credit: total, held and spendable. |

Only `scout_approve_job` charges your credit. The server tells the agent to show you the price and wait for your answer first. Keep your client's confirmation prompt on for this tool. The read-only tools (`scout_list_*`, `scout_get_*`) carry the `readOnlyHint` annotation.

## Example prompts

Change the values in brackets.

**Add a competitor and check the sample**

> Add `[https://www.example-shop.com]` to my DeepScout project `[Default project]`. The market is `[DE]` and the currency is `[EUR]`. Wait until the setup job is complete. Then download the sample and tell me which fields are often empty or look wrong. Do not start a paid scrape.

**Match my products with competitors**

> In my DeepScout project `[Default project]`, `[my-shop.com]` is my website and `[competitor-a.com, competitor-b.com]` are my competitors. Run the free matching sample first. When it is complete, show me 10 matched pairs from the export. Then create the full matching, show me the price and wait for my approval.

**Compare my prices with the market**

> Get the matches export of my DeepScout project `[Default project]` and the product export of each website in it. For each of my products that has at least one competitor, compare my price with the lowest competitor price. List the 20 products with the largest difference in percent.

**Find gaps in my assortment**

> Get the matches export of my DeepScout project `[Default project]`. My website is `[my-shop.com]`. List the products that two or more competitors sell and my website does not, with the lowest price. Sort them by the number of competitors.

More prompts are in the [documentation](https://docs.deepscout.ai/api/mcp).

## Pricing

Usage-based, paid from prepaid credit. The same prices as the API:

- Website analysis and sample data: 10 shops a month free, then €0.50 per analysis
- Scraping: from €0.40 per 1,000 products
- Matching: €7 per 1,000 products, with a free matching sample first

See [deepscout.ai/dev](https://deepscout.ai/en/dev) for details.

## Notes

- Jobs run at a rate that each e-shop accepts. A scrape of 100,000 products can take a day. You can close the conversation and ask about the job later.
- The agent cannot top up credit. Top up in the [console](https://console.deepscout.ai).

## Links

- Documentation: [docs.deepscout.ai/api/mcp](https://docs.deepscout.ai/api/mcp)
- REST API: [docs.deepscout.ai](https://docs.deepscout.ai)
- Website: [deepscout.ai/dev](https://deepscout.ai/en/dev)
- Support: [support@deepscout.ai](mailto:support@deepscout.ai)
