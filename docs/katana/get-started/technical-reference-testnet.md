## Bokuto

| Network Name   | Bokuto                                                                                       |
| :------------- | :------------------------------------------------------------------------------------------- |
| Chain ID       | 737373                                                                                       |
| RPC URL        | [https://rpc-bokuto.katanarpc.com](https://rpc-bokuto.katanarpc.com)                         |
| Block explorer | [https://bokuto.katanascan.com/](https://bokuto.katanascan.com/)                             |
|                | [https://explorer-bokuto.katanarpc.com/](https://explorer-bokuto.katanarpc.com/)             |
| Bridge UI      | SOON                                                                                         |
| Faucet         | [https://faucet.katana.tools/](https://faucet.katana.tools/)                                 |
|                | Join the [@katanadevs public developer community group](https://t.me/katanadevs) for a code. |

### Chain Configuration

| Gas Token         | ETH       |
| :---------------- | :-------- |
| Block time        | 1 second  |
| Gas Block Limit   | 60M units |
| Gas Pricing       | EIP1559   |
| Data Availability | EIP4844   |

### Interacting with the Unified Bridge

| Agglayer Documentation       | [https://docs.agglayer.dev/](https://docs.agglayer.dev/)                                                                               |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Unified Bridge Address       | `0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582`                                                                                           |
| Sepolia contract             | [0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582](https://sepolia.etherscan.io/address/0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582)          |
| Bokuto contract              | [0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582](https://explorer-bokuto.katanarpc.com/address/0x528e26b25a34a4A5d0dbDa1d57D318153d2ED582) |
| Bokuto Bridge Service API    | [https://rpc-bridge-bokuto.katanarpc.com/](https://rpc-bridge-bokuto.katanarpc.com/)                                                   |
| NetworkId (i.e. Agglayer Id) | `37`                                                                                                                                   |

For L2<->L2 transactions through the Agglayer, you'll need the other L2's Bridge Service API. The Unified Bridge Address lives in the same address across all testnets.

### Testnet Tokens

Flow: Asset (Sepolia) -> Vault Bridge Token (Sepolia) -> Vault Bridge Token (Katana)

| Name                | Network | Address                                                                                                                        |
| :------------------ | :------ | :----------------------------------------------------------------------------------------------------------------------------- |
| `WETH`              | Sepolia | [0x04d08c8525B55c409201289C4ff5a204fa437d9f](https://sepolia.etherscan.io/address/0x04d08c8525B55c409201289C4ff5a204fa437d9f)  |
| `WBTC`              | Sepolia | [0x8dbBbF4E801774265171D7e101a9f346Fa6f56bD](https://sepolia.etherscan.io/address/0x8dbBbF4E801774265171D7e101a9f346Fa6f56bD)  |
| `USDC`              | Sepolia | [0xCea1D25a715eC34adFB2267ACe127e8D107778dd](https://sepolia.etherscan.io/address/0xCea1D25a715eC34adFB2267ACe127e8D107778dd)  |
| `USDT`              | Sepolia | [0xDA9E6CAA9F85aE060BCcd6a789E0C7D39A33e24f](https://sepolia.etherscan.io/address/0xDA9E6CAA9F85aE060BCcd6a789E0C7D39A33e24f)  |
| `USDS`              | Sepolia | [0x5956982345967Dbc9648cD133c2fECb1eF132AE6](https://sepolia.etherscan.io/address/0x5956982345967Dbc9648cD133c2fECb1eF132AE6)  |
|                     |         |                                                                                                                                |
| `Vault Bridge ETH`  | Sepolia | [0x188FFFc2562C67aCdB9a0CD0B819021DDfC82A6B](https://sepolia.etherscan.io/address/0x188FFFc2562C67aCdB9a0CD0B819021DDfC82A6B)  |
| `Vault Bridge WBTC` | Sepolia | [0x2CE29070ee5e65C4191d5Efca8E85be181F34B6d](https://sepolia.etherscan.io/address/0x2CE29070ee5e65C4191d5Efca8E85be181F34B6d)  |
| `Vault Bridge USDC` | Sepolia | [0xb62Ba0719527701309339a175dDe3CBF1770dd38](https://sepolia.etherscan.io/address/0xb62Ba0719527701309339a175dDe3CBF1770dd38)  |
| `Vault Bridge USDT` | Sepolia | [0xdd9aCdD3D2AeC1C823C51f8389597C6be9779B28](https://sepolia.etherscan.io/address/0xdd9aCdD3D2AeC1C823C51f8389597C6be9779B28)  |
| `Vault Bridge USDS` | Sepolia | [0x406F1A8D91956d8D340821Cf6744Aa74c666836C](https://sepolia.etherscan.io/address/0x406F1A8D91956d8D340821Cf6744Aa74c666836C)  |
|                     |         |                                                                                                                                |
| `WETH (aka vbETH)`  | Katana  | [0x84b3493fA9B125A8EFf1CCc1328Bd84D0B4a2Dbf](https://bokuto.katanascan.com/address/0x84b3493fA9B125A8EFf1CCc1328Bd84D0B4a2Dbf) |
| `WBTC (aka vbWBTC)` | Katana  | [0xe8255B44634b478aB10a649c6C207A654473dbed](https://bokuto.katanascan.com/address/0xe8255B44634b478aB10a649c6C207A654473dbed) |
| `USDC (aka vbUSDC)` | Katana  | [0xc2a4C310F2512A17Ac0047cf871aCAed3E62bB4B](https://bokuto.katanascan.com/address/0xc2a4C310F2512A17Ac0047cf871aCAed3E62bB4B) |
| `USDT (aka vbUSDT)` | Katana  | [0xf6801557e17131Da48Fd03B2c34172872F936345](https://bokuto.katanascan.com/address/0xf6801557e17131Da48Fd03B2c34172872F936345) |
| `USDS (aka vbUSDS)` | Katana  | [0x801f719178d9b85D4948ed146C50596273885a75](https://bokuto.katanascan.com/address/0x801f719178d9b85D4948ed146C50596273885a75) |

---

### Appendix A \- OpCodes {#appendix-a---opcodes}

[All OpCodes supported by op-geth](https://github.com/ethereum-optimism/op-geth/blob/optimism/core/vm/opcodes.go)

### Appendix B \- Precompiles {#appendix-b---precompiles}

P256VERIFY (RIP-7212)

### Appendix C \- Predeploys {#appendix-c---predeploys}

Note: Katana has a subset of predeploys from op-geth (See [here](https://docs.optimism.io/stack/smart-contracts#layer-2-contracts-predeploys) for the full list of predeploys)

| Contract                              | Address                                        | Modified | Usable  |
| :------------------------------------ | :--------------------------------------------- | -------- | ------- |
| WETH9 (You should use Katana's WETH)  | 0x4200000000000000000000000000000000000006     | No       | **Yes** |
| SequencerFeeVault                     | 0x4200000000000000000000000000000000000011     | No       | **Yes** |
| GasPriceOracle                        | 0x420000000000000000000000000000000000000F     | No       | **Yes** |
| GovernanceToken                       | 0x4200000000000000000000000000000000000042     | No       | **Yes** |
| L1Block                               | 0x4200000000000000000000000000000000000015     | No       | **Yes** |
| ProxyAdmin                            | 0x4200000000000000000000000000000000000018     | No       | **Yes** |
| BaseFeeVault                          | 0x4200000000000000000000000000000000000019     | No       | **Yes** |
| L1FeeVault                            | 0x420000000000000000000000000000000000001a     | No       | **Yes** |
| SchemaRegistry                        | 0x4200000000000000000000000000000000000020     | No       | **Yes** |
| EAS (Ethereum Attestation Service)    | 0x4200000000000000000000000000000000000021     | No       | **Yes** |
| BeaconBlockRoot                       | 0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02     | No       | **Yes** |
| ~~L2CrossDomainMessenger~~            | ~~0x4200000000000000000000000000000000000007~~ | Yes      | **No**  |
| ~~L2StandardBridge~~                  | ~~0x4200000000000000000000000000000000000010~~ | Yes      | **No**  |
| ~~OptimismMintableERC20Factory~~      | ~~0x4200000000000000000000000000000000000012~~ | No       | **No**  |
| ~~L2ToL1MessagePasser~~               | ~~0x4200000000000000000000000000000000000016~~ | Yes      | **No**  |
| ~~L2ERC721Bridge~~                    | ~~0x4200000000000000000000000000000000000014~~ | Yes      | **No**  |
| ~~OptimismMintableERC721Factory~~     | ~~0x4200000000000000000000000000000000000017~~ | No       | **No**  |
| ~~DEPRECATED \- LegacyMessagePasser~~ | ~~0x4200000000000000000000000000000000000000~~ | No       | **No**  |
| ~~DEPRECATED \- DeployerWhitelist~~   | ~~0x4200000000000000000000000000000000000002~~ | No       | **No**  |
| ~~DEPRECATED \- LegacyERC20ETH~~      | ~~0xDeadDeAddeAddEAddeadDEaDDEAdDeaDDeAD0000~~ | No       | **No**  |
| ~~DEPRECATED \- L1BlockNumber~~       | ~~0x4200000000000000000000000000000000000013~~ | No       | **No**  |
