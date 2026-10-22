---
title: "Safety & Sustainability"
description: "How Katana Network manages risk, protects the network through governance safeguards, and keeps yield sustainable over the long term."
---

# Safety & Sustainability

This section covers how Katana protects its users, how changes to the network are controlled, and how Katana's yield is designed to last.

## Is Katana Safe?

Every DeFi network carries risk, and Katana is open about where those risks are. The main types are:

- **Smart contract risk:** bugs or exploits in the code that powers Katana.
- **Protocol risk:** failures in the core network.
- **Third-party risk:** decisions made by independent partners, such as the curators who manage vaults.
- **Duration risk:** delays when withdrawing, especially during periods of stress.
- **Liquidation risk:** losses when collateral drops in value or loses its peg.

To reduce these risks, Katana's core parts have been independently audited. Agglayer, the KAT token and Vault Bridge were reviewed by security firms including SigmaPrime, Certora and ChainSecurity, with formal verification for the KAT token and Vault Bridge. Katana also builds on the OP Stack and Succinct's SP1 prover, which have their own public audits.

- [Katana Risk Framework](../risk.md): each type of risk and the safeguards in place for it.
- [Katana Security Overview](../../additional-resources/security-overview.md): security practices and the full list of audits, with links to the public reports.

## How Changes Are Controlled

No single team can change Katana on its own. Katana uses a two-tier system that separates proposing changes from approving them:

- **Katana Admin:** a 3-of-5 multisig that proposes all major changes to the network, such as bridge and contract upgrades.
- **10-day timelock:** every proposal waits 10 days before it can take effect, giving the ecosystem time to review it and react.
- **DeFi Security Council:** a 10-of-13 multisig of DeFi protocols and infrastructure teams operating on Katana. It can veto any proposal before it takes effect and can step in during an emergency.

This lets Katana improve quickly while making sure every change is checked by independent partners.

- [Katana Governance](../governance.md): how the Admin and Security Council work, and who the members are.

## Built to Last

Katana's yield comes from real economic activity rather than relying only on token rewards. Its main sources are:

- **Vault Bridge:** assets bridged to Katana keep earning yield, which is directed back into Katana's liquidity pools.
- **AUSD:** Katana's native stablecoin brings in yield from outside the blockchain, backed by U.S. Treasury exposure.
- **Sequencer fees:** fees from transactions on Katana help build Chain-Owned Liquidity.
- **Core app fees:** a portion of fees from Katana's core apps is used to boost liquidity pools.

Because Chain-Owned Liquidity belongs to the network, it stays deployed even when other liquidity leaves. This helps keep borrowing rates steady and trading smooth during downturns, and the yield it generates can be used to reward the users who stay.

- [Real Yield in DeFi: How Katana Sustains Yield](../sustainability.md): how Katana's yield scales with activity and holds up across market cycles.
