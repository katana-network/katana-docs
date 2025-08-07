# Read Balances & Wrap ETH into bvbEth

This tutorial will guide you through the process of interacting with Katana's
core tokens, specifically how to read token balances and wrap ETH into the yield
bearing bvbEth (Bridged Vault Bridge ETH) using a local Katana fork.

For all intents and purposes, WETH and bvbEth can be used interchangeably.

## Goal

By the end of this tutorial, you'll be able to:

- Read ETH and bvbEth balances using viem
- Wrap ETH into bvbEth
- Unwrap bvbEth back to ETH
- Understand the relationship between ETH and bvbEth in the Katana ecosystem

## Prerequisites

- Completed [Tutorial 01: Getting Started](../get-started/set-up-your-environment.md)
- Basic understanding of ERC20 tokens and ETH wrapping
- Local Katana fork running

## ETH and bvbEth

Ethereum's native currency (ETH) doesn't conform to the ERC20 standard. This
presents challenges when applications need consistent token interfaces. Bridged
Vault Bridge ETH (bvbEth) solves this problem by wrapping ETH in an ERC20-compliant token
contract while providing additional yield-bearing capabilities.

In Katana, bvbEth has special properties:

- Maintains the standard WETH9 interface for compatibility
- Acts as a drop-in replacement for WETH across DeFi applications
- Yield from bridged ETH can be distributed to active network participants
- We call it **bvbEth** or "bridged vault bridge Eth"

## Setting Up the Local Environment

Before starting, make sure your local Katana fork is running:

### Terminal 1: Start Anvil Fork

```sh
bun run start:anvil katana
```

### Terminal 2: [Optional] Verify the Fork

```sh
bun run verify:anvil
```

### Configure Your Wallet

Connect your wallet to the local fork with these settings:

- **Network Name**: Katana Local Fork
- **RPC URL**: `http://localhost:8545`
- **Chain ID**: 747474
- **Currency Symbol**: ETH

Note that the local fork of the chain is initialized with some [unlocked and
Eth-rich accounts](https://getfoundry.sh/anvil/overview). You should either
import one of those into your wallet and use its balance, or send one such
account's balance to your main testing wallet.

> A note on Metamask shenanigans: when using Metamask to issue transactions on a
> custom chain, if you reset the chain, the nonce in the wallet will stay the
> same. Because the nonce in the chain for this account will have reset to 0,
> and the nonce in the wallet will have stayed at what it was during the last
> test, Metamask will no longer work (it is not known why Metamask doesn't
> simply read the nonce from the chain). To fix this, you need to "Clear
> Activity Data" for an account in Metamask's advanced settings. More info
> [here](https://support.metamask.io/configure/accounts/how-to-clear-your-account-activity-reset-account/).

## Understanding the Example App Structure

The starter kit provides a foundation we'll build upon. The main files are:

- `src/index.html`: The UI entry point
- `src/main.ts`: Core application logic with viem setup
- `src/style.css`: Styling for the application

The application is unopinionated on purpose, and as close to vanilla as possible
to reduce the chances of a supply chain attack. Only a simple esbuild script is
responsible for compiling the very rudimentary `main.ts` file into JavaScript.

If you find a need for frameworks, alternative build systems, or any other
dependencies, please feel free to pull those in as desired. For the purposes of
this tutorial, modifying this simple app skeleton will be just fine.

The outcome of this tutorial is a modified sample app which can be found in the
`examples/wrapped` folder.

## Building the App: Reading Balances and Wrapping ETH

Let's create a new component that reads ETH and WETH balances and allows users
to wrap/unwrap ETH.

### Step 1: Update the HTML

First, we'll add a new section to `index.html` for our functionality:

```html
<!-- ETH/bvbEth Operations Section -->
<section id="eth-weth-section">
  <h2>ETH/bvbEth Wrapping Operations</h2>
  <div class="card">
    <h3>Token Balances</h3>
    <div class="balances-container">
      <div class="balance-item">
        <div class="label">ETH Balance</div>
        <div id="eth-balance" class="value">-</div>
      </div>
      <div class="balance-item">
        <div class="label">bvbEth Balance</div>
        <div id="weth-balance" class="value">-</div>
      </div>
    </div>
    
    <div class="operations-container">
      <div class="operation-card">
        <h4>Wrap ETH to bvbEth</h4>
        <p class="operation-description">Convert your ETH into yield-bearing bvbEth tokens</p>
        <div class="form-group">
          <label for="wrap-amount">Amount (ETH)</label>
          <input type="number" id="wrap-amount" min="0" step="0.01" placeholder="0.0">
        </div>
        <button id="wrap-button" class="primary-button" disabled>Wrap ETH</button>
      </div>
      
      <div class="operation-card">
        <h4>Unwrap bvbEth to ETH</h4>
        <p class="operation-description">Convert your bvbEth tokens back to ETH</p>
        <div class="form-group">
          <label for="unwrap-amount">Amount (bvbEth)</label>
          <input type="number" id="unwrap-amount" min="0" step="0.01" placeholder="0.0">
        </div>
        <button id="unwrap-button" class="primary-button" disabled>Unwrap bvbEth</button>
      </div>
    </div>
  </div>
</section>
```

This section provides a clean interface for wrapping ETH into bvbEth and
unwrapping bvbEth back to ETH.

Add this section just before the closing `</main>` tag in the `index.html` file.

### Step 2: Update the TypeScript Code

Now we'll add the necessary code to `main.ts` to handle:

1. Reading ETH and bvbEth balances
2. Wrapping ETH to bvbEth
3. Unwrapping bvbEth back to ETH

First, we need to add `parseUnits` to our imports for handling number conversions:

```ts
import { createPublicClient, createWalletClient, http, custom, formatEther, formatUnits, parseUnits, PublicClient, WalletClient } from 'viem';
import { addresses, CHAIN_IDS } from '../utils/addresses/index.js';
```

The starter kit already includes comprehensive chain detection and address management,
so we can leverage the existing infrastructure.

Next, we'll add references to our new DOM elements:

```ts
// ETH/bvbEth wrapping elements
const ethBalanceElement = document.getElementById('eth-balance') as HTMLElement;
const wethBalanceElement = document.getElementById('weth-balance') as HTMLElement;
const wrapAmountInput = document.getElementById('wrap-amount') as HTMLInputElement;
const unwrapAmountInput = document.getElementById('unwrap-amount') as HTMLInputElement;
const wrapButton = document.getElementById('wrap-button') as HTMLButtonElement;
const unwrapButton = document.getElementById('unwrap-button') as HTMLButtonElement;
```

Before we get into wrapping and unwrapping, let's create the function to read
ETH and bvbEth balances:

```ts
// Load ETH and bvbEth balances
async function loadBalances() {
  if (!walletClient || !publicClient) {
    ethBalanceElement.textContent = 'Wallet not connected';
    wethBalanceElement.textContent = 'Wallet not connected';
    return;
  }
  
  try {
    // Get connected account
    const accounts = await walletClient.getAddresses();
    if (accounts.length === 0) {
      return;
    }
    const account = accounts[0];
    
    // Get bvbEth address for the current chain using the address utils
    const wethAddress = addresses.getAddress('bvbEth');
    if (!wethAddress) {
      ethBalanceElement.textContent = 'bvbEth not available';
      wethBalanceElement.textContent = 'bvbEth not available';
      return;
    }
    
    // Read ETH balance
    const ethBalance = await publicClient.getBalance({ address: account });
    ethBalanceElement.textContent = `${formatEther(ethBalance)} ETH`;
    
    // Read bvbEth balance
    const wethBalance = await publicClient.readContract({
      address: wethAddress,
      abi: WETH_ABI,
      functionName: 'balanceOf',
      args: [account]
    });
    
    wethBalanceElement.textContent = `${formatEther(wethBalance as bigint)} bvbEth`;
    
    // Enable buttons if wallet is connected
    wrapButton.disabled = false;
    unwrapButton.disabled = false;
  } catch (error) {
    console.error('Error loading balances:', error);
    ethBalanceElement.textContent = 'Error loading';
    wethBalanceElement.textContent = 'Error loading';
  }
}
```

We want this to run on wallet connect, so let's modify the existing
connectWallet function to call loadBalances():

```ts
// In the connectWallet function, add this after successful connection:
if (accounts.length > 0) {
  walletStatus.textContent = `Connected: ${shortenAddress(accounts[0])}`;
  connectWalletButton.textContent = 'Connected';
  connectWalletButton.disabled = true;
  
  // Load balances after wallet connection
  loadBalances();
}
```

This should already be runnable now - if you build the examples with
`bun run build:examples` and then host it with something like
`cd dist-examples/wrapping && npx http-server`, you should be able to see the
balances when you connect.

Remember - to top up your balance of "eth", just seed it from one of the
[Anvil-provided unlocked accounts](https://getfoundry.sh/anvil/overview). Import
the private key of one such wallet into a wallet of your choice and send
yourself some magic eth!

The wrapping functions are next. Add these functions to handle wrapping and unwrapping:

```ts
// Wrap ETH to bvbEth
async function wrapEth() {
  if (!walletClient || !publicClient) {
    alert('Please connect your wallet first');
    return;
  }
  
  const wrapAmount = wrapAmountInput.value;
  if (!wrapAmount || parseFloat(wrapAmount) <= 0) {
    alert('Please enter a valid amount');
    return;
  }
  
  try {
    const accounts = await walletClient.getAddresses();
    const account = accounts[0];
    
    // Get bvbEth address for the current chain
    const wethAddress = addresses.getAddress('bvbEth');
    if (!wethAddress) {
      alert('bvbEth not available on this chain');
      return;
    }
    
    // Convert the input value to Wei (bigint)
    const wrapAmountWei = parseUnits(wrapAmount, 18);
    
    // Show loading state
    wrapButton.disabled = true;
    wrapButton.textContent = 'Processing...';
    
    // Call bvbEth deposit function
    const hash = await walletClient.writeContract({
      address: wethAddress,
      abi: WETH_ABI,
      functionName: 'deposit',
      value: wrapAmountWei,
      account,
      chain: createChainConfig()
    });
    
    // Wait for transaction to be mined
    const receipt = await publicClient.waitForTransactionReceipt({ hash });
    
    if (receipt.status === 'success') {
      alert(`Successfully wrapped ${wrapAmount} ETH to bvbEth!`);
      
      // Clear input and update balances
      wrapAmountInput.value = '';
      loadBalances();
    } else {
      alert('Transaction failed. Please try again.');
    }
  } catch (error) {
    console.error('Error wrapping ETH:', error);
    alert('Error wrapping ETH. See console for details.');
  } finally {
    // Reset button state
    wrapButton.disabled = false;
    wrapButton.textContent = 'Wrap ETH';
  }
}

// Unwrap bvbEth to ETH
async function unwrapWeth() {
  if (!walletClient || !publicClient) {
    alert('Please connect your wallet first');
    return;
  }
  
  const unwrapAmount = unwrapAmountInput.value;
  if (!unwrapAmount || parseFloat(unwrapAmount) <= 0) {
    alert('Please enter a valid amount');
    return;
  }
  
  try {
    const accounts = await walletClient.getAddresses();
    const account = accounts[0];
    
    // Get bvbEth address for the current chain
    const wethAddress = addresses.getAddress('bvbEth');
    if (!wethAddress) {
      alert('bvbEth not available on this chain');
      return;
    }
    
    // Convert the input value to Wei (bigint)
    const unwrapAmountWei = parseUnits(unwrapAmount, 18);
    
    // Show loading state
    unwrapButton.disabled = true;
    unwrapButton.textContent = 'Processing...';
    
    // Call bvbEth withdraw function
    const hash = await walletClient.writeContract({
      address: wethAddress,
      abi: WETH_ABI,
      functionName: 'withdraw',
      args: [unwrapAmountWei],
      account,
      chain: createChainConfig()
    });
    
    // Wait for transaction to be mined
    const receipt = await publicClient.waitForTransactionReceipt({ hash });
    
    if (receipt.status === 'success') {
      alert(`Successfully unwrapped ${unwrapAmount} bvbEth to ETH!`);
      
      // Clear input and update balances
      unwrapAmountInput.value = '';
      loadBalances();
    } else {
      alert('Transaction failed. Please try again.');
    }
  } catch (error) {
    console.error('Error unwrapping bvbEth:', error);
    alert('Error unwrapping bvbEth. See console for details.');
  } finally {
    // Reset button state
    unwrapButton.disabled = false;
    unwrapButton.textContent = 'Unwrap bvbEth';
  }
}
```

The starter kit already handles initialization and chain detection, so we just
need to add our new event listeners:

```ts
// Add event listeners for the wrap and unwrap buttons
// Add these to the existing event listeners section:
connectWalletButton.addEventListener('click', connectWallet);
wrapButton.addEventListener('click', wrapEth);
unwrapButton.addEventListener('click', unwrapWeth);
```

The existing initialization function already handles all the chain detection and
setup, so we don't need to modify it further.

We also need to add a helper function to create the chain configuration that
viem requires for contract transactions:

```ts
// Helper function to create chain config for viem
function createChainConfig() {
  return {
    id: currentChainId,
    name: currentChainInfo?.name || 'Unknown',
    nativeCurrency: {
      decimals: 18,
      name: 'Ether',
      symbol: 'ETH',
    },
    rpcUrls: {
      default: {
        http: ['http://localhost:8545'],
      },
    },
  };
}
```

### Step 3: Add CSS Styles

The starter kit already includes comprehensive styling, but we need to add
specific styles for our wrapping interface. Add these styles to your `style.css`
file:

```css
/* ETH/bvbEth Operations */
#eth-weth-section {
  margin-top: 2rem;
}

.balances-container {
  display: flex;
  gap: 1.5rem;
  margin-bottom: 1.5rem;
}

.balance-item {
  flex: 1;
  background-color: #f5f7fa;
  border-radius: 6px;
  padding: 0.8rem 1rem;
}

.balance-item .label {
  font-size: 0.9rem;
  color: #687588;
  margin-bottom: 0.4rem;
}

.balance-item .value {
  font-weight: 600;
  font-size: 1.2rem;
}

.operations-container {
  display: flex;
  gap: 1.5rem;
}

.operation-card {
  flex: 1;
  border: 1px solid #e0e4e9;
  border-radius: 6px;
  padding: 1rem;
}

.operation-card h4 {
  margin-top: 0;
  margin-bottom: 0.5rem;
  font-size: 1rem;
}

.operation-description {
  font-size: 0.85rem;
  color: #687588;
  margin-bottom: 1rem;
  margin-top: 0;
}

.form-group {
  margin-bottom: 1rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-size: 0.9rem;
  font-weight: 500;
}

.form-group input {
  width: 100%;
  padding: 0.6rem;
  border: 1px solid #d0d6df;
  border-radius: 4px;
  font-size: 1rem;
  box-sizing: border-box;
}

.form-group input:focus {
  outline: none;
  border-color: #1a73e8;
}

@media (max-width: 768px) {
  .balances-container,
  .operations-container {
    flex-direction: column;
  }
}
```

### Step 4: Build and Test

Now let's build and test our updated application:

1. Make sure your local Katana fork is still running
2. Build the examples:

   ```sh
   bun run build:examples
   ```

3. Navigate to the wrapping example and start the server:

   ```sh
   cd dist-examples/wrapping
   npx http-server
   ```

4. Open your browser to the displayed URL (usually `http://localhost:8080`)
5. Connect your wallet to see your balances
6. Try wrapping some ETH into bvbEth and vice versa

The application will show you the current state of the Katana network and allow
you to interact with the bvbEth token contract directly.

## Extending the Example

Here are some ideas to further enhance your application:

1. **Add support for other tokens**:
    - Read and display AUSD balances
    - Interact with other vbTokens (yield-bearing vault bridge tokens)

2. **Improve the UI**:
    - Add loading indicators during transactions
    - Show transaction history
    - Display the current gas cost estimation

3. **Add advanced bvbEth functions**:
    - Implement `transfer()` to send bvbEth to another address
    - Implement `approve()` and `transferFrom()` for allowances

## Conclusion

In this tutorial, you've learned how to read ETH and bvbEth balances, wrap ETH
into bvbEth, and unwrap bvbEth back to ETH using a local Katana fork. These are
fundamental operations for interacting with DeFi applications on Katana.

The ability to convert between ETH and bvbEth is essential for participating in
DeFi protocols, as most require tokens to conform to the ERC20 standard.
Additionally, bvbEth provides yield-bearing capabilities that can enhance your
DeFi strategies. With this knowledge, you're now ready to explore more complex
interactions with Katana's "money legos."
