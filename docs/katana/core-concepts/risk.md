There are many forms of risk users are exposed to while using Katana. This section provides an overview of the various forms of risks and steps that have been taken to mitigate this risk.

The categories of risk are, but not limited to: smart contract risk (bugs or exploit in code); protocol risk (failures in the core network); counterparty risk (third-party negligence or misbehaviour); duration risk (withdrawal delays); and liquidation risk (losses due to depegs or forced unwinds). 

## **Smart contract Risk**

Smart contract risk refers to the potential for bugs or exploits in the code that powers Katana's infrastructure. All bridging, vault, and yield-generating activity rely on smart contracts, including ERC-4626 vaults and custom bridge logic that handles direct-to-vault flows and vbToken issuance on Katana through the Agglayer. Even when a smart contract has undergone a security audit there is always the possibility of an unknown exploit.

A complete list of Katana's smart contracts can be found in the technical reference [here](../get-started/technical-reference-testnet.md). 

## **Protocol Risk**

A system-level failure within the network could potentially lead to loss of funds or network liveness. While Katana minimizes certain kinds of protocol risk through the use of ZK proofs, ZK provers are still emergent technology. Learn more about [CDK-opgeth](../core-concepts/cdk-opgeth.md).

## **Third Party Risk**

Vault assets are managed by third-party risk curators, such as Gauntlet and Stakehouse Financial. While curators define risk parameters, including collateral thresholds and supported markets, their services include risk related to the use of smart contracts, and the use of Chainlink price feeds and other oracle solutions for high-liquidity/high-volume designs.

Deposited funds are subject to third-party control and strategy discretion.  Katana does not exercise custody over bridge assets or control over third parties.  VaultBridge utilizes the services of third parties, such as vault curators Gauntlet or Steakhouse Financial, and vault developers Morpho, or Yearn.  VaultBridge deposits are made directly by the user into Morpho vaults, and are subject to smart contract logic and curator policy.  Decisions made by unaffiliated protocol curators or third-party infrastructure providers may affect the performance and recoverability of assets. While these curators operate independently based on risk and strategy models, Katana does not control their actions, and users may be exposed to the outcomes of such decisions, including potential disruptions or reductions in the value or availability of bridged assets. 
For additional information on the services and terms and conditions of such services provided by the Curators, and associated risks related to these third-party service providers, please see: [Gauntlet](https://www.gauntlet.xyz/tos/) and [Steakhouse Financial](https://kitchen.steakhouse.financial/tos/).  


## **Duration Risk**

Because certain tokens bridged to Katana will be allocated to yield-generating strategies, there exists the possibility of duration risk. 

In practice, this means that if a large number of users attempt to withdraw simultaneously, it may be necessary to process redemptions on a rolling basis. Users may face a delay when claiming; in extreme circumstances, the delay would be 7 days, or more. While the yield-generating strategies for tokens locked in the bridge will be limited to large-cap collateral and no undercollateralized lending, duration risk is inherent to the network’s design. 

If there is a run on the bridge or mass redemptions:

VaultBridge is essentially a fractional reserve bridge. Not all assets are instantly liquid. However, VaultBridge pre-determined settings target the following allocations:

* \~15% in instant liquidity  
* \~50% redeemable within 48 hours  
* \~100% within 7 days

If a redemption exceeds these buffers:

* Slippage may occur in fast intent-based exits, but users can wait and redeem over time.  
* Katana’s Chain-Owned Liquidity (CoL) and reserve buffers act as backstops, absorbing pressure during stress events.

**Liquidity Risk**   
Liquidity risk on Katana may arise when the network scales up gas capacity (supply) but actual demand lags behind. In this case, sequencer fees may slow, and CoL  could decrease.

Katana has reserves that utilize network revenue – e.g. from VaultBridge, core app yields or emissions – that can support CoL. Ideally, this could help stimulate more user activity and restore demand, which in turn could revive sequencer fee flow.  

## **Liquidation Risk**

Liquidation risk on Katana may arise when bridged assets used as collateral experience a depeg, i.e., a significant and sustained drop below their value. The cause of such a depeg can occur as a result of counterparty or smart contract risks:

* Counterparty risk: Applies to asset-backed stablecoins like USDC or AUSD, where the depeg could stem from insolvency, reserve mismanagement, or regulatory action affecting the issuing entity.

* Smart contract risk: Applies to decentralized or algorithmic stablecoins where failures in the underlying smart contract logic or incentive mechanisms can cause instability.

In both cases, a depeg can trigger liquidations and impact the recoverable value of user assets at the time of withdrawal, increasing systemic risk within the broader Katana ecosystem.

## **Operational Safety** 

At mainnet launch, there will be several safety guardrails established to mitigate these risks.

### **Reserves**

To mitigate the duration risk of unwinding any yield-generating activities, USDC, USDT, ETH, and USDS are maintained in a reserve. This reserve would limit the likelihood of withdrawal delays in the event a large number of users withdraw a particular token simultaneously.  

The Chain-Owned Liquidity (CoL) of Katana also acts as a backstop during this scenario.

### **Curators**

The yield-generating activities by third parties on Katana present risk.  Yield generating activities on VaultBridge, and on Katana, will be managed by risk curators, such as [Gauntlet](https://www.gauntlet.xyz) and [Steakhouse Financial](https://www.steakhouse.financial).  These curators design and oversee vault strategies, selecting markets and setting parameters based on risk modeling and asset quality. 

While the risk curators shape risk profiles and optimize for performance, they do not custody user assets or have governance control over Katana. Their role is advisory and strategic, vaults remain secure, transparent, and aligned with the Katana ecosystem.

### **VaultBridge**

Deposits on VaultBridge are subject to third-party control and strategy discretion.  Katana does not exercise custody over bridged assets or control over third parties, such as vault curators Gauntlet or Steakhouse Financial, or vault developers Morpho, or Yearn. VaultBridge deposits are made directly into Morpho vaults, and are subject to smart contract logic and curator policy and actions.  Decisions made by protocol curators or third-party infrastructure providers may affect the performance and recoverability of assets. These curators operate independently based on risk and strategy models, Katana does not control their actions, and users may be exposed to the outcomes of such decisions, including potential disruptions or reductions in the value or availability of bridged assets.  VaultBridge functionality depends on third-party protocols.  VaultBridge interacts with third-party smart contracts on Ethereum through the Morpho protocol. Deposits are made to smart contracts that are controlled by Morpho curators (e.g., Gauntlet and Steakhouse Financial). These curators allocate capital in yield-generating strategies, which may fail, lose value, or become illiquid. 

VaultBridge tokens received via the bridge do not entitle users to yield or represent a claim against Morpho or VaultBridge.  VaultBridge and its associated contracts are not subject to community governance through vKAT. Users of the bridge do not acquire voting rights or governance powers by virtue of holding vbTokens or interacting with VaultBridge.

Withdrawals may be delayed or unavailable during periods of stress.  Liquidity buffers are designed to enable redemptions, but such buffers may not be sufficient during a market crisis or mass withdrawal event. In such cases, users may face delays, slippage, or be unable to redeem assets altogether. 
To learn more about Morpho vaults and the terms and conditions of Morpho vaults please visit [Morpho's website](https://morpho.org/).

### **Katana DeFi Security Council**

To protect against protocol and smart contract risk, Katana uses a Security Council that has the power to perform emergency functions.  If necessary, the Council may to halt bridge withdrawals. The composition of the Council includes all members of the Katana Consortium, including Conduit, GSR, Stakehouse, Morpho, Yearn, and others. 

For more information about Katana governance, see [here](../core-concepts/governance.md). 