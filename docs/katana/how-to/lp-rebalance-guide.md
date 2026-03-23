# Rebalance Your Staked LP Position

This tutorial walks you through the full rebalance flow for a staked SushiSwap V3
LP position on Katana: unstake, remove liquidity, mint a new position at the
correct price range, and restake.

## Goal

By the end of this tutorial, you'll be able to:

- Unstake your LP position from the SushiStaker contract
- Remove liquidity from your SushiSwap V3 position
- Mint a new position centered around the current price
- Restake the new position to continue earning KAT emissions

## Prerequisites

- A staked SushiSwap V3 LP position on Katana
- Tokens to provide as new liquidity (you'll recover these when removing liquidity)
- Basic understanding of concentrated liquidity and tick ranges
- Familiarity with [viem](https://viem.sh/) or [wagmi](https://wagmi.sh/)

If you're new to LP staking, read [Stake Your LP](../stake-lp.md) first.

## Why Rebalance?

SushiSwap V3 uses concentrated liquidity, your position only earns fees and
KAT emissions when the current price is within your tick range. If the price
moves outside your range, your position becomes inactive. Rebalancing means
closing your old position and opening a new one centered on the current price.

## Contract Addresses

```typescript
// SushiStaker (proxy)
const SUSHI_STAKER = "0xbe12e1b5C4859a3d141412748279B67458F729E9";

// SushiSwap V3 NonfungiblePositionManager
const POSITION_MANAGER = "0x2659C6085D26144117D904C46B48B6d180393d27";

// SushiSwap V3 Factory
const SUSHI_FACTORY = "0x203e8740894c8955cB8950759876d7E7E45E04c1";
```

## Set Up viem Clients

```typescript
import {
  createPublicClient,
  createWalletClient,
  http,
  defineChain,
  maxUint128,
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

??? note "SushiStaker ABI (click to expand)"

    ```typescript
    const sushiStakerAbi = [
      {
        name: "unstake",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [],
      },
      {
        name: "stake",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [],
      },
      {
        name: "isStaked",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [{ type: "bool" }],
      },
      {
        name: "getPositionInfo",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [
          { name: "token0", type: "address" },
          { name: "token1", type: "address" },
          { name: "fee", type: "uint24" },
          { name: "tickLower", type: "int24" },
          { name: "tickUpper", type: "int24" },
          { name: "liquidity", type: "uint128" },
        ],
      },
    ] as const;
    ```

??? note "NonfungiblePositionManager ABI (click to expand)"

    ```typescript
    const positionManagerAbi = [
      {
        name: "positions",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "tokenId", type: "uint256" }],
        outputs: [
          { name: "nonce", type: "uint96" },
          { name: "operator", type: "address" },
          { name: "token0", type: "address" },
          { name: "token1", type: "address" },
          { name: "fee", type: "uint24" },
          { name: "tickLower", type: "int24" },
          { name: "tickUpper", type: "int24" },
          { name: "liquidity", type: "uint128" },
          { name: "feeGrowthInside0LastX128", type: "uint256" },
          { name: "feeGrowthInside1LastX128", type: "uint256" },
          { name: "tokensOwed0", type: "uint128" },
          { name: "tokensOwed1", type: "uint128" },
        ],
      },
      {
        name: "decreaseLiquidity",
        type: "function",
        stateMutability: "nonpayable",
        inputs: [
          {
            name: "params",
            type: "tuple",
            components: [
              { name: "tokenId", type: "uint256" },
              { name: "liquidity", type: "uint128" },
              { name: "amount0Min", type: "uint256" },
              { name: "amount1Min", type: "uint256" },
              { name: "deadline", type: "uint256" },
            ],
          },
        ],
        outputs: [
          { name: "amount0", type: "uint256" },
          { name: "amount1", type: "uint256" },
        ],
      },
      {
        name: "collect",
        type: "function",
        stateMutability: "payable",
        inputs: [
          {
            name: "params",
            type: "tuple",
            components: [
              { name: "tokenId", type: "uint256" },
              { name: "recipient", type: "address" },
              { name: "amount0Max", type: "uint128" },
              { name: "amount1Max", type: "uint128" },
            ],
          },
        ],
        outputs: [
          { name: "amount0", type: "uint256" },
          { name: "amount1", type: "uint256" },
        ],
      },
      {
        name: "mint",
        type: "function",
        stateMutability: "payable",
        inputs: [
          {
            name: "params",
            type: "tuple",
            components: [
              { name: "token0", type: "address" },
              { name: "token1", type: "address" },
              { name: "fee", type: "uint24" },
              { name: "tickLower", type: "int24" },
              { name: "tickUpper", type: "int24" },
              { name: "amount0Desired", type: "uint256" },
              { name: "amount1Desired", type: "uint256" },
              { name: "amount0Min", type: "uint256" },
              { name: "amount1Min", type: "uint256" },
              { name: "recipient", type: "address" },
              { name: "deadline", type: "uint256" },
            ],
          },
        ],
        outputs: [
          { name: "tokenId", type: "uint256" },
          { name: "liquidity", type: "uint128" },
          { name: "amount0", type: "uint256" },
          { name: "amount1", type: "uint256" },
        ],
      },
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

??? note "SushiSwap V3 Pool ABI (click to expand)"

    ```typescript
    const poolAbi = [
      {
        name: "slot0",
        type: "function",
        stateMutability: "view",
        inputs: [],
        outputs: [
          { name: "sqrtPriceX96", type: "uint160" },
          { name: "tick", type: "int24" },
          { name: "observationIndex", type: "uint16" },
          { name: "observationCardinality", type: "uint16" },
          { name: "observationCardinalityNext", type: "uint16" },
          { name: "feeProtocol", type: "uint8" },
          { name: "unlocked", type: "bool" },
        ],
      },
    ] as const;
    ```

??? note "SushiSwap V3 Factory ABI (click to expand)"

    ```typescript
    const factoryAbi = [
      {
        name: "getPool",
        type: "function",
        stateMutability: "view",
        inputs: [
          { name: "tokenA", type: "address" },
          { name: "tokenB", type: "address" },
          { name: "fee", type: "uint24" },
        ],
        outputs: [{ name: "pool", type: "address" }],
      },
    ] as const;
    ```

??? note "ERC-20 ABI (click to expand)"

    ```typescript
    const erc20Abi = [
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
        name: "balanceOf",
        type: "function",
        stateMutability: "view",
        inputs: [{ name: "account", type: "address" }],
        outputs: [{ type: "uint256" }],
      },
    ] as const;
    ```

## The Rebalance Flow

The full rebalance is four transactions:

1. **Unstake** — Retrieve your NFT from the SushiStaker contract
2. **Remove liquidity** — Withdraw tokens from your old position
3. **Mint new position** — Create a new LP position at the current price range
4. **Restake** — Stake the new NFT to resume earning KAT emissions

!!! warning "Downtime between unstake and restake"
    Your position does **not** earn KAT emissions while unstaked. Complete the
    full rebalance in one session to minimize missed rewards.

## Step 1: Read Your Current Position

Before unstaking, check your position details to understand your current range:

```typescript
const TOKEN_ID = 12345n; // Replace with your NFT token ID

// Check that the position is staked
const staked = await publicClient.readContract({
  address: SUSHI_STAKER,
  abi: sushiStakerAbi,
  functionName: "isStaked",
  args: [TOKEN_ID],
});

console.log(`Position staked: ${staked}`);

// Read position details
const [token0, token1, fee, tickLower, tickUpper, liquidity] =
  await publicClient.readContract({
    address: SUSHI_STAKER,
    abi: sushiStakerAbi,
    functionName: "getPositionInfo",
    args: [TOKEN_ID],
  });

console.log(`Pool: ${token0}/${token1} (fee: ${fee})`);
console.log(`Tick range: ${tickLower} to ${tickUpper}`);
console.log(`Liquidity: ${liquidity}`);
```

## Step 2: Get the Current Pool Price

Read the current tick from the pool to determine where to center your new range:

```typescript
// Look up the pool address
const poolAddress = await publicClient.readContract({
  address: SUSHI_FACTORY,
  abi: factoryAbi,
  functionName: "getPool",
  args: [token0, token1, fee],
});

// Read current tick from pool
const [sqrtPriceX96, currentTick] = await publicClient.readContract({
  address: poolAddress,
  abi: poolAbi,
  functionName: "slot0",
});

console.log(`Current tick: ${currentTick}`);
console.log(`Your range: ${tickLower} to ${tickUpper}`);

if (currentTick < tickLower || currentTick >= tickUpper) {
  console.log("Price is OUT of your range — rebalance recommended");
} else {
  console.log("Price is IN your range — rebalance is optional");
}
```

## Step 3: Unstake Your Position

Call `unstake()` on the SushiStaker contract to retrieve your NFT:

```typescript
const unstakeHash = await walletClient.writeContract({
  address: SUSHI_STAKER,
  abi: sushiStakerAbi,
  functionName: "unstake",
  args: [TOKEN_ID],
});

await publicClient.waitForTransactionReceipt({ hash: unstakeHash });
console.log("Position unstaked — NFT returned to your wallet");
```

!!! note "Fees on unstake"
    When you unstake, any trading fees accumulated during the staking period are
    collected and sent to the protocol fee collector — not to your wallet. This is
    by design and separate from the KAT emissions you earn.

## Step 4: Remove Liquidity

Now that you own the NFT again, remove all liquidity from the position. This is
two calls: `decreaseLiquidity` to convert liquidity back to tokens, then
`collect` to withdraw those tokens to your wallet.

```typescript
// Remove all liquidity
const deadline = BigInt(Math.floor(Date.now() / 1000) + 600); // 10 minutes

// Calculate expected token amounts from the current position using sqrtPriceX96.
//
// token0 and token1 are ordered by address on-chain — for example, the USDC/WETH
// pool has token0 = USDC and token1 = WETH. The math below works regardless of
// which token is token0 or token1.
//
// sqrtPriceX96 encodes the pool's current price as sqrt(price) * 2^96.
// We use it alongside the position's tick boundaries to estimate how much
// of each token the position holds.
const sqrtPrice = Number(sqrtPriceX96) / 2 ** 96;
const sqrtLower = Math.sqrt(1.0001 ** Number(tickLower));
const sqrtUpper = Math.sqrt(1.0001 ** Number(tickUpper));
const liq = Number(liquidity);

let expectedAmount0: bigint;
let expectedAmount1: bigint;

if (Number(currentTick) < Number(tickLower)) {
  // Price below range — position is entirely token0
  expectedAmount0 = BigInt(Math.floor(liq * (1 / sqrtLower - 1 / sqrtUpper)));
  expectedAmount1 = 0n;
} else if (Number(currentTick) >= Number(tickUpper)) {
  // Price above range — position is entirely token1
  expectedAmount0 = 0n;
  expectedAmount1 = BigInt(Math.floor(liq * (sqrtUpper - sqrtLower)));
} else {
  // Price in range — position holds a mix of both tokens
  expectedAmount0 = BigInt(Math.floor(liq * (1 / sqrtPrice - 1 / sqrtUpper)));
  expectedAmount1 = BigInt(Math.floor(liq * (sqrtPrice - sqrtLower)));
}

// Apply 0.5% slippage tolerance — reject the transaction if you'd receive
// less than 99.5% of the expected amount. This protects against sandwich
// attacks where a bot manipulates the price right before your transaction.
const SLIPPAGE_BPS = 50n; // 0.5% expressed in basis points (50 / 10000)
const amount0Min = expectedAmount0 - (expectedAmount0 * SLIPPAGE_BPS) / 10000n;
const amount1Min = expectedAmount1 - (expectedAmount1 * SLIPPAGE_BPS) / 10000n;

console.log(`Expected token0: ${expectedAmount0}, min: ${amount0Min}`);
console.log(`Expected token1: ${expectedAmount1}, min: ${amount1Min}`);

const decreaseHash = await walletClient.writeContract({
  address: POSITION_MANAGER,
  abi: positionManagerAbi,
  functionName: "decreaseLiquidity",
  args: [
    {
      tokenId: TOKEN_ID,
      liquidity: liquidity, // Full amount from Step 1
      amount0Min,
      amount1Min,
      deadline,
    },
  ],
});

await publicClient.waitForTransactionReceipt({ hash: decreaseHash });
console.log("Liquidity removed");

// Collect the tokens
const collectHash = await walletClient.writeContract({
  address: POSITION_MANAGER,
  abi: positionManagerAbi,
  functionName: "collect",
  args: [
    {
      tokenId: TOKEN_ID,
      recipient: account.address,
      amount0Max: maxUint128,
      amount1Max: maxUint128,
    },
  ],
});

const collectReceipt = await publicClient.waitForTransactionReceipt({
  hash: collectHash,
});
console.log("Tokens collected to your wallet");
```

!!! warning "Slippage protection"
    The example above uses a **0.5% slippage tolerance**. This means the
    transaction will revert if the amounts you receive are more than 0.5% below
    the expected values — protecting you from sandwich attacks. Adjust
    `SLIPPAGE_BPS` based on market conditions: tighter (e.g., `25n` for 0.25%)
    in calm markets, wider (e.g., `100n` for 1%) during high volatility.

## Step 5: Calculate New Tick Range

Center your new position around the current price. This example uses a +/-25%
range — adjust the percentage to your preferred concentration level.

```typescript
// Helper: calculate tick range for a +/- percentage around current tick
// tickSpacing depends on fee tier (fee 100 = spacing 1, 500 = 10, 3000 = 60, 10000 = 200)
function getTickSpacing(feeTier: number): number {
  const spacings: Record<number, number> = { 100: 1, 500: 10, 3000: 60, 10000: 200 };
  return spacings[feeTier] || 60;
}

// Calculate +/-25% range in tick space
// Each tick represents a ~0.01% price change, so 25% ≈ 2500 ticks
const RANGE_TICKS = 2500;
const tickSpacing = getTickSpacing(Number(fee));

// Round ticks to nearest valid tickSpacing
const newTickLower =
  Math.floor((Number(currentTick) - RANGE_TICKS) / tickSpacing) * tickSpacing;
const newTickUpper =
  Math.ceil((Number(currentTick) + RANGE_TICKS) / tickSpacing) * tickSpacing;

console.log(`New tick range: ${newTickLower} to ${newTickUpper}`);
```

!!! note "Adjusting concentration"
    A tighter range (e.g., +/-10%) earns more fees when in range but goes out of
    range more often. A wider range (e.g., +/-50%) is more resilient to price
    movement but earns less per unit of liquidity. Adjust `RANGE_TICKS` to your
    preference.

## Step 6: Approve Tokens and Mint New Position

Approve both tokens for the Position Manager, then mint a new LP position:

```typescript
// Check your token balances
const balance0 = await publicClient.readContract({
  address: token0,
  abi: erc20Abi,
  functionName: "balanceOf",
  args: [account.address],
});

const balance1 = await publicClient.readContract({
  address: token1,
  abi: erc20Abi,
  functionName: "balanceOf",
  args: [account.address],
});

console.log(`Token0 balance: ${balance0}`);
console.log(`Token1 balance: ${balance1}`);

// Approve token0
const approve0Hash = await walletClient.writeContract({
  address: token0,
  abi: erc20Abi,
  functionName: "approve",
  args: [POSITION_MANAGER, balance0],
});
await publicClient.waitForTransactionReceipt({ hash: approve0Hash });

// Approve token1
const approve1Hash = await walletClient.writeContract({
  address: token1,
  abi: erc20Abi,
  functionName: "approve",
  args: [POSITION_MANAGER, balance1],
});
await publicClient.waitForTransactionReceipt({ hash: approve1Hash });

console.log("Both tokens approved");

// Apply 0.5% slippage tolerance to the mint (same approach as Step 4)
const mintSlippageBps = 50n; // 0.5%
const mintAmount0Min = balance0 - (balance0 * mintSlippageBps) / 10000n;
const mintAmount1Min = balance1 - (balance1 * mintSlippageBps) / 10000n;

// Mint new position
const mintHash = await walletClient.writeContract({
  address: POSITION_MANAGER,
  abi: positionManagerAbi,
  functionName: "mint",
  args: [
    {
      token0,
      token1,
      fee,
      tickLower: newTickLower,
      tickUpper: newTickUpper,
      amount0Desired: balance0,
      amount1Desired: balance1,
      amount0Min: mintAmount0Min,
      amount1Min: mintAmount1Min,
      recipient: account.address,
      deadline: BigInt(Math.floor(Date.now() / 1000) + 600),
    },
  ],
});

const mintReceipt = await publicClient.waitForTransactionReceipt({
  hash: mintHash,
});
console.log("New position minted!", mintReceipt);
```

To get the new token ID from the mint transaction, parse the `Transfer` event
from the receipt:

```typescript
import { parseEventLogs } from "viem";

const transferAbi = [
  {
    type: "event",
    name: "Transfer",
    inputs: [
      { name: "from", type: "address", indexed: true },
      { name: "to", type: "address", indexed: true },
      { name: "tokenId", type: "uint256", indexed: true },
    ],
  },
] as const;

const logs = parseEventLogs({
  abi: transferAbi,
  logs: mintReceipt.logs,
});

const newTokenId = logs[0].args.tokenId;
console.log(`New NFT token ID: ${newTokenId}`);
```

## Step 7: Restake the New Position

Approve the SushiStaker contract and stake your new NFT:

```typescript
// Approve SushiStaker to transfer your NFT
const approveNftHash = await walletClient.writeContract({
  address: POSITION_MANAGER,
  abi: positionManagerAbi,
  functionName: "approve",
  args: [SUSHI_STAKER, newTokenId],
});
await publicClient.waitForTransactionReceipt({ hash: approveNftHash });

// Stake the new position
const stakeHash = await walletClient.writeContract({
  address: SUSHI_STAKER,
  abi: sushiStakerAbi,
  functionName: "stake",
  args: [newTokenId],
});

await publicClient.waitForTransactionReceipt({ hash: stakeHash });
console.log(`Position ${newTokenId} staked — earning KAT emissions again!`);
```

## Step 8: Verify

Confirm your new position is staked and active:

```typescript
const isNowStaked = await publicClient.readContract({
  address: SUSHI_STAKER,
  abi: sushiStakerAbi,
  functionName: "isStaked",
  args: [newTokenId],
});

const [, , , , newLower, newUpper, newLiq] = await publicClient.readContract({
  address: SUSHI_STAKER,
  abi: sushiStakerAbi,
  functionName: "getPositionInfo",
  args: [newTokenId],
});

console.log(`Staked: ${isNowStaked}`);
console.log(`New range: ${newLower} to ${newUpper}`);
console.log(`Liquidity: ${newLiq}`);
console.log("Rebalance complete!");
```

## Using wagmi (React)

If you're building a React frontend, here's the pattern using wagmi hooks. This
example covers the full rebalance flow with a step-by-step UI:

```typescript
import { useWriteContract, useWaitForTransactionReceipt, useReadContract, useAccount } from "wagmi";
import { maxUint128 } from "viem";

const SUSHI_STAKER = "0xbe12e1b5C4859a3d141412748279B67458F729E9";
const POSITION_MANAGER = "0x2659C6085D26144117D904C46B48B6d180393d27";

function RebalanceLP({ tokenId }: { tokenId: bigint }) {
  const { address } = useAccount();
  const { writeContract, data: hash, isPending } = useWriteContract();
  const { isLoading, isSuccess } = useWaitForTransactionReceipt({ hash });

  // Read current position
  const { data: positionInfo } = useReadContract({
    address: SUSHI_STAKER,
    abi: sushiStakerAbi,
    functionName: "getPositionInfo",
    args: [tokenId],
  });

  // Step 1: Unstake
  const handleUnstake = () => {
    writeContract({
      address: SUSHI_STAKER,
      abi: sushiStakerAbi,
      functionName: "unstake",
      args: [tokenId],
    });
  };

  // Step 2: Remove liquidity
  const handleRemoveLiquidity = (
    expectedAmount0: bigint,
    expectedAmount1: bigint
  ) => {
    if (!positionInfo) return;
    const [, , , , , liquidity] = positionInfo;

    // 0.5% slippage tolerance
    const slippageBps = 50n;
    const amount0Min = expectedAmount0 - (expectedAmount0 * slippageBps) / 10000n;
    const amount1Min = expectedAmount1 - (expectedAmount1 * slippageBps) / 10000n;

    writeContract({
      address: POSITION_MANAGER,
      abi: positionManagerAbi,
      functionName: "decreaseLiquidity",
      args: [
        {
          tokenId,
          liquidity,
          amount0Min,
          amount1Min,
          deadline: BigInt(Math.floor(Date.now() / 1000) + 600),
        },
      ],
    });
  };

  // Step 3: Collect tokens
  const handleCollect = () => {
    if (!address) return;
    writeContract({
      address: POSITION_MANAGER,
      abi: positionManagerAbi,
      functionName: "collect",
      args: [
        {
          tokenId,
          recipient: address,
          amount0Max: maxUint128,
          amount1Max: maxUint128,
        },
      ],
    });
  };

  // Step 4: Mint new position (after calculating new ticks)
  const handleMint = (
    token0: `0x${string}`,
    token1: `0x${string}`,
    fee: number,
    newTickLower: number,
    newTickUpper: number,
    amount0: bigint,
    amount1: bigint
  ) => {
    if (!address) return;

    // 0.5% slippage tolerance
    const slippageBps = 50n;
    const amount0Min = amount0 - (amount0 * slippageBps) / 10000n;
    const amount1Min = amount1 - (amount1 * slippageBps) / 10000n;

    writeContract({
      address: POSITION_MANAGER,
      abi: positionManagerAbi,
      functionName: "mint",
      args: [
        {
          token0,
          token1,
          fee,
          tickLower: newTickLower,
          tickUpper: newTickUpper,
          amount0Desired: amount0,
          amount1Desired: amount1,
          amount0Min,
          amount1Min,
          recipient: address,
          deadline: BigInt(Math.floor(Date.now() / 1000) + 600),
        },
      ],
    });
  };

  // Step 5: Stake new position
  const handleStake = (newTokenId: bigint) => {
    writeContract({
      address: SUSHI_STAKER,
      abi: sushiStakerAbi,
      functionName: "stake",
      args: [newTokenId],
    });
  };

  // Each handler above is called after the previous transaction confirms.
  // Wire these to your UI step-by-step — for example:
  return (
    <div>
      <button onClick={handleUnstake} disabled={isPending}>
        {isPending ? "Unstaking..." : "1. Unstake"}
      </button>
      {/* 2. Call handleRemoveLiquidity(expectedAmount0, expectedAmount1) */}
      {/* 3. Call handleCollect() */}
      {/* 4. Call handleMint(token0, token1, fee, tickLower, tickUpper, amount0, amount1) */}
      {/* 5. Call handleStake(newTokenId) */}
    </div>
  );
}
```

## What's Next

- [Stake Your LP](../stake-lp.md) — Learn how to stake for the first time
- [Vote on Gauges](kat-vote-on-gauges.md) — Direct KAT emissions to your pool
- [KAT Staking](../katana-staking.md) — Understand the full KAT staking and emissions system
