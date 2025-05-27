### Chain Info

**Note: Tatara will deprecate on or around the release of Public Mainnet. A new testnet will release along with it**

| Network Name | Tatara Network (aka Katana Testnet) |
| :---- | :---- |
| Chain ID | 129399 |
| RPC URL | [https://rpc.tatara.katanarpc.com/](https://rpc.tatara.katanarpc.com/)<apikey\> |

| Block explorer | [https://explorer.tatara.katana.network/](https://explorer.tatara.katana.network/) |
| :---- | :---- |
| Faucet | [https://katana-faucet-api.polygon.technology/api-docs/](https://katana-faucet-api.polygon.technology/api-docs/)  |
| Bridge UI | [https://portal-staging.polygon.technology/bridge](https://portal-staging.polygon.technology/bridge)  |

### Chain Configuration

| Gas Token | ETH |
| :---- | :---- |
| Block time | 1 second |
| Gas Block Limit | 60M units |
| Gas Pricing | EIP1559 |
| Data Availability | EIP4844 |

| OpCodes | [Appendix A \- Opcodes](#appendix-a---opcodes) |
| :---- | :---- |
| Precompiles | [Appendix B \- Precompiles](#appendix-b---precompiles) |
| Predeploys | [Appendix C \- Predeploys](#appendix-c---predeploys) |

### RPC Access

There are two steps for generating the API key needed to access the Katana testnet environment:  
	1\. Request an API key from the Katana API Request Form by following the instructions [here](https://forms.clickup.com/90131276658/f/2ky3w8vj-4573/KHGUSZC4VHAMX9XMYN)   
2\. Begin deploying your application by appending your API key to the following URL: [https://rpc.tatara.katanarpc.com/](https://rpc.tatara.katanarpc.com/)<apikey\>

### Services and General Infrastructure \[Testnet\]

| RPC Providers | Conduit RPC |
| :---- | :---- |
| **Block Explorers** | Blockscout |
| **Oracles** | Conduit Randomness Oracle |
| **Bridges / Interoperability** | Agglayer Unified Bridge |
| **Wallets** | Safe (contracts only) |

### Interacting with the Unified Bridge

**Agglayer Documentation**: [https://docs.agglayer.dev/](https://docs.agglayer.dev/)  
**Testnet Addresses and Endpoints**:

| Agglayer Unified Bridge | 0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582 | [Katana](https://explorer.tatara.katana.network/address/0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582), [Sepolia](https://sepolia.etherscan.io/address/0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582)  |
|  |  |  |
| Bridge Service API | [https://bridge.tatara.katanarpc.com](https://bridge.tatara.katanarpc.com) |  |

### Interacting with the Core Applications and Core Assets

#### **DEX \- Sushi**

Sushi Documentation: [https://www.sushi.com/academy](https://www.sushi.com/academy)  
Source Code: [v3](https://github.com/sushiswap/v3-core)  
Testnet Addresses: 

| SushiRouter | 0xAC4c6e212A361c968F1725b4d055b47E63F80b75 |
| :---- | :---- |
| V3Factory | 0x9B3336186a38E1b6c21955d112dbb0343Ee061eE |
| V3PositionManager | 0x1400feFD6F9b897970f00Df6237Ff2B8b27Dc82C |

#### **Lending/Borrowing \- Morpho**

Morpho Documentation: [https://docs.morpho.org/](https://docs.morpho.org/)  
Source Code: [morpho-blue v1.0.0](https://github.com/morpho-org/morpho-blue)  
Testnet Addresses:

| Morpho Blue | 0xC263190b99ceb7e2b7409059D24CB573e3bB9021 |
| :---- | :---- |
| Morpho Adaptive IRM | 0x9eB6d0D85FCc07Bf34D69913031ade9E16BD5dB0 |
| Morpho Chainlink Oracle V2 Factory | 0xe795DD345aD7E1bC9e8F6B4437a21704d731F9E0 |
| MetaMorpho Factory 1.1 | 0x505619071bdCDeA154f164b323B6C42Fc14257f7 |
| Bundler3 | 0xD0bDf3E62F6750Bd83A50b4001743898Af287009 |
| Public Allocator | 0x8FfD3815919081bDb60CD8079C68444331B65042 |

#### **Yield Aggregator \- Yearn**

Yearn Documentation: [https://docs.yearn.fi/](https://docs.yearn.fi/)  
Source Code: [yearn-vaults-v3.0.4](https://github.com/yearn/yearn-vaults-v3)  
Testnet Addresses:

| yvAUSD ERC-4626 | 0xAe4b2FCf45566893Ee5009BA36792D5078e4AD60 |
| :---- | :---- |
| yvWETH ERC-4626 | 0xccc0fc2e34428120f985b460b487eb79e3c6fa57 |

#### **Native Stablecoin \- AUSD**

AUSD Documentation: [https://developer.agora.finance/](https://developer.agora.finance/)  
Source Code: [Blockscout](https://explorer.tatara.katana.network/address/0xa9012a055bd4e0eDfF8Ce09f960291C09D5322dC?tab=contract)  
Testnet Address:

| AUSD ERC-20 | 0xa9012a055bd4e0eDfF8Ce09f960291C09D5322dC |
| :---- | :---- |
| AUSD Faucet \[testnet only\] | 0xd236c18D274E54FAccC3dd9DDA4b27965a73ee6C |

#### **Native Bitcoin Wrapper \- Lombard**

Lombard Documentation: [https://docs.lombard.finance/](https://docs.lombard.finance/)  
Source Code:   
Testnet Address: NOT YET DEPLOYED

#### **VaultBridge Tokens**

**Note: The names and contract addresses of the vbTokens listed below may change.**  
Source Code: Contracts are verified on-chain  
Testnet Addresses:

| Mock USDC | 0x2b9Ca0A8C773bb1B92A3dDAE9F882Fd14457DACc | Sepolia |
| :---- | :---- | :---: |
| Mock USDC BaseSepolia | 0x3c95BB5f49F3643558aa8F699403564A652FBeB0 | BaseSepolia |
| Mock USDT | 0x18fDA3c97Ea92A04D1636D84948624b414D0058E | Sepolia |
| Mock USDS | 0xfC7b006bDEd8e5D4A55FbaC7A91dAf3753f085CD | Sepolia |
| Mock WBTC | 0xd67A804510739C33c578162A26324C83DCFC0a0A | Sepolia |
| vbETH | 0x4CcD4CbDE5Ec758cCBf75f0be280647Ff359c17a | Sepolia |
| WETH Custom Token | 0x17B8Ee96E3bcB3b04b3e8334de4524520C51caB4 | Tatara |
| WETH Native Converter | 0x3aFbD158CF7B1E6BE4dAC88bC173FA65EBDf2EcD | Tatara |
| vbUSDC | 0x4C8414eBFE5A55eA5859aF373371EE3233fFF7CD | Sepolia |
| vbUSDC Custom Token | 0x102E14ffF48170F2e5b6d0e30259fCD4eE5E28aE | Tatara |
| vbUSDC Native Converter | 0x28FDCaF075242719b16D342866c9dd84cC459533 | Tatara |
| vbUSDT | 0xb3f50565f611D645e0DDB44eB09c4588B1601514 | Sepolia |
| vbUSDT Custom Token | 0xDe51Ef59663e79B494E1236551187399D3359C92 | Tatara |
| vbUSDT Native Converter | 0x8f3a47e64d3AD1fBdC5C23adD53183CcCD05D8a4 | Tatara |
| vbWBTC | 0xa278D086289f71a30D237feccBAF3698E43Bc5D6 | Sepolia |
| vbWBTC Custom Token | 0x1538aDF273f6f13CcdcdBa41A5ce4b2DC2177D1C | Tatara |
| vbWBTC Native Converter | 0x3Ef265DD0b4B86fC51b08D5B03699E57d52C9B27 | Tatara |
| vbUSDS | 0x56b89A124376CB0481c93C3d94f821F262Dc0D7A | Sepolia |
| vbUSDS Custom Token | 0xD416d04845d299bCC0e5105414C99fFc88f0C97d | Tatara |
| vbUSDS Native Converter | 0x56342E6093381E2Bd732FFd6141b22136efB98Bf | Tatara |

#### **Asset Importer \- Universal Assets**

Documentation: [https://docs.universal.xyz/universal-protocol](https://docs.universal.xyz/universal-protocol)  
Source Code:   
Testnet Addresses: 

| uBTC ERC-20 | 0xB295FDad3aD8521E9Bc20CAeBB36A4258038574e |
| :---- | :---- |
| uSOL ERC-20 | 0x79b2417686870EFf463E37a1cA0fDA1c7e2442cE |
| uXRP ERC-20 | 0x26435983DF976A02C55aC28e6F67C6477bBd95E7 |

### Other Relevant Contracts

| Agglayer Unified Bridge | 0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582 | Tatara |
| :---- | :---- | :---: |
| [Safe(opens in a new tab)](https://github.com/safe-global/safe-smart-account/blob/v1.3.0/contracts/GnosisSafe.sol) | 0x69f4D1788e39c87893C980c06EdF4b7f686e2938 | Tatara |
| [SafeL2(opens in a new tab)](https://github.com/safe-global/safe-smart-account/blob/v1.3.0/contracts/GnosisSafeL2.sol) | 0xfb1bffC9d739B8D520DaF37dF666da4C687191EA | Tatara |
| [MultiSend(opens in a new tab)](https://github.com/safe-global/safe-smart-account/blob/v1.3.0/contracts/libraries/MultiSend.sol) | 0x998739BFdAAdde7C933B942a68053933098f9EDa | Tatara |
| [MultiSendCallOnly(opens in a new tab)](https://github.com/safe-global/safe-smart-account/blob/v1.3.0/contracts/libraries/MultiSendCallOnly.sol) | 0xA1dabEF33b3B82c7814B6D82A79e50F4AC44102B | Tatara |
| [Safe Singleton Factory(opens in a new tab)](https://github.com/safe-global/safe-singleton-factory/blob/main/source/deterministic-deployment-proxy.yul) | 0x914d7Fec6aaC8cd542e72Bca78B30650d45643d7 | Tatara |
| Multicall 1 | 0x4e1d97344FFa4B55A2C6335574982aa9cB627C4F | Tatara |
| Multicall 2 | 0xfC0F3dADD7aE3708f352610aa71dF7C93087a676 | Tatara |
| [Multicall3(opens in a new tab)](https://github.com/mds1/multicall/tree/main) | 0xcA11bde05977b3631167028862bE2a173976CA11 | Tatara |
| [create2Deployer(opens in a new tab)](https://github.com/pcaversaccio/create2deployer) | 0x13b0D85CcB8bf860b6b79AF3029fCA081AE9beF2 | Tatara |
| [CreateX(opens in a new tab)](https://github.com/pcaversaccio/createx) | 0xba5Ed099633D3B313e4D5F7bdc1305d3c28ba5Ed | Tatara |
| [Batch Distributor](https://github.com/pcaversaccio/batch-distributor) | 0x36C38895A20c835F9A6A294821D669995eB2265E | Tatara |
| [Arachnid's Deterministic Deployment Proxy(opens in a new tab)](https://github.com/Arachnid/deterministic-deployment-proxy) | 0x4e59b44847b379578588920cA78FbF26c0B4956C | Tatara |
| [Permit2(opens in a new tab)](https://github.com/Uniswap/permit2) | 0x000000000022D473030F116dDEE9F6B43aC78BA3 | Tatara |
| [ERC-4337 v0.6.0 EntryPoint(opens in a new tab)](https://github.com/eth-infinitism/account-abstraction/tree/v0.6.0) | 0x5FF137D4b0FDCD49DcA30c7CF57E578a026d2789 | Tatara |
| [ERC-4337 v0.6.0 SenderCreator(opens in a new tab)](https://github.com/eth-infinitism/account-abstraction/tree/v0.6.0) | 0x7fc98430eAEdbb6070B35B39D798725049088348 | Tatara |
| [ERC-4337 v0.7.0 EntryPoint(opens in a new tab)](https://github.com/eth-infinitism/account-abstraction/tree/v0.7.0) | 0x0000000071727De22E5E9d8BAf0edAc6f37da032 | Tatara |
| [ERC-4337 v0.7.0 SenderCreator(opens in a new tab)](https://github.com/eth-infinitism/account-abstraction/tree/v0.7.0) | 0xEFC2c1444eBCC4Db75e7613d20C6a62fF67A167C | Tatara |
| RIP-7212 | 0x0000000000000000000000000000000000000100 | Tatara |
| Seaport 1.6 | 0x0000000000FFe8B47B3e2130213B802212439497 | Tatara |
| Seaport Conduit Controller | 0x00000000F9490004C11Cef243f5400493c00Ad63 | Tatara |
| Smart Contracts & Security Admin Multisig | 0x165BD6204Df6A4C47875D62582dc7C1Ed6477c17 | Tatara |

### Appendix A \- OpCodes {#appendix-a---opcodes}

[All OpCodes supported by op-geth](https://github.com/ethereum-optimism/op-geth/blob/optimism/core/vm/opcodes.go)

### Appendix B \- Precompiles {#appendix-b---precompiles}

P256VERIFY (RIP-7212)

### Appendix C \- Predeploys {#appendix-c---predeploys}

Note: Katana has a subset of predeploys from op-geth (See [here](https://docs.optimism.io/stack/smart-contracts#layer-2-contracts-predeploys) for the full list of predeploys)

| Contract | Address | Modified | Usable |
| :---- | :---- | ----- | ----- |
| WETH9 (You should use Katana's WETH) | 0x4200000000000000000000000000000000000006 | No | **Yes** |
| SequencerFeeVault | 0x4200000000000000000000000000000000000011 | No | **Yes** |
| GasPriceOracle | 0x420000000000000000000000000000000000000F | No | **Yes** |
| GovernanceToken | 0x4200000000000000000000000000000000000042 | No | **Yes** |
| L1Block | 0x4200000000000000000000000000000000000015 | No | **Yes** |
| ProxyAdmin | 0x4200000000000000000000000000000000000018 | No | **Yes** |
| BaseFeeVault | 0x4200000000000000000000000000000000000019 | No | **Yes** |
| L1FeeVault | 0x420000000000000000000000000000000000001a | No | **Yes** |
| SchemaRegistry | 0x4200000000000000000000000000000000000020 | No | **Yes** |
| EAS (Ethereum Attestation Service) | 0x4200000000000000000000000000000000000021 | No | **Yes** |
| BeaconBlockRoot | 0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02 | No | **Yes** |
| ~~L2CrossDomainMessenger~~ | ~~0x4200000000000000000000000000000000000007~~ | Yes | **No** |
| ~~L2StandardBridge~~ | ~~0x4200000000000000000000000000000000000010~~ | Yes | **No** |
| ~~OptimismMintableERC20Factory~~ | ~~0x4200000000000000000000000000000000000012~~ | No | **No** |
| ~~L2ToL1MessagePasser~~ | ~~0x4200000000000000000000000000000000000016~~ | Yes | **No** |
| ~~L2ERC721Bridge~~ | ~~0x4200000000000000000000000000000000000014~~ | Yes | **No** |
| ~~OptimismMintableERC721Factory~~ | ~~0x4200000000000000000000000000000000000017~~ | No | **No** |
| ~~DEPRECATED \- LegacyMessagePasser~~ | ~~0x4200000000000000000000000000000000000000~~ | No | **No** |
| ~~DEPRECATED \- DeployerWhitelist~~ | ~~0x4200000000000000000000000000000000000002~~ | No | **No** |
| ~~DEPRECATED \- LegacyERC20ETH~~ | ~~0xDeadDeAddeAddEAddeadDEaDDEAdDeaDDeAD0000~~ | No | **No** |
| ~~DEPRECATED \- L1BlockNumber~~ | ~~0x4200000000000000000000000000000000000013~~ | No | **No** |

