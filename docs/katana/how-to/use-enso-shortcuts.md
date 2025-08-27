# Integrate Enso DeFi Shortcuts

Enso [DeFi Shortcuts](https://docs.enso.build/pages/build/get-started/overview) simplify protocol interactions by offering a unified interface to different DeFi protocols. 

Enso's crosschain APIs return **executable atomic transaction objects** that represent the optimal route between positions:

- [**/route API**](https://docs.enso.build/pages/build/get-started/route): Define input/output positions and receive optimized execution pathways.  
- [**/bundle API**](https://docs.enso.build/pages/build/get-started/bundling-actions): Construct custom sequences of protocol interactions.

The supporting APIs provide comprehensive data about:

- available [positions](https://navigator.enso.build/tokens),  
- integrated projects and [protocols](https://docs.enso.build/pages/build/get-started/protocol-data#get-supported-protocols),
- [interaction methods](https://docs.enso.build/pages/build/reference/actions) - the standardized interfaces enabling declarative route construction,  
- portfolio [holdings and DeFi positions](https://docs.enso.build/pages/build/get-started/balances) for any wallet address.

For additional examples, browse the [Use Case Library](https://docs.enso.build/pages/use-cases).

## Integration

To accelerate development, explore:

- [Crosschain Route Widget](https://happypath.enso.build) \- an off-the-shelf React component enabling crosschain routing of DeFi positions and native tokens, available as a standalone application or for [embedding into your platform](https://docs.enso.build/pages/templates/cross-chain-route-widget)  
- Available [projects](https://navigator.enso.build/projects?&chainId=1) and [positions](https://navigator.enso.build/tokens?chainId=1)   
- Building with Enso through REST [API](https://docs.enso.build/pages/api-reference/overview) & [SDK](https://github.com/EnsoBuild/sdk-ts) integration


## Deposit USDT into Steer AUSD-USDC Automated Liquidity Management

This route converts USDT into a Steer UniV3 vault position by splitting the input and depositing into a dual-token vault. The route uses Enso's splitting functionality to optimize token allocation.

[Try this route →](https://happypath.enso.build/?tokenIn=0x2DCa96907fde857dd3D816880A0df407eeB2D2F2&outChainId=747474&chainId=747474&tokenOut=0x8Dc76B0dcF92A1F5C163896F1D04557ef3DB21cE&amountIn=1000000)

**Route mechanics:**

1. Split `USDT` into two paths via enso
2. Swap `USDT` to `AUSD` via sushiswap-router
3. Swap `USDT` to `USDC` via sushiswap-router
4. Deposit `AUSD` and `USDC` to `STEERUV3` via steer

```ts
export async function vaultBridgeUsdtSteerUniv3Vault_3AusdUsdc(){
  const chainId = 747474;
  const TOKEN_IN = "0x2DCa96907fde857dd3D816880A0df407eeB2D2F2"; // USDT
  const TOKEN_OUT = "0x8Dc76B0dcF92A1F5C163896F1D04557ef3DB21cE"; // STEERUV3
  const amountIn = parseUnits("1", 6);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Convert USDT to Morpho Blue steakUSDC Lending Vault

This route bridges USDT to steakUSDC through a simple swap and deposit mechanism using Morpho Blue vaults.

[Try this route →](https://happypath.enso.build/?tokenIn=0x2DCa96907fde857dd3D816880A0df407eeB2D2F2&outChainId=747474&chainId=747474&tokenOut=0x61D4F9D3797BA4dA152238c53a6f93Fb665C3c1d&amountIn=1000000)

**Route mechanics:**

1. Swap `USDT` to `USDC` via sushiswap-router
2. Deposit `USDC` to `steakUSDC` via morpho-blue-vaults

```ts
export async function vaultBridgeUsdtSteakusdc(){
  const chainId = 747474;
  const TOKEN_IN = "0x2DCa96907fde857dd3D816880A0df407eeB2D2F2"; // USDT
  const TOKEN_OUT = "0x61D4F9D3797BA4dA152238c53a6f93Fb665C3c1d"; // steakUSDC
  const amountIn = parseUnits("1", 6);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Convert USDC to Midas Restaked SOL (Re7SOL)

This route converts USDC to Midas Re7SOL tokens through SOL bridging and Midas protocol deposit.

[Try this route →](https://happypath.enso.build/?tokenIn=0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36&outChainId=747474&chainId=747474&tokenOut=0xC6135d59F8D10c9C035963ce9037B3635170D716&amountIn=1000000)

**Route mechanics:**

1. Swap `USDC` to `uSOL` via sushiswap-router
2. Deposit `uSOL` to `mRe7SOL` via midas-rwa

```ts
export async function usdcMidasRe7sol(){
  const chainId = 747474;
  const TOKEN_IN = "0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36"; // USDC
  const TOKEN_OUT = "0xC6135d59F8D10c9C035963ce9037B3635170D716"; // mRe7SOL
  const amountIn = parseUnits("1", 6);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Migrate bbqUSDC Morpho Position to BitVault Staked bvUSD

This route redeems bbqUSDC tokens and converts them to sbvUSD through a redemption and deposit flow.

[Try this route →](https://happypath.enso.build/?tokenIn=0x1445A01a57D7B7663CfD7B4EE0a8Ec03B379aabD&outChainId=747474&chainId=747474&tokenOut=0x24E2aE2f4c59b8b7a03772142d439fDF13AAF15b&amountIn=1000000000000000000)

**Route mechanics:**

1. Redeem `bbqUSDC` to `USDC` via morpho-blue-vaults
2. Swap `USDC` to `bvUSD` via sushiswap-router
3. Deposit `bvUSD` to `sbvUSD` via bitvault-sbvusd

```ts
export async function bbqusdcSbvusd() {
  const chainId = 747474;
  const TOKEN_IN = "0x1445A01a57D7B7663CfD7B4EE0a8Ec03B379aabD"; // bbqUSDC
  const TOKEN_OUT = "0x24E2aE2f4c59b8b7a03772142d439fDF13AAF15b"; // sbvUSD
  const amountIn = parseUnits("1", 18);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Convert USDC to Steer SUSHI-USDC Automated LP Management

This route creates a Steer UniV3 vault position by splitting USDC into SUSHI and USDC components.

[Try this route →](https://happypath.enso.build/?tokenIn=0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36&outChainId=747474&chainId=747474&tokenOut=0x4d3C354499389c52F1090Ba4c79bf4F4C083b274&amountIn=1000000)

**Route mechanics:**

1. Split `USDC` into two paths via enso
2. Swap `USDC` to `SUSHI` via sushiswap-router
3. Deposit `SUSHI` and `USDC` to `STEERUV12` via steer

```ts
export async function usdcSteerUniv3Vault_12() {
  const chainId = 747474;
  const TOKEN_IN = "0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36"; // USDC
  const TOKEN_OUT = "0x4d3C354499389c52F1090Ba4c79bf4F4C083b274"; // STEERUV12
  const amountIn = parseUnits("100", 6);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Transform USDC into Steer LBTC-WBTC Bitcoin LP Strategy

This route converts USDC into a BTC-focused Steer vault position through dual token splitting and deposit.

[Try this route →](https://happypath.enso.build/?tokenIn=0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36&outChainId=747474&chainId=747474&tokenOut=0x433ebd9268a3B76413DbE94698bFdb589Ff95D8E&amountIn=1000000)

**Route mechanics:**

1. Split `USDC` into two paths via enso
2. Swap `USDC` to `LBTC` via sushiswap-router
3. Swap `USDC` to `WBTC` via sushiswap-router
4. Deposit `LBTC` and `WBTC` to `STEERUV26` via steer

```ts
export async function usdcSteerUniv3Vault_26LbtcWbtc() {
  const chainId = 747474;
  const TOKEN_IN = "0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36"; // USDC
  const TOKEN_OUT = "0x433ebd9268a3B76413DbE94698bFdb589Ff95D8E"; // STEERUV26
  const amountIn = parseUnits("100", 6);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Zap USDC to SushiSwap USDC-ETH Liquidity Pool

This route creates SushiSwap LP tokens by splitting USDC and providing liquidity to the USDC/ETH pool.

[Try this route →](https://happypath.enso.build/?tokenIn=0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36&outChainId=747474&chainId=747474&tokenOut=0xf9B1AE5F1929F9A4De548e98e0393ae1A9d1D0f8&amountIn=1000000)

**Route mechanics:**

1. Split `USDC` into two paths via enso
2. Swap `USDC` to `wETH` via sushiswap-router
3. Deposit `USDC` and `wETH` to `SLP` via sushiswap-v2

```ts
export async function usdcSushiswapLpToken() {
  const chainId = 747474;
  const TOKEN_IN = "0x203A662b0BD271A6ed5a60EdFbd04bFce608FD36"; // USDC
  const TOKEN_OUT = "0xf9B1AE5F1929F9A4De548e98e0393ae1A9d1D0f8"; // SLP
  const amountIn = parseUnits("100", 6);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## usdcVbethYvault

This route swaps USDT to ETH and deposits into a Yearn vault for vbETH exposure.

[Try this route →](https://happypath.enso.build/?tokenIn=0x2DCa96907fde857dd3D816880A0df407eeB2D2F2&outChainId=747474&chainId=747474&tokenOut=0xE007CA01894c863d7898045ed5A3B4Abf0b18f37&amountIn=1000000)

**Route mechanics:**

1. Swap `USDT` to `wETH` via sushiswap-router
2. Deposit `wETH` to `yvvbETH` via yearn-v3

```ts
export async function steakhouseHighYieldUsdcVbethYvault() {
  const chainId = 747474;
  const TOKEN_IN = "0x1445A01a57D7B7663CfD7B4EE0a8Ec03B379aabD"; // bbqUSDC
  const TOKEN_OUT = "0xE007CA01894c863d7898045ed5A3B4Abf0b18f37"; // yvvbETH
  const amountIn = parseUnits("1", 18);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Convert bbqUSDC to Yearn vbETH Vault Strategy

This route redeems bbqUSDC, converts to ETH, and deposits into a Yearn vbETH vault.

[Try this route →](https://happypath.enso.build/?tokenIn=0x1445A01a57D7B7663CfD7B4EE0a8Ec03B379aabD&outChainId=747474&chainId=747474&tokenOut=0xE007CA01894c863d7898045ed5A3B4Abf0b18f37&amountIn=1000000000000000000)

**Route mechanics:**

1. Redeem `bbqUSDC` to `USDC` via morpho-blue-vaults
2. Swap `USDC` to `wETH` via sushiswap-router
3. Deposit `wETH` to `yvvbETH` via yearn-v3

```ts
export async function steakhouseHighYieldUsdcVbethYvault() {
  const chainId = 747474;
  const TOKEN_IN = "0x1445A01a57D7B7663CfD7B4EE0a8Ec03B379aabD"; // bbqUSDC
  const TOKEN_OUT = "0xE007CA01894c863d7898045ed5A3B4Abf0b18f37"; // yvvbETH
  const amountIn = parseUnits("1", 18);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
```

## Migrate WBTC to SushiSwap USDC-ETH LP Position via Multi-Swap

This route uses WBTC to create SushiSwap LP tokens through token splitting and liquidity provision.

[Try this route →](https://happypath.enso.build/?tokenIn=0x0913DA6Da4b42f538B445599b46Bb4622342Cf52&outChainId=747474&chainId=747474&tokenOut=0xf9B1AE5F1929F9A4De548e98e0393ae1A9d1D0f8&amountIn=100000000)

**Route mechanics:**

1. Split `WBTC` into two paths via enso
2. Swap `WBTC` to `USDC` via sushiswap-router
3. Swap `WBTC` to `wETH` via sushiswap-router
4. Deposit `USDC` and `wETH` to `SLP` via sushiswap-v2

```ts
export async function wbtcSushiswapLpToken() {
  const chainId = 747474;
  const TOKEN_IN = "0x0913DA6Da4b42f538B445599b46Bb4622342Cf52"; // WBTC
  const TOKEN_OUT = "0xf9B1AE5F1929F9A4De548e98e0393ae1A9d1D0f8"; // SLP
  const amountIn = parseUnits("1", 8);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}
```

## Transform WBTC to Midas Restaked SOL with High Slippage

This route converts WBTC to Midas Re7SOL tokens through SOL bridging with extremely high price impact.

[Try this route →](https://happypath.enso.build/?tokenIn=0x0913DA6Da4b42f538B445599b46Bb4622342Cf52&outChainId=747474&chainId=747474&tokenOut=0xC6135d59F8D10c9C035963ce9037B3635170D716&amountIn=100000000)

**Route mechanics:**

1. Swap `WBTC` to `uSOL` via sushiswap-router
2. Deposit `uSOL` to `mRe7SOL` via midas-rwa

```ts
export async function wbtcMidasRe7sol() {
  const chainId = 747474;
  const TOKEN_IN = "0x0913DA6Da4b42f538B445599b46Bb4622342Cf52"; // WBTC
  const TOKEN_OUT = "0xC6135d59F8D10c9C035963ce9037B3635170D716"; // mRe7SOL
  const amountIn = parseUnits("1", 8);

  const routeParams: RouteParams = {
    fromAddress: userAddress,
    receiver: userAddress,
    chainId: chainId,
    destinationChainId: 747474,
    amountIn: [amountIn.toString()],
    tokenIn: [TOKEN_IN],
    tokenOut: [TOKEN_OUT],
    routingStrategy: "router",
    slippage: "150",
    referralCode: "yield-stack"
  };

  const approvalData = await client.getApprovalData({
    amount: amountIn.toString(),
    chainId: chainId,
    tokenAddress: TOKEN_IN,
    fromAddress: userAddress,
  });
  
  await sendEoa(approvalData.tx, approvalData.gas);
  const route = await client.getRouteData(routeParams);
  await sendEoa(route.tx, route.gas);
  return route;
}

```

## Resources

- [Enso Website](https://enso.build)  
- [Crosschain Route Widget](https://happypath.enso.build)  
- [Developer Docs](https://docs.enso.build)  
- [Use Case Library](https://docs.enso.build/pages/use-cases)  
- [Developer Support](https://t.me/enso_intent_engine)
