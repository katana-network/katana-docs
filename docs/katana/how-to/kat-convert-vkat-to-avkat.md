# Convert vKAT to avKAT

This tutorial shows you how to convert an existing vKAT NFT (direct staking)
into avKAT vault tokens (vault-based staking).

## Goal

By the end of this tutorial, you'll be able to:

- Approve your vKAT NFT for vault deposit
- Deposit a vKAT NFT into the avKAT vault
- Understand what happens to your lock during conversion

## Prerequisites

- An existing vKAT NFT (see [Stake KAT to vKAT](kat-stake-to-vkat.md))

## Why Convert?

You might want to convert from vKAT to avKAT if:

- You would like to automate the voting logic of your choice
- You want a transferable token you can use in DeFi
- You want to consolidate into the vault without creating new NFTs each time
- You want to exit your position instantly via a DEX

!!! warning
    Converting is a one-way operation. Once your vKAT NFT is deposited into the
    vault, it is consumed. You receive avKAT tokens in return. To get a vKAT NFT
    back, you would need to withdraw from the vault, which creates a new NFT with a new token ID.

## Contract Addresses

```typescript
const VOTING_ESCROW = "0x4d6fC15Ca6258b168225D283262743C623c13Ead";
const NFT_LOCK = "0x106F7D67Ea25Cb9eFf5064CF604ebf6259Ff296d";
const VAULT = "0x7231dbaCdFc968E07656D12389AB20De82FbfCeB";
```

## ABIs

??? note "VotingEscrow ABI (click to expand)"

    ```typescript
    const votingEscrowAbi = [
      {
        name: "locked",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [
          { name: "amount", type: "uint256" },
          { name: "start", type: "uint256" },
        ],
      },
      {
        name: "ownedTokens",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "owner", type: "address" }],
        outputs: [{ type: "uint256[]" }],
      },
    ] as const;
    ```

??? note "NFT Lock ABI (click to expand)"

    ```typescript
    const nftLockAbi = [
      {
        name: "approve",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "to", type: "address" },
          { name: "tokenId", type: "uint256" },
        ],
        outputs: [],
      },
    ] as const;
    ```

??? note "avKAT Vault ABI (click to expand)"

    ```typescript
    const vaultAbi = [
      {
        name: "depositTokenId",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "tokenId", type: "uint256" },
          { name: "receiver", type: "address" },
        ],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "convertToAssets",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "shares", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "convertToShares",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "assets", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
    ] as const;
    ```

## Step 1: Check Your vKAT NFTs

List your existing locks:

```typescript
import { formatEther } from "viem";

// Get all vKAT token IDs you own
const tokenIds = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "ownedTokens",
  args: [account.address],
});

console.log(`You own ${tokenIds.length} vKAT NFTs`);

// Get details for each lock
for (const id of tokenIds) {
  const [amount, start] = await publicClient.readContract({
    address: VOTING_ESCROW,
    abi: votingEscrowAbi,
    functionName: "locked",
    args: [id],
  });
  console.log(`Lock #${id}: ${formatEther(amount)} KAT (started ${new Date(Number(start) * 1000).toLocaleDateString()})`);
}
```

## Step 2: Preview the Conversion

Check the exchange rate and expected tokens before converting:

```typescript
const tokenId = tokenIds[0]; // Replace with your chosen token ID

// Get the locked amount in this NFT
const [lockedAmount] = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "locked",
  args: [tokenId],
});

// Check current exchange rate
const exchangeRate = await publicClient.readContract({
  address: VAULT,
  abi: vaultAbi,
  functionName: "convertToAssets",
  args: [parseEther("1")],
});
console.log(`Current rate: 1 avKAT = ${formatEther(exchangeRate)} KAT`);

// Preview: how many tokens for your NFT's locked amount
const expectedTokens = await publicClient.readContract({
  address: VAULT,
  abi: vaultAbi,
  functionName: "convertToShares",
  args: [lockedAmount],
});
console.log(`Expected tokens: ${formatEther(expectedTokens)} avKAT`);
```

## Step 3: Approve the NFT for the Vault

The vault needs permission to take custody of your vKAT NFT:

```typescript
const approvalHash = await walletClient.writeContract({
  address: NFT_LOCK,
  abi: nftLockAbi,
  functionName: "approve",
  args: [VAULT, tokenId],
});

await publicClient.waitForTransactionReceipt({ hash: approvalHash });
console.log("NFT approved for vault");
```

## Step 4: Deposit the NFT

```typescript
const depositHash = await walletClient.writeContract({
  address: VAULT,
  abi: vaultAbi,
  functionName: "depositTokenId",
  args: [tokenId, account.address],
});

const receipt = await publicClient.waitForTransactionReceipt({
  hash: depositHash,
});
console.log(`Converted vKAT #${tokenId} to avKAT tokens!`, receipt);
```

## What Happens During Conversion

When you deposit a vKAT NFT into the vault:

1. The vault takes custody of your NFT
2. The locked KAT from your NFT is merged into the vault's master position
3. You receive avKAT tokens proportional to the KAT locked in your NFT
4. The original NFT is consumed — it no longer exists in your wallet

The number of avKAT tokens you receive depends on the current exchange rate
between avKAT and KAT at the time of deposit.

## What's Next

- [Vote on Gauges](kat-vote-on-gauges.md) — If you still have other vKAT NFTs,
  use their voting power
- [Unstake and Exit](kat-unstake-and-exit.md) — Learn how to exit avKAT back to
  KAT
