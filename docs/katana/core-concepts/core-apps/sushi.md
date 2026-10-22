---
title: "Sushi on Katana: Swaps and Liquidity"
description: "Sushi is Katana Network's core decentralized exchange: how swaps and liquidity pools work, how Sushi connects to Chain-Owned Liquidity and KAT incentives, and how to earn as a liquidity provider."
seo_target: "sushi katana (350 global/mo), crypto share of a mixed search."
---

# Sushi on Katana: Swaps and Liquidity

Sushi (SushiSwap) is Katana's core decentralized exchange (DEX). It is where tokens are swapped on Katana and where liquidity providers supply the liquidity that makes those swaps possible.

You can swap tokens directly in the [Katana app](https://app.katana.network).

## How Sushi Works on Katana

Sushi uses liquidity pools. Instead of matching buyers with sellers, each pool holds a pair of tokens supplied by liquidity providers, and swaps are made against that pool. Liquidity providers earn a share of the trading fees in return.

Sushi on Katana offers two types of pools:

- **Concentrated liquidity pools (V3):** you choose the price range your liquidity covers. This puts your liquidity to work where trading actually happens, but it only earns while the market price is inside your range.
- **Full-range pools (V2):** your liquidity covers every possible price. It is simpler to manage, since it never goes out of range, but it is less capital efficient.

## How Sushi Connects to Katana

Sushi is one of Katana's core apps, so it is built into how the network works rather than being just another app deployed on it.

- **Chain-Owned Liquidity:** Katana uses part of its network revenue to build liquidity that the network owns and keeps deployed in its core markets, including spot trading. This gives Sushi a base of liquidity that doesn't depend only on outside liquidity providers. See [Chain-Owned Liquidity](../chain-owned-liquidity.md).
- **KAT incentives:** 400 million KAT from Katana's liquidity mining allocation is set aside for Sushi to distribute to its users. See [Tokenomics & Schedules](../what-is-kat/tokenomics.md).
- **Gauge voting:** vKAT holders vote on which liquidity pools receive KAT incentives each epoch, so the community decides where rewards go. See [How KAT Staking & Voting Work](../../katana-staking.md#gauge-voting-incentives-rewards).
- **Security:** Sushi is a member of the Katana DeFi Security Council, which can veto admin proposals and respond to emergencies. See [Governance](../governance.md).

## Earning as a Liquidity Provider

Liquidity providers on Sushi can earn in two ways:

1. **Trading fees** from swaps made against your pool.
2. **KAT incentives**, if you stake your liquidity position in the Katana app and your pool receives incentives through gauge voting.

For concentrated liquidity positions, only liquidity that is **in range** earns trading fees and KAT incentives. If the price moves outside your range, your position stops earning until the price comes back or you rebalance it.

- [Stake Your Liquidity Position](../../stake-lp.md): how to stake a position and how KAT rewards work.
- [Rebalance Your Staked LP Position](../../how-to/lp-rebalance-guide.md): how to move an out-of-range position back around the current price.

!!! warning "Providing liquidity has risks"
    Liquidity providers can lose value when token prices move (often called impermanent loss), and positions can earn less than expected if they go out of range. See [Risk](../risk.md) for more on the risks of using DeFi on Katana.

## For Developers

- [Contract Addresses](../../technical-reference/contract-addresses.md#sushi): Sushi contract addresses on Katana.
- [Install Katana Skills](../../get-started/install-katana-skills.md): the **dex** skill teaches AI agents to quote and make swaps, analyze pools and provide liquidity on Sushi.