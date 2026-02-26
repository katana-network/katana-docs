# Stake KAT to vKAT

This tutorial walks you through staking KAT tokens to receive a vKAT NFT, which
grants you voting power in the Katana governance system.

## Goal

By the end of this tutorial, you'll be able to:

- Approve the VotingEscrow contract to spend your KAT
- Create a vKAT lock (soulbound NFT)
- Read your lock details and voting power
- Understand how multiple locks work

## Prerequisites

- KAT tokens in your wallet
- Basic understanding of ERC-20 approvals
If you're new to the KAT ecosystem, read the
[KAT Token Overview](kat-token-overview.md) first.

## How Staking Works

When you stake KAT, the VotingEscrow contract locks your tokens and mints a
soulbound NFT (vKAT) to your wallet. This NFT represents your locked position
and carries voting power proportional to the amount of KAT locked.

The staking process is a two-step operation:

1. **Approve** — Allow the VotingEscrow contract to transfer your KAT
2. **Create Lock** — Call `createLock()` to lock KAT and receive your vKAT NFT

## Contract Addresses

```typescript
const KAT_ADDRESS = "0x7f1f4b4b29f5058fa32cc7a97141b8d7e5abdc2d";
const VOTING_ESCROW = "0x4d6fC15Ca6258b168225D283262743C623c13Ead";
```

## Set Up viem Clients

```typescript
import {
  createPublicClient,
  createWalletClient,
  http,
  parseEther,
  formatEther,
  defineChain,
} from "viem";
import { privateKeyToAccount } from "viem/accounts";

const katana = defineChain({
  id: 747474,
  name: "Katana",
  nativeCurrency: { name: "Ether", symbol: "ETH", decimals: 18 },
  rpcUrls: {
    default: { http: ["https://rpc.katana.network"] },
  },
  blockExplorers: {
    default: { name: "Katanascan", url: "https://katanascan.com" },
  },
});

const account = privateKeyToAccount(process.env.PRIVATE_KEY as `0x${string}`);

const publicClient = createPublicClient({
  chain: katana,
  transport: http(),
});

const walletClient = createWalletClient({
  account,
  chain: katana,
  transport: http(),
});
```

## ABIs

??? note "KAT Token ABI (click to expand)"

    ```typescript
    const katAbi = [
      {
        name: "balanceOf",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "account", type: "address" }],
        outputs: [{ type: "uint256" }],
      },
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
    ] as const;
    ```

??? note "VotingEscrow ABI (click to expand)"

    ```typescript
    const votingEscrowAbi = [
      {
        name: "createLock",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [{ name: "_value", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "createLockFor",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "_value", type: "uint256" },
          { name: "_to", type: "address" },
        ],
        outputs: [{ type: "uint256" }],
      },
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
        name: "votingPower",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "ownedTokens",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "owner", type: "address" }],
        outputs: [{ type: "uint256[]" }],
      },
      {
        name: "merge",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "_from", type: "uint256" },
          { name: "_to", type: "uint256" },
        ],
        outputs: [],
      },
      {
        name: "split",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "_from", type: "uint256" },
          { name: "_amount", type: "uint256" },
        ],
        outputs: [{ type: "uint256" }],
      },
    ] as const;
    ```

## Step 1: Check Your KAT Balance

Before staking, verify you have enough KAT:

```typescript
const balance = await publicClient.readContract({
  address: KAT_ADDRESS,
  abi: katAbi,
  functionName: "balanceOf",
  args: [account.address],
});

console.log(`KAT Balance: ${formatEther(balance)}`);
```

## Step 2: Approve KAT for Staking

The VotingEscrow contract needs permission to transfer your KAT:

```typescript
const stakeAmount = parseEther("1000"); // 1,000 KAT

const approvalHash = await walletClient.writeContract({
  address: KAT_ADDRESS,
  abi: katAbi,
  functionName: "approve",
  args: [VOTING_ESCROW, stakeAmount],
});

await publicClient.waitForTransactionReceipt({ hash: approvalHash });
console.log("Approval confirmed");
```

## Step 3: Create the Lock

Now stake your KAT to receive a vKAT NFT:

```typescript
const stakeHash = await walletClient.writeContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "createLock",
  args: [stakeAmount],
});

const receipt = await publicClient.waitForTransactionReceipt({
  hash: stakeHash,
});
console.log("Lock created!", receipt);
```

To create a lock on behalf of another address:

```typescript
const stakeForHash = await walletClient.writeContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "createLockFor",
  args: [stakeAmount, recipientAddress],
});
```

## Step 4: Read Your Position

Query your lock details and voting power:

```typescript
// Get all vKAT token IDs you own
const tokenIds = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "ownedTokens",
  args: [account.address],
});

console.log(`You own ${tokenIds.length} vKAT NFTs: ${tokenIds}`);

// Get lock details for a specific token
const tokenId = tokenIds[0];
const [lockedAmount, lockStart] = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "locked",
  args: [tokenId],
});

console.log(`Locked Amount: ${formatEther(lockedAmount)} KAT`);
console.log(`Lock Start: ${new Date(Number(lockStart) * 1000)}`);

// Get voting power
const votingPower = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "votingPower",
  args: [tokenId],
});

console.log(`Voting Power: ${formatEther(votingPower)}`);
```

## Managing Multiple Locks

You can create multiple vKAT NFTs, each representing a separate lock position.
This is useful if you want to stake additional KAT at different times.

### Merge Locks

Combine two vKAT NFTs into one:

```typescript
const mergeHash = await walletClient.writeContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "merge",
  args: [fromTokenId, toTokenId],
});

await publicClient.waitForTransactionReceipt({ hash: mergeHash });
console.log(`Merged lock #${fromTokenId} into #${toTokenId}`);
```

### Split a Lock

Create a new vKAT NFT by splitting KAT from an existing lock:

```typescript
const splitHash = await walletClient.writeContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "split",
  args: [sourceTokenId, parseEther("500")],
});

const splitReceipt = await publicClient.waitForTransactionReceipt({
  hash: splitHash,
});
console.log("Lock split!", splitReceipt);
```

## Using wagmi (React)

If you're building a React frontend, here's the pattern using wagmi hooks:

```typescript
import { useWriteContract, useWaitForTransactionReceipt } from "wagmi";
import { parseEther } from "viem";

const KAT_ADDRESS = "0x7f1f4b4b29f5058fa32cc7a97141b8d7e5abdc2d";
const VOTING_ESCROW = "0x4d6fC15Ca6258b168225D283262743C623c13Ead";

function StakeKAT() {
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading, isSuccess } = useWaitForTransactionReceipt({ hash });

  // Step 1: Approve
  const handleApprove = () => {
    writeContract({
      address: KAT_ADDRESS,
      abi: katAbi,
      functionName: "approve",
      args: [VOTING_ESCROW, parseEther("1000")],
    });
  };

  // Step 2: Create Lock (call after approval succeeds)
  const handleStake = () => {
    writeContract({
      address: VOTING_ESCROW,
      abi: votingEscrowAbi,
      functionName: "createLock",
      args: [parseEther("1000")],
    });
  };

  return (
    <div>
      <button onClick={handleApprove} disabled={isPending}>
        {isPending ? "Approving..." : "Approve KAT"}
      </button>
      <button onClick={handleStake} disabled={isPending || !isSuccess}>
        {isLoading ? "Staking..." : "Stake KAT"}
      </button>
    </div>
  );
}
```

## What's Next

Now that you have a vKAT NFT with voting power, you can:

- [Vote on Gauges](kat-vote-on-gauges.md) — Direct incentives to liquidity pools
- [Convert vKAT to avKAT](kat-convert-vkat-to-avkat.md) — Deposit your NFT into
  the avKAT vault
- [Claim Voting Incentives](kat-claim-rewards.md) — Collect your earned rewards
