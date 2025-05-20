There are many forms of risk users are exposed to while using Katana Network. This section provides an overview of the various forms of risks and steps that have been taken to mitigate this risk.

The categories of risk are, but not limited to: smart contract risk (bugs or exploit in code); protocol risk (failures in the core network); counterparty risk (third-party negligence or misbehaviour); duration risk (withdrawal delays); and liquidation risk (losses due to depegs or forced unwinds). 

## **Smart contract risk**

Smart contract risk refers to the potential for bugs or exploits in the code that powers Katana Network’s infrastructure. All bridging, vault, and yield-generating activity rely on smart contracts, including ERC-4626 vaults and custom bridge logic that handles direct-to-vault flows and vbToken issuance on Katana Network through the Agglayer. While all of these smart contracts have undergone security audits, there is always the possibility of an unknown exploit. 

A complete list of Katana Network’s smart contracts can be found in the technical reference [here](/get-started/technical-reference/). 

## **Protocol risk**

A system-level failure within the network could potentially lead to loss of funds or network liveness. While Katana Network minimizes certain kinds of protocol risk through the use of ZK proofs, ZK provers are still emergent technology. Learn more about CDK-opgeth.

## **Counterparty risk**

Vault assets are managed by third-party risk curators Gauntlet and Stakehouse Financial. While curators define risk parameters, including collateral thresholds and supported markets, they use Chainlink price feeds and other battle-tested oracle solutions for high-liquidity/high-volume designs.

While they operate transparently and at the direction of Katana Foundation, their decisions can impact the rewards used to incentivize activity on the network. 

## **Duration risk**

Because certain tokens bridged to Katana Network will be allocated to yield-generating strategies, there exists the possibility of duration risk. 

In practice, this means that if a large number of users attempt to withdraw simultaneously, it may be necessary to process redemptions on a rolling basis. Users may face a delay when claiming; in the most extreme circumstances, the delay would be 7 days. While the yield-generating strategies for tokens locked in the bridge will be limited to large-cap collateral and no undercollateralized lending, duration risk is inherent to the network’s design. 

If there is a run on the bridge or mass redemptions:

VaultBridge is essentially a fractional reserve bridge. Not all assets are instantly liquid. However, VaultBridge aims to keep:

* \~15% in instant liquidity  
* \~50% redeemable within 48 hours  
* \~100% within 7 days

If a redemption exceeds these buffers:

* Slippage may occur in fast intent-based exits, but users can always wait and redeem over time.  
* Katana’s Chain-Owned Liquidity (CoL) and insurance buffers act as backstops, absorbing pressure during stress events.

**Liquidity Risk**   
Liquidity risk on Katana may arise when the network scales up gas capacity (supply) but actual demand lags behind. In this case, sequencer fees start to dry up, and so does the CoL runway.

Katana has levers to redirect a portion of network revenue – e.g. from VaultBridge, core app yields or emissions – towards CoL. Ideally, this would help stimulate more user activity and bring demand back up, which in turn would revive sequencer fee flow.

## **Liquidation risk**

Liquidation risk on Katana may arise when bridged assets used as collateral experience a depeg, i.e., a significant and sustained drop below their value. The cause of such a depeg can occur as a result of counterparty or smart contract risks:

* Counterparty risk: Applies to asset-backed stablecoins like USDC or AUSD, where the depeg could stem from insolvency, reserve mismanagement, or regulatory action affecting the issuing entity.

* Smart contract risk: Applies to decentralized or algorithmic stablecoins where failures in the underlying smart contract logic or incentive mechanisms can cause instability.

In both cases, a depeg can trigger liquidations and impact the recoverable value of user assets at the time of withdrawal, increasing systemic risk within the broader Katana ecosystem.

## **Operational safety** 

At mainnet launch, there will be several safety guardrails meant to account for or mitigate these risks.

### **Insurance reserve**

To mitigate the duration risk of unwinding any yield-generating activities, USDC, USDT, ETH, and USDS are maintained in an insurance reserve. This reserve would limit the potential of any withdrawal delays in the event a large number of users withdraw a particular token simultaneously.  

The Chain-Owned Liquidity (CoL) of Katana also acts as a backstop during this scenario.

### **Risk curators**

The yield-generating activities will be managed by risk curators [Gauntlet](https://www.gauntlet.xyz/) and [Steakhouse Financial](https://www.steakhouse.financial/). These curators design and oversee vault strategies, selecting markets and setting parameters based on risk modeling and asset quality. 

While the risk curators shape risk profiles and optimize for performance, they never custody user assets or have governance control. Their role is advisory and strategic, ensuring vaults remain secure, transparent, and aligned with the Katana Foundation.

### **Katana DeFi Security Council**

To protect against protocol and smart contract risk, Katana Network uses a Security Council that has the power to perform emergency upgrades in the event of a bug.  If necessary, the Council is also about to halt bridge withdrawals. The composition of the Council includes all members of the Katana Consortium, including Conduit, GSR, Stakehouse, Morpho, Yearn, and others. 

For more information about Katana governance, see [here](/core-concepts/governance/). 