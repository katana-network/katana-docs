# Bridge USDC to Katana with LayerZero

All bridged funds on Katana go through the Vault Bridge, which deposits assets into ERC-4626 vaults on Ethereum and bridges the resulting vault shares to Katana via LayerZero OFT. This means users earn yield on their bridged assets automatically.

`Ethereum → Katana` <br>
![ETH Bridge](../../img/bridges/ethtokatanabridge.png)

Coming from Mainnet Ethereum, your token (USDC in this guide) is deposited into the Vault Bridge through the OVault Composer. The Composer deposits the token into the vault, receives shares, and bridges those shares to Katana. On arrival, the user receives vbUSDC tokens on Katana.

`L2 → Ethereum → Katana` <br>
![ETH Bridge](../../img/bridges/l2toethtokatana.png)

Coming from an alternative L2, the asset must bridge back to Ethereum Mainnet first before depositing to the Vault Bridge and bridging shares to Katana.

## Overview

The deposit and bridge flow requires **two transactions** on Ethereum:

1. **Approve** — Allow the OVault Composer to spend your USDC
2. **Deposit & Bridge** — The Composer deposits USDC into the vault and bridges the minted shares to Katana via LayerZero

```
User (USDC on Ethereum)
  │
  ├─ TX 1: Approve USDC → OVaultComposer
  │
  └─ TX 2: depositAndSend()
       ├─ Composer takes USDC from user
       ├─ Deposits USDC into ERC-4626 Vault
       ├─ Vault mints shares to Composer
       └─ Composer bridges shares to Katana via LayerZero OFT
             │
             └─ User receives vbUSDC on Katana (~3-7 min)
```

A full working example can be found in the [lz-ops-demo repository](https://github.com/katana-network/lz-ops-demo).

## Prerequisites

### Key Contracts

**Ethereum Mainnet**

| Contract          | Address                                      | Explorer                                                                            |
| ----------------- | -------------------------------------------- | ----------------------------------------------------------------------------------- |
| USDC              | `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48` | [etherscan](https://etherscan.io/address/0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48) |
| OVault Composer   | `0x8A35897fda9E024d2aC20a937193e099679eC477` | [etherscan](https://etherscan.io/address/0x8A35897fda9E024d2aC20a937193e099679eC477) |
| USDC Vault Bridge | `0x53E82ABbb12638F09d9e624578ccB666217a765e` | [etherscan](https://etherscan.io/address/0x53E82ABbb12638F09d9e624578ccB666217a765e) |
| Share OFT Adapter | `0xb5bADA33542a05395d504a25885e02503A957Bb3` | [etherscan](https://etherscan.io/address/0xb5bADA33542a05395d504a25885e02503A957Bb3) |

| Config            | Value     |
| ----------------- | --------- |
| Chain              | Ethereum Mainnet |
| LayerZero EID     | `30101`   |

**Katana**

| Contract          | Address                                      | Explorer                                                                              |
| ----------------- | -------------------------------------------- | ------------------------------------------------------------------------------------- |
| Share OFT         | `0x807275727Dd3E640c5F2b5DE7d1eC72B4Dd293C0` | [katanascan](https://katanascan.com/address/0x807275727dd3e640c5f2b5de7d1ec72b4dd293c0) |

| Config            | Value     |
| ----------------- | --------- |
| Chain              | Katana   |
| LayerZero EID     | `30375`   |

Other supported Vault Bridge assets:

<!--
| Asset | Vault Bridge Address                           | Explorer                                                                            |
| ----- | ---------------------------------------------- | ----------------------------------------------------------------------------------- |
| USDT  | `0x6d4f9f9f8f0155509ecd6Ac6c544fF27999845CC`  | [etherscan](https://etherscan.io/address/0x6d4f9f9f8f0155509ecd6Ac6c544fF27999845CC) |
| WBTC  | `0x2C24B57e2CCd1f273045Af6A5f632504C432374F`  | [etherscan](https://etherscan.io/address/0x2C24B57e2CCd1f273045Af6A5f632504C432374F) |
| WETH  | `0x2DC70fb75b88d2eB4715bc06E1595E6D97c34DFF`  | [etherscan](https://etherscan.io/address/0x2DC70fb75b88d2eB4715bc06E1595E6D97c34DFF) |
-->

All L2 Stargate pool contracts can be found in the [Stargate docs](https://docs.stargate.finance/resources/contracts/mainnet-contracts).

### Environment Setup

Set your private key in a `.env` file:

```bash
PRIVATE_KEY=your_private_key_here
```

### Dependencies

Install the required packages:

```bash
npm install ethers @layerzerolabs/lz-v2-utilities dotenv
```

## Step 1: Configure the Script

Start by importing your dependencies and setting up the configuration. This example bridges USDC, but you can swap in any supported Vault Bridge asset by changing the contract addresses.

```typescript
import { ethers } from 'ethers'
import { parseUnits } from 'ethers/lib/utils'
import { Options, addressToBytes32 } from '@layerzerolabs/lz-v2-utilities'

const CONFIG = {
    privateKey: process.env.PRIVATE_KEY!,

    transaction: {
        amount: '10.0',           // Amount of USDC to deposit
        recipientAddress: '0x...', // Your address on Katana that will receive vbUSDC
        slippageBps: 50,           // 0.5% slippage tolerance
    },

    ethereum: {
        eid: 30101,
        rpcUrl: 'https://ethereum-rpc.publicnode.com',
    },

    katana: {
        eid: 30375,
    },

    contracts: {
        asset: '0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48',    // USDC on Ethereum
        vault: '0x53E82ABbb12638F09d9e624578ccB666217a765e',    // USDC Vault Bridge
        composer: '0x8A35897fda9E024d2aC20a937193e099679eC477',  // OVault Composer
        shareOFT: '0xb5bADA33542a05395d504a25885e02503A957Bb3', // Share OFT Adapter
    },
}
```

## Step 2: Set Up the Provider and Wallet

Connect to Ethereum and create a signer from your private key:

```typescript
const ethereumProvider = new ethers.providers.JsonRpcProvider(CONFIG.ethereum.rpcUrl)
const wallet = new ethers.Wallet(CONFIG.privateKey, ethereumProvider)

console.log(`Wallet Address: ${wallet.address}`)
```

## Step 3: Check Your USDC Balance

Before depositing, verify that your wallet has enough USDC:

```typescript
const erc20Abi = [
    'function decimals() view returns (uint8)',
    'function balanceOf(address) view returns (uint256)',
    'function allowance(address,address) view returns (uint256)',
    'function approve(address,uint256) returns (bool)',
]

const assetToken = new ethers.Contract(CONFIG.contracts.asset, erc20Abi, wallet)
const assetDecimals = await assetToken.decimals()
const amount = parseUnits(CONFIG.transaction.amount, assetDecimals)

const balance = await assetToken.balanceOf(wallet.address)
console.log(`USDC balance: ${ethers.utils.formatUnits(balance, assetDecimals)}`)

if (balance.lt(amount)) {
    throw new Error(
        `Insufficient USDC. Need ${CONFIG.transaction.amount}, have ${ethers.utils.formatUnits(balance, assetDecimals)}`
    )
}
```

## Step 4: Preview the Vault Deposit

Query the vault to see how many shares you'll receive for your USDC deposit. This uses the ERC-4626 `previewDeposit` function:

```typescript
const vaultAbi = [
    'function decimals() view returns (uint8)',
    'function previewDeposit(uint256) view returns (uint256)',
]

const vault = new ethers.Contract(CONFIG.contracts.vault, vaultAbi, ethereumProvider)
const vaultDecimals = await vault.decimals()

const expectedShares = await vault.previewDeposit(amount)
const minShares = expectedShares.mul(10000 - CONFIG.transaction.slippageBps).div(10000)

console.log(`USDC to deposit: ${ethers.utils.formatUnits(amount, assetDecimals)}`)
console.log(`Expected vault shares: ${ethers.utils.formatUnits(expectedShares, vaultDecimals)}`)
console.log(`Min shares (0.5% slippage): ${ethers.utils.formatUnits(minShares, vaultDecimals)}`)
```

The `minShares` value protects you from slippage — the transaction will revert if you receive fewer shares than this minimum.

## Step 5: Quote the LayerZero Bridge Fee

LayerZero charges a fee in ETH to relay your message from Ethereum to Katana. Use `quoteSend` on the Share OFT contract to get this fee:

```typescript
const sendParam = {
    dstEid: CONFIG.katana.eid,
    to: addressToBytes32(CONFIG.transaction.recipientAddress),
    amountLD: expectedShares,
    minAmountLD: minShares,
    extraOptions: Options.newOptions().addExecutorLzReceiveOption(100000, 0).toHex(),
    composeMsg: '0x',
    oftCmd: '0x',
}

const oftAbi = [
    'function quoteSend((uint32,bytes32,uint256,uint256,bytes,bytes,bytes),bool) view returns ((uint256,uint256))',
]

const shareOFT = new ethers.Contract(CONFIG.contracts.shareOFT, oftAbi, ethereumProvider)

const quote = await shareOFT.quoteSend(
    [
        sendParam.dstEid,
        sendParam.to,
        sendParam.amountLD,
        sendParam.minAmountLD,
        sendParam.extraOptions,
        sendParam.composeMsg,
        sendParam.oftCmd,
    ],
    false // don't pay in LZ token
)

const bridgeFee = quote[0]
console.log(`LayerZero bridge fee: ${ethers.utils.formatEther(bridgeFee)} ETH`)
```

Key parameters explained:

- **`dstEid`** — The LayerZero endpoint ID for Katana (`30375`)
- **`to`** — The recipient address on Katana, encoded as bytes32
- **`amountLD`** — The amount of shares to bridge
- **`minAmountLD`** — Minimum shares to receive (slippage protection)
- **`extraOptions`** — Gas limit for the receive transaction on Katana (`100000` is sufficient)

## Step 6: Approve USDC to the Composer

The OVault Composer needs permission to transfer your USDC. Check the current allowance first to avoid unnecessary approvals:

```typescript
const allowance = await assetToken.allowance(wallet.address, CONFIG.contracts.composer)
console.log(`Current allowance: ${ethers.utils.formatUnits(allowance, assetDecimals)}`)

if (allowance.lt(amount)) {
    console.log(`Approving ${CONFIG.transaction.amount} USDC...`)
    const approveTx = await assetToken.approve(CONFIG.contracts.composer, amount)
    console.log(`Approval tx: ${approveTx.hash}`)
    await approveTx.wait()
    console.log(`Approval confirmed`)
}
```

## Step 7: Execute the Deposit and Bridge

Call `depositAndSend` on the OVault Composer. This single transaction deposits your USDC into the vault and bridges the minted shares to Katana:

```typescript
const composerAbi = [
    'function depositAndSend(uint256,(uint32,bytes32,uint256,uint256,bytes,bytes,bytes),address) payable',
]

const composer = new ethers.Contract(CONFIG.contracts.composer, composerAbi, wallet)

const tx = await composer.depositAndSend(
    amount,
    [
        sendParam.dstEid,
        sendParam.to,
        sendParam.amountLD,
        sendParam.minAmountLD,
        sendParam.extraOptions,
        sendParam.composeMsg,
        sendParam.oftCmd,
    ],
    wallet.address, // refund address for excess ETH
    { value: bridgeFee }
)

console.log(`Transaction hash: ${tx.hash}`)
console.log(`Waiting for confirmation...`)

const receipt = await tx.wait()
console.log(`Confirmed in block ${receipt.blockNumber}`)
```

The `depositAndSend` function does the following in a single call:

1. Transfers USDC from your wallet to the Composer
2. Deposits USDC into the ERC-4626 vault on Ethereum
3. Bridges the minted vault shares to your recipient address on Katana via LayerZero

## What Happens Next

After your Ethereum transaction confirms, the bridging process is handled by LayerZero:

1. LayerZero validators confirm the cross-chain message (~1-2 minutes)
2. The LayerZero executor delivers the shares to Katana (~2-5 minutes)
3. Your recipient address receives vbUSDC on Katana

You can track the bridge status at:

- **LayerZero Scan**: `https://layerzeroscan.com/tx/<your_tx_hash>`
- **Etherscan**: `https://etherscan.io/tx/<your_tx_hash>`


## Full Example

A complete runnable script with all of the steps above is available in the [lz-ops-demo repository](https://github.com/katana-network/lz-ops-demo) as well as a layer2 chain example. Clone the repo and run:

```bash
git clone https://github.com/katana-network/lz-ops-demo.git
cd lz-ops-demo
npm install
npx ts-node scripts/1-ethereum-to-katana-deposit.ts
```
