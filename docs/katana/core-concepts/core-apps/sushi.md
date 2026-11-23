---
title: "Katana Swap: Swaps and Liquidity on Katana (Built on Sushi)"
description: "Katana Swap is Katana's decentralized exchange, built on Sushi: how swaps and liquidity pools work, how Katana Swap revenue funds KAT buybacks and Chain-Owned Liquidity, and how to earn as a liquidity provider."
seo_target: "sushi katana (350 global/mo), crypto share of a mixed search."
---

# Katana Swap: Swaps and Liquidity on Katana

Katana Swap is Katana's decentralized exchange (DEX), built on Sushi's (SushiSwap) deployment on Katana. It is where tokens are swapped on Katana and where liquidity providers supply the liquidity that makes those swaps possible.

Katana Swap is one of the venues Katana owns and operates, so a share of the revenue it generates stays with the network.

You can swap tokens directly in the [Katana app](https://app.katana.network).

## How Katana Swap Works

Katana Swap uses liquidity pools. Instead of matching buyers with sellers, each pool holds a pair of tokens supplied by liquidity providers, and swaps are made against that pool. Liquidity providers earn a share of the trading fees in return.

Katana Swap offers two types of pools:

- **Concentrated liquidity pools (V3):** you choose the price range your liquidity covers. This puts your liquidity to work where trading actually happens, but it only earns while the market price is inside your range.
- **Full-range pools (V2):** your liquidity covers every possible price. It is simpler to manage, since it never goes out of range, but it is less capital efficient.

## How Swaps Are Routed

When you swap in the Katana app, the trade is routed through [Everlong](https://router.foreverlong.io/swap-api/guide). Everlong finds a route for your trade across Katana's liquidity pools, which can include several hops, and the Everlong router contract executes it in a single transaction.

## How Katana Swap Connects to Katana

Katana Swap is one of Katana's core venues, so it is built into how the network works rather than being just another app deployed on it.

- **Rewards for KAT stakers:** a share of Katana Swap trading fees funds open-market KAT buybacks, and 100% of the bought-back KAT is compounded into avKAT. See [How KAT Staking Works](../../katana-staking.md).
- **Chain-Owned Liquidity:** Katana uses part of its network revenue to build liquidity that the network owns and keeps deployed in its core markets, including spot trading. This gives Katana Swap a base of liquidity that doesn't depend only on outside liquidity providers. See [Chain-Owned Liquidity](../chain-owned-liquidity.md).
- **KAT incentives:** 400 million KAT from Katana's liquidity mining allocation is set aside for liquidity providers on Katana Swap. See [Tokenomics & Schedules](../what-is-kat/tokenomics.md).
- **Security:** Sushi is a member of the Katana DeFi Security Council, which can veto admin proposals and respond to emergencies. See [Governance](../governance.md).

## Earning as a Liquidity Provider

Liquidity providers on Katana Swap can earn in two ways:

1. **Trading fees** from swaps made against your pool.
2. **KAT incentives**, if you stake your liquidity position in the Katana app and your pool is selected for incentives by the Katana Foundation.

For concentrated liquidity positions, only liquidity that is **in range** earns trading fees and KAT incentives. If the price moves outside your range, your position stops earning until the price comes back or you rebalance it.

- [Stake Your Liquidity Position](../../stake-lp.md): how to stake a position and how KAT rewards work.
- [Rebalance Your Staked LP Position](../../how-to/lp-rebalance-guide.md): how to move an out-of-range position back around the current price.

!!! warning "Providing liquidity has risks"
    Liquidity providers can lose value when token prices move (often called impermanent loss), and positions can earn less than expected if they go out of range. See [Risk](../risk.md) for more on the risks of using DeFi on Katana.

## For Developers

- [Contract Addresses](../../technical-reference/contract-addresses.md#katana-swap): Katana Swap and [Everlong Router](../../technical-reference/contract-addresses.md#everlong-router) contract addresses.

### Swapping Through Everlong

Swap routes and quotes are computed offchain by the Everlong Swap API. The router contract only executes the route it is given, so there are two ways to integrate:

- **Use the Everlong Swap API (recommended).** The API returns ready-to-send transaction data with the route, hops and minimum amounts already worked out. It requires an API key, which you can request by following the [Everlong Swap API guide](https://router.foreverlong.io/swap-api/guide#id-1.-obtain-an-api-key).
- **Call the router contract directly.** This works without an API key, but you must supply your own route and minimum amounts, so you need your own routing logic.

To swap against a single pool with no routing, you can use the Katana Swap `V3SwapRouter` and `V3QuoterV2` contracts directly.

!!! warning "Don't hardcode the router address"
    The Everlong router address can change when Everlong updates its router. Fetch the current address from [router.foreverlong.io](https://router.foreverlong.io).
- [Install Katana Skills](../../get-started/install-katana-skills.md): the **dex** skill teaches AI agents to quote and make swaps, analyze pools and provide liquidity.
