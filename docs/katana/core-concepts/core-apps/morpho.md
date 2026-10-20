---
title: "DeFi Lending on Katana: Borrow and Lend with Morpho"
description: "DeFi lending on Katana Network: how to lend, borrow against crypto, and earn through curated vaults with Morpho, Katana's core lending app."
seo_target: "defi lending (1,300 global/mo), borrow against crypto (350), morpho lending (200)."
---

# DeFi Lending on Katana: Borrow and Lend with Morpho

Morpho is Katana's core lending and borrowing app. You can lend assets to earn interest, borrow against the crypto you hold, or deposit into curated vaults that put your assets to work across lending markets for you.

You can find lending opportunities on the [Earn page](https://app.katana.network/earn) of the Katana app.

## How Morpho Works on Katana

Morpho offers two ways to take part:

- **Lending markets:** each market pairs one asset that can be borrowed with one asset used as collateral. Lenders supply assets to a market and earn interest from borrowers. Borrowers deposit collateral and borrow against it, up to a limit set by the market.
- **Vaults:** instead of choosing markets yourself, you deposit into a vault. The vault's curator spreads deposits across lending markets and manages them on your behalf, so you earn lending yield without managing individual positions.

### Curators

Morpho vaults on Katana are managed by independent risk curators, including **Gauntlet**, **Steakhouse Financial** and **Clearstar**. Curators choose which markets a vault lends to and set its risk limits, such as which collateral is accepted and how much can be borrowed against it.

Curators do not hold user funds and have no control over Katana. Their decisions do affect how a vault performs, so it is worth understanding who curates a vault before you deposit.

## How Morpho Connects to Katana

Morpho is one of Katana's core apps, so it is built into how the network works rather than being just another app deployed on it.

- **Chain-Owned Liquidity:** Katana uses part of its network revenue to build liquidity that the network owns and keeps deployed in its core markets, including lending. This permanent liquidity helps keep borrowing rates steady during volatile periods. See [Chain-Owned Liquidity](../chain-owned-liquidity.md).
- **Vault Bridge:** when you bridge supported assets to Katana through Vault Bridge, the underlying assets are deposited into Morpho vaults on Ethereum to earn yield. The vbTokens you receive on Katana, such as vbUSDC, can then be used across Katana DeFi, including Morpho's lending markets. See [Vault Bridge](../vault-bridge.md).
- **KAT incentives:** 250 million KAT from Katana's liquidity mining allocation is set aside for Morpho to distribute to its users. See [Tokenomics & Schedules](../what-is-kat/tokenomics.md).

## Borrowing Against Your Crypto

Borrowing lets you access funds without selling the assets you hold. You deposit collateral, borrow up to the market's limit, and pay interest on what you borrow.

If the value of your collateral falls, or the value of what you borrowed rises, your position can become unsafe. When it crosses the market's limit, part of your collateral can be sold to repay the loan. This is called **liquidation**. Keeping your borrowing well below the limit gives your position room to absorb price moves.

!!! warning "Lending and borrowing have risks"
    Borrowers can be liquidated if their collateral loses value, including if a stablecoin or bridged asset loses its peg. Lenders and vault depositors are exposed to smart contract risk and to the decisions of third-party curators. See [Risk](../risk.md) for more on the risks of using DeFi on Katana.

## For Developers

- [Contract Addresses](../../technical-reference/contract-addresses.md#morpho): Morpho contract addresses on Katana.
- [Install Katana Skills](../../get-started/install-katana-skills.md): the **lending** skill teaches AI agents to read Morpho markets and vaults and manage lending positions on Katana.
