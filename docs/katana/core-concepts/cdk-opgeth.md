---
title: "What Is Agglayer? How Katana Is Built on It"
description: "Katana Network's architecture: a DeFi-first Ethereum Layer 2 built on Agglayer's CDK OP Stack, using op-geth for execution and ZK proofs to settle to Ethereum."
---

# What Is Agglayer? How Katana Is Built on It

Katana is an Ethereum Layer 2 built using [Agglayer's CDK OP Stack](https://docs.agglayer.dev/cdk/cdk-opgeth/architecture/). The stack combines the familiar execution environment of Geth and the OP Stack with zero-knowledge proofs for settlement and native connectivity to Agglayer. This gives Katana an Ethereum-compatible execution environment while using ZK proofs to validate state transitions before they are finalized on Ethereum.

## **How Katana's Stack Works**

Katana uses **op-geth**, an OP Stack implementation based on Geth, as its execution client. This means developers building on Katana get an environment that closely follows Ethereum's existing tooling, smart contract standards, and EVM infrastructure.

Although Katana uses OP Stack technology for execution, it is not an optimistic rollup. Katana uses zero-knowledge validity proofs to prove the correctness of state transitions and settle them to Ethereum.

## **Agglayer Connectivity**

Katana is natively connected to Agglayer, an interoperability layer designed to connect Ethereum Layer 2 networks and enable assets and messages to move between them. Katana uses Agglayer's Unified Bridge as its canonical bridge to Ethereum and other connected networks. Agglayer also uses a **Pessimistic Proof** to verify cross-chain accounting and ensure that one connected chain cannot improperly withdraw assets belonging to another.

Together, Katana's execution proofs and Agglayer's cross-chain proofs provide ZK-based verification for both the state of the Katana network and its interactions with the broader Agglayer ecosystem.

## **Why This Architecture Matters**

Katana's architecture combines three important properties:

- **Ethereum compatibility** through Geth and the OP Stack
- **ZK-secured settlement** through validity proofs verified on Ethereum
- **Native interoperability** through Agglayer and its Unified Bridge

For users and developers, this means Katana can maintain a familiar Ethereum development environment while benefiting from faster ZK-based finality and built-in connectivity to Ethereum and other Agglayer-connected networks.
