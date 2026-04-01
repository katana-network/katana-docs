# Set Up the Katana MCP Server

The Katana MCP Server gives AI agents direct access to the Katana Network DeFi
ecosystem. Once connected, your AI tool can query token prices, analyze Morpho
lending markets, inspect perpetual futures orderbooks, explore liquidity pools,
check reward campaigns, and pull real-time market data. All through natural
language.

This makes it a powerful tool for building products on Katana. Whether you're
prototyping a DeFi dashboard, researching pool analytics for a trading bot,
or building an AI-powered portfolio tracker, the MCP server gives your AI
assistant the context it needs to help you ship faster.

It works with Claude Desktop, Claude Code, Cursor, Windsurf, Cline, and any
MCP-compatible client.

## Choose Your Setup

Katana hosts the MCP server for you at `https://mcp.katana.network/mcp`  No
local software required. Just point your client at the URL and you're ready to
go.

Pick the section below that matches the tool you're using:

- [Claude Code (CLI)](#claude-code-cli)
- [Claude Code (VS Code & JetBrains)](#claude-code-vs-code-jetbrains)
- [Claude Desktop](#claude-desktop)
- [Claude Web (claude.ai)](#claude-web-claudeai)
- [Cursor](#cursor)
- [Windsurf](#windsurf)
- [Cline](#cline-vs-code-extension)
- [Custom Clients & OpenAI-Compatible Agents](#custom-clients-openai-compatible-agents)

## Claude Code (CLI)

One command is all you need:

```bash
claude mcp add --transport katana-mcp http https://mcp.katana.network/mcp
```

This installs the server for the current project. To make it available
everywhere:

```bash
claude mcp add --transport -s user katana-mcp http https://mcp.katana.network/mcp
```

### Management commands

```bash
# List all configured MCP servers
claude mcp list

# Inspect a specific server
claude mcp get katana-mcp

# Remove a server
claude mcp remove katana-mcp
```

## Claude Code (VS Code & JetBrains)

The Claude Code extensions for VS Code and JetBrains use the same MCP
configuration as the CLI. Open a terminal in your IDE and run:

```bash
claude mcp add katana-mcp --transport http --url https://mcp.katana.network/mcp
```

The server will automatically be available in the Claude Code sidebar panel.

**VS Code**: Install the "Claude Code" extension from the VS Code Marketplace.

**JetBrains**: Install the Claude Code plugin from the JetBrains Marketplace.

## Claude Desktop

### 1. Locate the configuration file

Claude Desktop stores MCP server configurations in a JSON file:

- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

If the file does not exist, create it manually. Make sure the directory exists
first.

### 2. Add the Katana MCP server

Open the configuration file and add (or merge) the following:

```json
{
  "mcpServers": {
    "katana-mcp": {
      "type": "http",
      "url": "https://mcp.katana.network/mcp"
    }
  }
}
```

### 3. Restart Claude Desktop

After saving the configuration file, fully quit and reopen Claude Desktop. The
MCP server will not load until the application is restarted. You should see the
Katana MCP tools appear in the tools menu (hammer icon).

### 4. Verify the connection

Start a new conversation and ask Claude something like "Get the current gas
price on Katana" or "What are the KAT token prices?". If the server is
connected, Claude will call the appropriate tool and return results.

### Log files

If the server fails to start, check the MCP logs:

- **macOS**: `~/Library/Logs/Claude/mcp*.log`
- **Windows**: `%APPDATA%\Claude\logs\mcp*.log`

## Claude Web (claude.ai)

Claude Web supports MCP servers through connectors. No local software is
required.

1. Go to [claude.ai](https://claude.ai) and open **Settings**
2. Navigate to **Connectors** (or **Integrations**)
3. Click **Add custom connector**
4. Enter the server URL: `https://mcp.katana.network/mcp`
5. Save and start a new conversation

The Katana MCP tools should now be available in your chat.

## Cursor

### Configuration file location

- **Project-level**: `.cursor/mcp.json` (version controlled)
- **Global**: `~/.cursor/mcp.json`

### Configuration

Create or edit the MCP configuration file:

```json
{
  "mcpServers": {
    "katana-mcp": {
      "type": "http",
      "url": "https://mcp.katana.network/mcp"
    }
  }
}
```

### Verify in Cursor

1. Open Cursor Settings (`Cmd/Ctrl + ,`)
2. Navigate to the MCP section
3. You should see "katana-mcp" listed with a green status indicator
4. If it shows red, check the logs for errors

## Windsurf

### Configuration file location

`~/.windsurf/mcp.json` or project-level `.windsurf/mcp.json`

### Configuration

```json
{
  "mcpServers": {
    "katana-mcp": {
      "type": "http",
      "url": "https://mcp.katana.network/mcp"
    }
  }
}
```

Open Windsurf's AI panel to verify — the Katana MCP tools should appear in the
available tools list.

## Cline (VS Code Extension)

### Via the settings UI

1. Open VS Code and go to the Cline extension settings
2. Find the "MCP Servers" section
3. Click "Add Server"
4. Set the type to `http` and the URL to `https://mcp.katana.network/mcp`

### Via JSON config

Edit `cline_mcp_settings.json`:

```json
{
  "mcpServers": {
    "katana-mcp": {
      "type": "http",
      "url": "https://mcp.katana.network/mcp"
    }
  }
}
```

## Custom Clients & OpenAI-Compatible Agents

The Katana MCP server is available as a remote HTTP endpoint. Any MCP-compatible
client can connect to it directly.

### Node.js example

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StreamableHTTPClientTransport } from "@modelcontextprotocol/sdk/client/streamableHttp.js";

const transport = new StreamableHTTPClientTransport(
  new URL("https://mcp.katana.network/mcp")
);

const client = new Client({
  name: "my-agent",
  version: "1.0.0"
});

await client.connect(transport);

// List available tools
const tools = await client.listTools();
console.log(tools);

// Call a tool
const result = await client.callTool({
  name: "get_token_prices",
  arguments: { tokens: "WETH,KAT" }
});
console.log(result);
```

### Python example

```python
from mcp import ClientSession
from mcp.client.streamable_http import streamablehttp_client

async with streamablehttp_client("https://mcp.katana.network/mcp") as (read, write, _):
    async with ClientSession(read, write) as session:
        await session.initialize()

        # List tools
        tools = await session.list_tools()

        # Call a tool
        result = await session.call_tool(
            "get_token_prices",
            arguments={"tokens": "WETH,KAT"}
        )
        print(result)
```

## What Can You Build?

With the Katana MCP server connected, your AI assistant has access to 60+ tools
covering the full Katana DeFi ecosystem. Here are some ideas to get started:

- **DeFi dashboards** — pull real-time token prices, pool analytics, and lending
  market data to power frontend displays
- **Trading bots** — query perpetual futures orderbooks, funding rates, and
  ticker data to inform automated strategies
- **Portfolio trackers** — read wallet balances, staking positions, and reward
  accruals across protocols
- **Yield analyzers** — compare Morpho lending rates, LP pool yields, and Merkl
  reward campaigns
- **Risk monitors** — track liquidation levels, position health, and market
  conditions

Ask your AI assistant to explore the available tools — try "List all the tools
you have for Katana" to see what's possible.

## Troubleshooting

**Tools not appearing in Claude Desktop**: Verify the JSON syntax in
`claude_desktop_config.json` is valid. Make sure you fully quit and reopened
Claude Desktop (not just closed the window). Check the MCP logs for errors.

**Connection errors**: Make sure you can reach `https://mcp.katana.network/mcp`
from your network. If you're behind a corporate firewall or VPN, you may need to
allowlist this domain.

**Tools not loading in Cursor/Windsurf**: Double-check that the `type` is set to
`http` and the `url` is correct. Restart the application after saving the config.

## Advanced: Enabling Transaction Signing

The setup above uses the MCP server in read-only mode, which is all you need
for building products, querying data, and prototyping. If you want the server to
also build transactions (swaps, staking, lending, etc.), you can provide a wallet
private key as a query parameter when connecting to the hosted server.

!!! warning
    **Use at your own risk.** Providing a private key gives the MCP server the
    ability to build transactions on behalf of your wallet. Never share your
    private key or commit it to version control. Use a dedicated wallet with
    limited funds. Consider testing on the Bokuto testnet (chain ID 737373)
    before using mainnet. The server returns unsigned transactions and does not
    broadcast them automatically, but you are solely responsible for any
    transactions you sign and submit.
