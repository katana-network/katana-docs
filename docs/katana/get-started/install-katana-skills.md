# Install Katana Skills

Katana Skills teach AI agents how to interact with DeFi on Katana Network.
Unlike the [MCP server](./setup-mcp-server.md) which gives agents access to
hosted tools, skills are reference guides that live alongside your code. They
teach your agent about contract addresses, function signatures, workflows, safety
rules, and common mistakes so it can build and reason about on-chain operations
directly.

Skills work with any agent framework that talks to EVM chains: Claude Code,
OpenClaw, LangChain, CrewAI, or your own stack.

## What Are Skills?

Each skill is a structured markdown file (`SKILL.md`) that covers a specific
area of Katana DeFi. When your agent loads a skill, it gains the knowledge it
needs to work with that protocol. No API keys, no server, no dependencies.

| Skill | What It Covers |
|-------|---------------|
| **wallet-manager** | Balances, transfers, approvals, wrap/unwrap ETH |
| **dex** | Token swaps, quotes, pool analysis, LP provision on SushiSwap |
| **lending** | Morpho markets, vaults, positions, leverage loops |
| **merkl** | Reward discovery, yield farming, claiming Merkl rewards |
| **analytics** | Token prices, gas costs, transaction lookup, contract reference |
| **perps** | Perpetual futures, market data, orders, positions, withdrawals |
| **kat** | KAT staking (vKAT), auto-compounding vault (avKAT), gauge voting |

## Prerequisites

- **Node.js** version 22 or higher — download from
  [nodejs.org](https://nodejs.org)

Verify your install:

```bash
node --version
```

The version must be >= 22.0.0.

## Install All Skills

One command installs every skill in the collection:

```bash
npx skills add https://github.com/katana-network/katana-skills
```

This downloads the full Katana Skills Hub into your project. Your agent will
automatically pick up the skills it needs based on what you ask it to do.

## Choose Your Setup

Pick the section below that matches the tool you're using:

- [Claude Code (CLI)](#claude-code-cli)
- [Claude Code (VS Code & JetBrains)](#claude-code-vs-code-jetbrains)
- [Claude Code — Manual Install](#claude-code-manual-install)
- [OpenClaw](#openclaw)
- [LangChain / CrewAI / Custom Frameworks](#langchain-crewai-custom-frameworks)

## Claude Code (CLI)

### Install via the skills command

Run this from your project directory:

```bash
npx skills add https://github.com/katana-network/katana-skills
```

This creates a `skills/` folder in your project with all Katana skills. Claude
Code will automatically detect and use them when relevant to your prompts.

### Verify the installation

Ask Claude Code something that would activate a skill:

- "What are the current KAT token prices?" (activates **analytics**)
- "How do I swap WETH for KAT on SushiSwap?" (activates **dex**)
- "Show me the Morpho lending markets on Katana" (activates **lending**)

If the skills are loaded correctly, Claude will reference the contract addresses,
function signatures, and workflows from the skill files in its response.

### Management

```bash
# View installed skills
ls skills/

# Update to the latest version
npx skills add https://github.com/katana-network/katana-skills

# Remove a specific skill
rm -rf skills/dex

# Remove all Katana skills
rm -rf skills/
```

## Claude Code (VS Code & JetBrains)

Claude Code extensions use the same skills directory as the CLI. Open a terminal
in your IDE and run:

```bash
npx skills add https://github.com/katana-network/katana-skills
```

The skills will be available in the Claude Code sidebar panel. Any skill
installed via the CLI is automatically picked up by the extension.

**VS Code**: Install the "Claude Code" extension from the VS Code Marketplace.

**JetBrains**: Install the Claude Code plugin from the JetBrains Marketplace.

## Claude Code — Manual Install

If you prefer not to use the `npx skills` command, you can download the skill
files directly and add them to your project by hand.

### 1. Download the skills

Go to the
[Katana Skills Hub repository](https://github.com/katana-network/katana-skills)
and click **Code > Download ZIP**. Extract the ZIP file somewhere on your
machine.

### 2. Copy the skills folder

Inside the extracted folder you'll find a `skills/` directory containing all the
skill folders. Copy the entire `skills/` folder into your project root:

```
your-project/
├── skills/
│   ├── analytics/
│   │   └── SKILL.md
│   ├── dex/
│   │   └── SKILL.md
│   ├── kat/
│   │   └── SKILL.md
│   ├── lending/
│   │   └── SKILL.md
│   ├── merkl/
│   │   └── SKILL.md
│   ├── perps/
│   │   └── SKILL.md
│   └── wallet-manager/
│       └── SKILL.md
└── ... your other project files
```

You can also pick and choose — only copy the skill folders you need.

### 3. Verify in Claude Code

Open Claude Code in the same project directory. The skills will be automatically
detected. Test it by asking something like "How do I swap tokens on Katana?" and
Claude should reference the skill content in its response.

!!! note
    To update manually installed skills, download the latest ZIP from the
    repository and replace the files in your `skills/` folder.

## OpenClaw

OpenClaw supports the same skills format natively. From your project directory:

```bash
npx skills add https://github.com/katana-network/katana-skills
```

OpenClaw will automatically discover the skills in the `skills/` folder and
activate them based on user prompts.

## LangChain / CrewAI / Custom Frameworks

Skills are plain markdown files — any framework can read and use them. After
installing:

```bash
npx skills add https://github.com/katana-network/katana-skills
```

Each skill lives in its own folder under `skills/` with a `SKILL.md` file. You
can load these files into your agent's context however your framework handles
system prompts or reference documents.

### Example: Loading a skill into your agent's context

```python
# Read a skill file and include it in your agent's system prompt
with open("skills/dex/SKILL.md", "r") as f:
    dex_skill = f.read()

system_prompt = f"""
You are a DeFi assistant for Katana Network.

{dex_skill}
"""
```

```typescript
// Read a skill file in Node.js
import { readFileSync } from "fs";

const dexSkill = readFileSync("skills/dex/SKILL.md", "utf-8");

const systemPrompt = `
You are a DeFi assistant for Katana Network.

${dexSkill}
`;
```

Each `SKILL.md` includes YAML frontmatter with a `name` and `description` field
that you can use to decide which skills to load based on the user's request.

## What Each Skill Teaches Your Agent

### Wallet-Manager

Covers core wallet operations on Katana: checking ETH and token balances,
transferring tokens, wrapping and unwrapping ETH (which on Katana produces
yield-generating vbETH), and managing ERC-20 approvals. Includes known token
addresses and decimals for all major tokens on the network.

### Dex

Everything your agent needs to work with SushiSwap on Katana — V3 concentrated
liquidity and V2 full-range pools. Covers token swaps, getting trade quotes,
analyzing pool liquidity, and providing LP positions. Includes fee tiers, tick
spacing, and all relevant contract addresses.

### Lending

Teaches your agent about Morpho Blue on Katana — direct lending markets,
MetaMorpho vaults, position management, and leveraged looping strategies.
Includes market parameters, health factor calculations, and safety thresholds.

### Merkl

Covers the Merkl reward distribution system on Katana. Your agent learns how to
discover incentivized DeFi opportunities, check reward accruals, and build claim
transactions. Includes campaign types, reward timing, and the Distributor
contract.

### Analytics

Read-only market data and chain utilities — token prices, gas estimation,
transaction debugging, and contract reference lookups. A good starting point for
building dashboards or monitoring tools.

### Perps

Perpetual futures trading on Katana Perps — a non-custodial, cross-margined
exchange with a central limit order book. Covers market data, funding rates,
order placement, position management, and withdrawals. Includes up to 50x
leverage across available markets.

### KAT

The KAT token ecosystem — staking KAT to receive vKAT (non-transferable
governance NFTs), depositing into the avKAT auto-compounding vault, gauge
voting for reward allocation, and exit mechanics including cooldown periods and
fees.

## Skills vs MCP Server

Both give your agent access to Katana DeFi, but they work differently:

| | Skills | MCP Server |
|---|--------|-----------|
| **What it is** | Reference docs your agent reads | Hosted tools your agent calls |
| **How it works** | Agent builds transactions using contract knowledge | Agent calls pre-built tools via API |
| **Requires** | Node.js 22+ (for install only) | Nothing (hosted at `mcp.katana.network`) |
| **Best for** | Building custom integrations, deep protocol knowledge | Quick queries, market data, prototyping |
| **Framework support** | Any agent framework | MCP-compatible clients |

You can use both together, the MCP server for quick data queries and skills for
building more complex on-chain interactions.

## Troubleshooting

**"npx skills" command not found**: Make sure Node.js 22+ is installed. Run
`node --version` to check.

**Skills not activating in Claude Code**: Make sure the `skills/` folder is in
your project root (the same directory you run Claude Code from). Try asking a
specific question like "How do I swap tokens on Katana?" to trigger skill
activation.

**Permission errors on install**: On macOS/Linux, you may need to check folder
permissions. Make sure you have write access to your project directory.

**Outdated skills**: Re-run `npx skills add https://github.com/katana-network/katana-skills`
to pull the latest version.
