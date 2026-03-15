# Vote on Gauges

This tutorial shows you how to use your vKAT voting power to vote on gauges,
directing incentive emissions to liquidity pools on Katana.

## Goal

By the end of this tutorial, you'll be able to:

- Understand how gauge voting works
- View available gauges and their current vote allocations
- Cast votes using your vKAT voting power
- Reset and change your votes

## Prerequisites

- A vKAT NFT with voting power (see [Stake KAT to vKAT](kat-stake-to-vkat.md))

## How Gauge Voting Works

Gauge voting is the mechanism by which vKAT holders direct KAT incentive
emissions to specific liquidity pools. The more votes a gauge receives, the more
rewards flow to its associated pool.

Key concepts:

- **Gauges** represent liquidity pools that can receive incentive emissions
- **Weights** are specified in basis points (BPS) where 10,000 = 100%
- Your vote allocations must sum to 10,000 BPS (100%) or less
- Votes are **persistent** — they stay active until you change or reset them
- Only vKAT holders can vote directly; the vault's CompoundStrategy contract
  handles voting for the vault's master position

!!! note
    avKAT holders do not vote directly. The vault's CompoundStrategy
    handles the relay votes for the master vKAT position. If you want direct
    control over your votes, use vKAT.

## Contract Addresses

```typescript
const VOTING_ESCROW = "0x4d6fC15Ca6258b168225D283262743C623c13Ead";
const GAUGE_VOTER = "0x5e755A3C5dc81A79DE7a7cEF192FFA60964c9352";
```

## ABIs

??? note "VotingEscrow ABI (click to expand)"

    ```typescript
    const votingEscrowAbi = [
      {
        name: "votingPower",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "isVoting",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [{ type: "bool" }],
      },
    ] as const;
    ```

??? note "GaugeVoter ABI (click to expand)"

    ```typescript
    const gaugeVoterAbi = [
      {
        name: "vote",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          { name: "tokenId", type: "uint256" },
          {
            name: "votes",
            type: "tuple[]",
            components: [
              { name: "gauge", type: "address" },
              { name: "weight", type: "uint256" },
            ],
          },
        ],
        outputs: [],
      },
      {
        name: "reset",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [],
      },
      {
        name: "getAllGauges",
        type: "function",
        stateMutability: "view",
        inputs: [],
        outputs: [{ type: "address[]" }],
      },
      {
        name: "gaugeVotes",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "gauge", type: "address" }],
        outputs: [{ type: "uint256" }],
      },
      {
        name: "usedVotingPower",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [{ type: "uint256" }],
      },
    ] as const;
    ```

## Step 1: View Available Gauges

```typescript
import { formatEther } from "viem";

// Get all gauges
const gauges = await publicClient.readContract({
  address: GAUGE_VOTER,
  abi: gaugeVoterAbi,
  functionName: "getAllGauges",
});

console.log(`Total gauges: ${gauges.length}`);

// Check votes on each gauge
for (const gauge of gauges) {
  const votes = await publicClient.readContract({
    address: GAUGE_VOTER,
    abi: gaugeVoterAbi,
    functionName: "gaugeVotes",
    args: [gauge],
  });
  console.log(`Gauge ${gauge}: ${formatEther(votes)} total votes`);
}
```

## Step 2: Check Your Voting Power

```typescript
const tokenId = 42n; // Your vKAT NFT token ID

// Get voting power
const votingPower = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "votingPower",
  args: [tokenId],
});
console.log(`Your voting power: ${formatEther(votingPower)}`);

// Check if already voting
const isVoting = await publicClient.readContract({
  address: VOTING_ESCROW,
  abi: votingEscrowAbi,
  functionName: "isVoting",
  args: [tokenId],
});
console.log(`Currently voting: ${isVoting}`);

// Check how much voting power is already allocated
const usedPower = await publicClient.readContract({
  address: GAUGE_VOTER,
  abi: gaugeVoterAbi,
  functionName: "usedVotingPower",
  args: [tokenId],
});
console.log(`Used voting power: ${formatEther(usedPower)}`);
```

## Step 3: Cast Your Votes

Allocate your voting power across one or more gauges. Weights are in basis
points (BPS), where 10,000 = 100%.

```typescript
const gaugeAddress1 = "0x..."; // First gauge address
const gaugeAddress2 = "0x..."; // Second gauge address

const voteHash = await walletClient.writeContract({
  address: GAUGE_VOTER,
  abi: gaugeVoterAbi,
  functionName: "vote",
  args: [
    tokenId,
    [
      { gauge: gaugeAddress1, weight: 6000n }, // 60% of voting power
      { gauge: gaugeAddress2, weight: 4000n }, // 40% of voting power
    ],
  ],
});

await publicClient.waitForTransactionReceipt({ hash: voteHash });
console.log("Votes cast successfully!");
```

## Step 4: Reset Votes

To clear all your current votes (for example, before reallocating or
withdrawing):

```typescript
const resetHash = await walletClient.writeContract({
  address: GAUGE_VOTER,
  abi: gaugeVoterAbi,
  functionName: "reset",
  args: [tokenId],
});

await publicClient.waitForTransactionReceipt({ hash: resetHash });
console.log("Votes reset");
```

!!! warning
    You must reset your votes before beginning a withdrawal from vKAT. If your
    vKAT is actively voting, the withdrawal will fail. See
    [Unstake and Exit](kat-unstake-and-exit.md) for the combined
    `resetVotesAndBeginWithdrawal` flow.

## Voting Strategy Tips

- **Research gauges** — Look at which pools have the most liquidity and trading
  volume. Voting for high-activity pools can attract more depositors and generate
  more trading fees.
- **Diversify** — Spreading votes across multiple gauges reduces your dependency
  on any single pool.
- **Monitor results** — After each epoch, check if your preferred gauges
  received enough votes to generate meaningful rewards.
- **Votes persist** — Once cast, your votes carry forward each epoch. You only
  need to update them if you want to change your allocation.

## Using wagmi (React)

```typescript
import { useReadContract, useWriteContract, useWaitForTransactionReceipt } from "wagmi";
import { formatEther } from "viem";

const GAUGE_VOTER = "0x5e755A3C5dc81A79DE7a7cEF192FFA60964c9352";

function GaugeVoting({ tokenId }: { tokenId: bigint }) {
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading, isSuccess } = useWaitForTransactionReceipt({ hash });

  // Read available gauges
  const { data: gauges } = useReadContract({
    address: GAUGE_VOTER,
    abi: gaugeVoterAbi,
    functionName: "getAllGauges",
  });

  const handleVote = (gaugeAddress: `0x${string}`, weight: bigint) => {
    writeContract({
      address: GAUGE_VOTER,
      abi: gaugeVoterAbi,
      functionName: "vote",
      args: [tokenId, [{ gauge: gaugeAddress, weight }]],
    });
  };

  const handleReset = () => {
    writeContract({
      address: GAUGE_VOTER,
      abi: gaugeVoterAbi,
      functionName: "reset",
      args: [tokenId],
    });
  };

  return (
    <div>
      <h3>Available Gauges</h3>
      {gauges?.map((gauge) => (
        <button key={gauge} onClick={() => handleVote(gauge, 10000n)}>
          Vote 100% for {gauge}
        </button>
      ))}
      <button onClick={handleReset}>Reset Votes</button>
    </div>
  );
}
```

## What's Next

- [Claim Voting Incentives](kat-claim-rewards.md) — Collect rewards earned from
  your gauge votes
- [Unstake and Exit](kat-unstake-and-exit.md) — Learn how to exit your vKAT
  position
