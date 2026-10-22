---
title: "Core Apps on Katana: Trading, Lending and Perps"
description: "Katana Network's core apps: Sushi for swaps and liquidity, Morpho for lending and borrowing, and Katana Perps for perpetual futures, and how they connect to the network."
---

# Core Apps on Katana: Trading, Lending and Perps

Katana is built around a small set of core markets that most of DeFi depends on: spot trading, lending and borrowing, and perpetual futures. Each of these markets is served by one core app.

| App | What you can do | Where |
|---|---|---|
| [Sushi](sushi.md) | Swap tokens and provide liquidity | [Katana app](https://app.katana.network) |
| [Morpho](morpho.md) | Lend, borrow, and earn with curated vaults | [Katana app Earn page](https://app.katana.network/earn) |
| [Katana Perps](../katana-perps.md) | Trade perpetual futures with leverage | [perps.katana.network](https://perps.katana.network) |

Other apps on Katana can build on and connect to these shared markets. This keeps liquidity concentrated in a few deep markets instead of spread thinly across many separate venues.

The core apps are independent. They are deployed on Katana but are not owned, operated or controlled by Katana.

## What Makes Core Apps Different

Core apps are connected to how Katana itself works, rather than being just another app deployed on the network:

- **Chain-Owned Liquidity:** Katana uses part of its network revenue to build liquidity that the network owns and keeps deployed in its core markets. This gives the core apps a base of liquidity that doesn't depend only on outside providers. See [Chain-Owned Liquidity](../chain-owned-liquidity.md).
- **KAT incentives:** 1 billion KAT is set aside for the core apps to distribute to their users: 400 million KAT for Sushi, 250 million KAT for Morpho, and 350 million KAT for perps, launchpad and yield apps. See [Tokenomics & Schedules](../what-is-kat/tokenomics.md).
- **Gauge voting:** vKAT holders vote on which liquidity pools receive KAT incentives, so the community decides where rewards go. See [How KAT Staking & Voting Work](../../katana-staking.md#gauge-voting-incentives-rewards).
- **Fees back into the network:** a portion of core app fees is used to boost liquidity pools on Katana. As more people use the core apps, more yield flows back into the ecosystem. See [Real Yield in DeFi](../sustainability.md).

## Explore the Core Apps

- **[Sushi on Katana: Swaps and Liquidity](sushi.md):** how swaps and liquidity pools work, and how to earn as a liquidity provider.
- **[DeFi Lending on Katana: Borrow and Lend with Morpho](morpho.md):** lending markets, curated vaults, and borrowing against your crypto.
- **[Perpetual Futures on Katana: Katana Perps Explained](../katana-perps.md):** how onchain perps work, trading features, and developer tools.
