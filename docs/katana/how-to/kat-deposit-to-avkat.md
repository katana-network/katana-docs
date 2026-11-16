---
description: "Stake KAT to avKAT: a developer walkthrough for depositing KAT into the avKAT vault, reading the exchange rate, previewing deposits and exiting through the 7-day cooldown."
---

# Stake KAT to avKAT

This tutorial walks you through depositing KAT into the avKAT vault to receive
avKAT, Katana's yield-bearing staking token.

## Goal

By the end of this tutorial, you'll be able to:

- Deposit KAT into the avKAT vault (ERC-4626)
- Understand the avKAT exchange rate
- Read your avKAT position and underlying value
- Preview deposits before executing them

## Prerequisites

- KAT tokens in your wallet
- Basic understanding of ERC-20 approvals and ERC-4626 vaults
If you're new to the KAT ecosystem, read the
[KAT Token Overview](kat-token-overview.md) first.

## How the avKAT Vault Works

The avKAT vault is an [ERC-4626 tokenized vault](https://eips.ethereum.org/EIPS/eip-4626).
When you deposit KAT, the vault:

1. Locks your KAT into the VotingEscrow as part of the vault's master position
2. Issues you avKAT tokens representing your proportional ownership of the vault

A share of the revenue from Katana Swap, Katana Perps, Vault Bridge and
sequencer fees is used to buy KAT on the open market. 100% of that KAT is
compounded into the vault every epoch through the CompoundStrategy contract.
There is nothing to claim.

## Contract Addresses

```typescript
const KAT_ADDRESS = "0x7f1f4b4b29f5058fa32cc7a97141b8d7e5abdc2d";
const VAULT_ADDRESS = "0x7231dbaCdFc968E07656D12389AB20De82FbfCeB";
```

## ABIs

??? note "KAT Token ABI (click to expand)"

    ```typescript
    const katAbi = [
      {
        name: "approve",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "spender", type: "address" },
          { name: "amount", type: "uint256" },
        ],
        outputs: [{ type: "bool" }],
      },
      {
        name: "allowance",
        type: "function",
        stateMutability: "view",
        inputs: [
          { name: "owner", type: "address" },
          { name: "spender", type: "address" },
        ],
        outputs: [{ type: "uint256" }],
      },
    ] as const;
    ```

??? note "avKAT Vault ABI (click to expand)"

    ```typescript
    const vaultAbi = [
      {
        name: "deposit",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "assets", type: "uint256" },
          { name: "receiver", type: "address" },
        ],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "previewDeposit",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "assets", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "previewRedeem",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "shares", type: "uint256" }],
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
      {
        name: "balanceOf",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "account", type: "address" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "totalAssets",
        type: "function",
        stateMutability: "view",
        inputs: [],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "redeem",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "shares", type: "uint256" },
          { name: "receiver", type: "address" },
          { name: "owner", type: "address" },
        ],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "withdrawTokenId",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "assets", type: "uint256" },
          { name: "receiver", type: "address" },
          { name: "owner", type: "address" },
        ],
        outputs: [{ type: "uint256" }],
      },
    ] as const;
    ```

## Step 1: Preview Your Deposit

Before depositing, check how many avKAT tokens you'll receive:

```typescript
import { parseEther, formatEther } from "viem";

const depositAmount = parseEther("1000"); // 1,000 KAT

// Preview how many tokens you'll get
const expectedTokens = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "previewDeposit",
  args: [depositAmount],
});

console.log(`Depositing ${formatEther(depositAmount)} KAT`);
console.log(`Expected avKAT tokens: ${formatEther(expectedTokens)}`);

// Check current exchange rate (how much KAT 1 avKAT is worth)
const exchangeRate = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "convertToAssets",
  args: [parseEther("1")], // 1 avKAT
});

console.log(`Exchange rate: 1 avKAT = ${formatEther(exchangeRate)} KAT`);
```

## Step 2: Approve KAT for the Vault

```typescript
const approvalHash = await walletClient.writeContract({
  address: KAT_ADDRESS,
  abi: katAbi,
  functionName: "approve",
  args: [VAULT_ADDRESS, depositAmount],
});

await publicClient.waitForTransactionReceipt({ hash: approvalHash });
console.log("Approval confirmed");
```

## Step 3: Deposit into the Vault

```typescript
const depositHash = await walletClient.writeContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "deposit",
  args: [depositAmount, account.address],
});

const receipt = await publicClient.waitForTransactionReceipt({
  hash: depositHash,
});
console.log("Deposited!", receipt);
```

## Step 4: Read Your Position

```typescript
// Get your avKAT token balance
const tokens = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "balanceOf",
  args: [account.address],
});

// Convert tokens to underlying KAT value
const underlyingKat = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "convertToAssets",
  args: [tokens],
});

console.log(`avKAT Tokens: ${formatEther(tokens)}`);
console.log(`Underlying KAT value: ${formatEther(underlyingKat)}`);
```

## Understanding the Exchange Rate

The exchange rate between avKAT and KAT reflects the vault's total assets
relative to outstanding tokens. You can query the current rate on-chain:

```typescript
// Convert tokens to underlying KAT value
const katValue = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "convertToAssets",
  args: [parseEther("100")], // 100 avKAT
});
console.log(`100 avKAT = ${formatEther(katValue)} KAT`);

// Convert KAT amount to expected tokens
const tokensNeeded = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "convertToShares",
  args: [parseEther("100")], // 100 KAT
});
console.log(`100 KAT = ${formatEther(tokensNeeded)} avKAT`);
```

The exchange rate changes as bought-back KAT is compounded into the vault's master
position. The rate at any given time reflects the vault's accounting state.
Always verify onchain before transacting.

## Vault Stats

You can read protocol-level vault information:

```typescript
// Total KAT locked in the vault
const totalAssets = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "totalAssets",
});

console.log(`Total vault assets: ${formatEther(totalAssets)} KAT`);
```

## Exiting avKAT

There are two ways to exit your avKAT position:

### Option 1: Swap on a DEX (Instant)

avKAT is an ERC-4626 vault token, which means it implements the full ERC-20
interface. It is transferable, tradeable, and compatible with any DEX that has
liquidity. This gives you an instant exit with no cooldown, but the swap
happens at the market rate and may involve trading fees, price impact and
slippage.

### Option 2: Unstake Through the Vault (7-Day Cooldown)

You can redeem avKAT through the vault. This creates a vKAT NFT for the
underlying KAT, which you then withdraw through the 7-day exit cooldown. If you
wait the full 7 days there is no exit fee. Exiting earlier costs a fee that
starts at 25% and goes down every day (including an instant exit for a 25%
fee).

```typescript
// Preview how much KAT you'd get for your tokens
const tokensToRedeem = parseEther("100");
const katAmount = await publicClient.readContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "previewRedeem",
  args: [tokensToRedeem],
});
console.log(`Redeeming ${formatEther(tokensToRedeem)} avKAT = ${formatEther(katAmount)} KAT`);

// Redeem as a new vKAT NFT (then follow the unstaking process)
const withdrawHash = await walletClient.writeContract({
  address: VAULT_ADDRESS,
  abi: vaultAbi,
  functionName: "withdrawTokenId",
  args: [katAmount, account.address, account.address],
});

const withdrawReceipt = await publicClient.waitForTransactionReceipt({
  hash: withdrawHash,
});
console.log("Redeemed as vKAT NFT!", withdrawReceipt);
```

For the full exit process from vKAT, see
[Unstake and Exit](kat-unstake-and-exit.md).

## Using wagmi (React)

```typescript
import { useReadContract, useWriteContract } from "wagmi";
import { parseEther, formatEther } from "viem";

const VAULT = "0x7231dbaCdFc968E07656D12389AB20De82FbfCeB";
const KAT = "0x7f1f4b4b29f5058fa32cc7a97141b8d7e5abdc2d";

function DepositToVault() {
  // Preview deposit
  const { data: expectedTokens } = useReadContract({
    address: VAULT,
    abi: vaultAbi,
    functionName: "previewDeposit",
    args: [parseEther("1000")],
  });

  const { writeContract, data: hash, isPending } = useWriteContract();

  const handleApprove = () => {
    writeContract({
      address: KAT,
      abi: katAbi,
      functionName: "approve",
      args: [VAULT, parseEther("1000")],
    });
  };

  const handleDeposit = () => {
    writeContract({
      address: VAULT,
      abi: vaultAbi,
      functionName: "deposit",
      args: [parseEther("1000"), account],
    });
  };

  return (
    <div>
      <p>Expected tokens: {expectedTokens && formatEther(expectedTokens)}</p>
      <button onClick={handleApprove}>Approve</button>
      <button onClick={handleDeposit}>Deposit</button>
    </div>
  );
}
```

## What's Next

- [Convert vKAT to avKAT](kat-convert-vkat-to-avkat.md) — Already have a vKAT
  NFT? Deposit it into the vault
- [Unstake and Exit](kat-unstake-and-exit.md) — Learn about the full exit
  process and fee schedule
