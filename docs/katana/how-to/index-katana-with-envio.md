# Index Katana smart contract data in minutes using Envio

**Note:** This guide was created by the Envio team. If you notice anything outdated, please reach the Envio team on [Discord](https://discord.gg/envio) or [Telegram](https://t.me/+kAIGElzPjApiMjI0).

## Intro

This guide walks you through building a HyperIndex indexer for a smart contract on Katana. By the end you will have a local indexer that streams Katana blockchain data into a Postgres database and serves it through a GraphQL API.

## What you will build

An indexer for VaultBridge Bridged USDC (vbUSDC), an ERC20 token on Katana. It tracks `Transfer` and `Approval` events, keeps a running token balance per account, and records every approval.

**Target contract**

* **Chain**: Katana, chain ID `747474`
* **Contract address**: `0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36`
* **Explorer**: [katanascan.com](https://katanascan.com/address/0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36)
* **Docs**: [Envio overview](https://docs.envio.dev/) | [Katana on Envio](https://docs.envio.dev/docs/HyperSync/hypersync-supported-networks)

## Steps at a glance

1. Scaffold the indexer
2. Add your API token
3. Configure the indexer for Katana
4. Define the data schema
5. Write the event handler
6. Generate types
7. Run the indexer
8. Query your data
9. Stop the indexer

## Before you begin

Make sure you have the following installed. Run each check in a terminal:

* **Node.js v22 or later**. Check with `node -v`.
* **pnpm v8 or later**. Check with `pnpm -v`.
* **Docker** installed and running. Check with `docker ps`.
* **A free Envio API token**. HyperIndex v3 requires an API token to use HyperSync as a data source. Create one at [envio.dev/app/api-tokens](https://envio.dev/app/api-tokens).

## Step 1: Scaffold the indexer

Create a new HyperIndex project from the ERC20 template, then move into the project folder:

```bash
pnpx envio init template -t erc20 -d ./katana-indexer --api-token ""
cd katana-indexer
```

A new `katana-indexer` folder is created with `config.yaml`, `schema.graphql`, a `.env` file, and a `src/handlers/` directory. In v3, every file in `src/handlers/` is registered automatically, so there is no central `EventHandlers.ts` file. Generated types are written to the `.envio/` directory.

Docs: [Envio CLI reference](https://docs.envio.dev/docs/HyperIndex/cli-commands)

## Step 2: Add your API token

Open the `.env` file in the project and set your Envio API token:

```bash
ENVIO_API_TOKEN=your-token-here
```

**Note:** A token is required. Without a valid token, the indexer stops immediately with the error *"An API token is required for using HyperSync as a data-source."* Create a free token at [envio.dev/app/api-tokens](https://envio.dev/app/api-tokens).

## Step 3: Configure the indexer for Katana

Replace the contents of `config.yaml` with the configuration below. This points the indexer at Katana and the target contract, and selects the two events to index:

```yaml
# yaml-language-server: $schema=./node_modules/envio/evm.schema.json
name: katana-indexer
description: Katana ERC20 indexer

contracts:
  - name: MyContract
    events:
      - event: Transfer(address indexed from, address indexed to, uint256 value)
      - event: Approval(address indexed owner, address indexed spender, uint256 value)

chains:
  - id: 747474 # Katana
    start_block: 0
    hypersync_config:
      url: https://747474.hypersync.xyz
    contracts:
      - name: MyContract
        address:
          - "0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36"
```

Docs: [Configuration file](https://docs.envio.dev/docs/HyperIndex/configuration-file) | [Full schema reference](https://docs.envio.dev/docs/HyperIndex/configuration-file) | [Katana endpoint](https://docs.envio.dev/docs/HyperSync/hypersync-supported-networks)

## Step 4: Define the data schema

Replace the contents of `schema.graphql` with the schema below. Each type becomes a database table and a GraphQL query:

```graphql
type Account {
  id: ID!
  balance: BigInt!
  approvals: [Approval!]! @derivedFrom(field: "owner")
}

type Approval {
  id: ID!
  owner: Account!
  spender: String!
  value: BigInt!
  blockNumber: BigInt!
  timestamp: BigInt!
}
```

The `@derivedFrom` directive creates a virtual reverse lookup, so each `Account` exposes its list of approvals without you storing that list explicitly.

Docs: [Schema reference](https://docs.envio.dev/docs/HyperIndex/schema)

## Step 5: Write the event handler

The ERC20 template ships a handler and a test file that both reference the template contract. Remove them first:

```bash
rm -f src/handlers/ERC20.ts src/indexer.test.ts
```

**Note:** Removing `src/indexer.test.ts` matters. It imports the deleted ERC20 handler, so leaving it in place breaks `pnpm test`.

Now create a new handler file at `src/handlers/MyContract.ts` with the following content:

```typescript
import { indexer } from "envio";

indexer.onEvent(
  { contract: "MyContract", event: "Transfer" },
  async ({ event, context }) => {
    const { from, to, value } = event.params;

    const sender = await context.Account.get(from);
    context.Account.set({
      id: from,
      balance: (sender?.balance ?? 0n) - value,
    });

    const receiver = await context.Account.get(to);
    context.Account.set({
      id: to,
      balance: (receiver?.balance ?? 0n) + value,
    });
  },
);

indexer.onEvent(
  { contract: "MyContract", event: "Approval" },
  async ({ event, context }) => {
    const { owner, spender, value } = event.params;

    const existing = await context.Account.get(owner);
    context.Account.set({
      id: owner,
      balance: existing?.balance ?? 0n,
    });

    context.Approval.set({
      id: `${event.block.hash}-${event.logIndex}`,
      owner_id: owner,
      spender,
      value,
      blockNumber: BigInt(event.block.number),
      timestamp: BigInt(event.block.timestamp),
    });
  },
);
```

Key points about the HyperIndex handler API:

* Handlers are registered with `indexer.onEvent`, imported from the `envio` package. (In v3 this replaces the per-contract `Contract.Event.handler` registration used in v2.)
* Linked entities are set with the `<field>_id` convention, so the `owner` relation is written as `owner_id`.
* Event metadata is available on `event.block` (`hash`, `number`, `timestamp`) and `event.logIndex`.

Docs: [Event handlers](https://docs.envio.dev/docs/HyperIndex/event-handlers) | [What is new in v3](https://docs.envio.dev/docs/HyperIndex/v3-migration-guide)

## Step 6: Generate types

Generate the typed code from your config and schema:

```bash
pnpm codegen
```

The command reads `config.yaml` and `schema.graphql`, writes typed code into `.envio/`, and exits with no errors. Run this again any time you change the config or schema.

Docs: [Envio CLI reference](https://docs.envio.dev/docs/HyperIndex/cli-commands)

## Step 7: Run the indexer

Start the indexer in development mode:

```bash
pnpm dev
```

Envio starts Postgres and Hasura in Docker, then begins streaming Katana blocks through HyperSync. Indexed data appears within seconds. Leave this terminal running.

You can explore the data in the [Envio Console](https://envio.dev/app).

Docs: [Running locally](https://docs.envio.dev/docs/HyperIndex/running-locally)

## Step 8: Query your data

With the indexer still running, open a new terminal and query the GraphQL API. This asks for the top 3 accounts by balance and the 3 most recent approvals:

```bash
curl -s -X POST http://localhost:8080/v1/graphql \
  -H "Content-Type: application/json" \
  -d '{"query":"{ Account(limit: 3, order_by: { balance: desc }) { id balance } Approval(limit: 3, order_by: { blockNumber: desc }) { id spender value blockNumber } }"}'
```

Example response (formatted for readability, one row of each shown):

```json
{
  "data": {
    "Account": [
      {
        "id": "0x5F88Eeb3A5662489eB2D5DA9f0c73F03355F3009",
        "balance": "16871664813864"
      }
    ],
    "Approval": [
      {
        "id": "0xf7e98cf8fcd43c2196172f034fdf50088b8e49dd30e73ff83e2939ec22b4f73e-1",
        "spender": "0x628d684D57c73A5D8cA77F455Fdf2CC8Bd503c16",
        "value": "8049212954",
        "blockNumber": "12597860"
      }
    ]
  }
}
```

You get back JSON with `Account` and `Approval` rows. If the response is empty, wait a few seconds for the indexer to sync further and run the query again.

## Step 9: Stop the indexer

Stop `pnpm dev` with `Ctrl+C` in its terminal, then clean up the local environment:

```bash
pnpm envio stop
```

**Important:** This stops the Docker containers and removes the local database. Do not use `docker compose down`. HyperIndex v3 manages its containers directly, so `docker compose down` fails.

## Troubleshooting

* **"Failed to automatically find HyperSync endpoint for the chain 747474"** — the `hypersync_config` block is missing from `config.yaml`. See [Step 3](#step-3-configure-the-indexer-for-katana).
* **"An API token is required for using HyperSync as a data-source"** — set `ENVIO_API_TOKEN` in `.env`. See [Step 2](#step-2-add-your-api-token).
* **The indexer resumes but makes no progress (progress shows `-1`)** — the database holds stale state from an earlier aborted run. Run `pnpm envio dev -r` to wipe the database and re-index from scratch.

## Get support

Have a question or run into something this guide didn't cover? The Envio team is active and always happy to help:

* [Discord](https://discord.gg/envio)
* [Telegram](https://t.me/envio_indexer)

## Additional resources

* [HyperIndex overview](https://docs.envio.dev/docs/HyperIndex/overview)
* [Katana on Envio](https://docs.envio.dev/docs/HyperSync/hypersync-supported-networks)
* [Configuration file](https://docs.envio.dev/docs/HyperIndex/configuration-file)
* [Schema reference](https://docs.envio.dev/docs/HyperIndex/schema)
* [Event handlers](https://docs.envio.dev/docs/HyperIndex/event-handlers)
* [Envio CLI reference](https://docs.envio.dev/docs/HyperIndex/cli-commands)
* [Running locally](https://docs.envio.dev/docs/HyperIndex/running-locally)
* [Envio Console](https://envio.dev/app)
* [What is new in v3](https://docs.envio.dev/docs/HyperIndex/v3-migration-guide)
* [GitHub](https://github.com/enviodev) 
