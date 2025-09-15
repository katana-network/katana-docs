## Verifying Contracts

After you deploy your smart contracts to Katana (mainnet) / Bokuto (testnet), you will want
to verify them on the block explorer.

### Verifying contracts on Katanascan

1. You will need [Foundry nightly release](https://docs.etherscan.io/etherscan-v2/contract-verification/verify-with-foundry)
2. You will need [Etherscan's multichain API key](https://info.etherscan.com/etherscan-api-v2-multichain/)
3. Don't use `--verifier-url`, but use instead the chain name; example:

```
forge verify-contract --watch --chain katana 0xc094c224ce0406bc338e00837b96ad2e265f7287 src/GmWorld.sol:GmWorld --verifier etherscan --etherscan-api-key YourEtherscanApiKey
```

NOTE: [Hardhat](https://hardhat.org/) also works.

### Verifying contracts on Blockscout

You can either follow Blockscout's instructions for e.g. [Foundry](https://docs.blockscout.com/devs/verification/foundry-verification), [Hardhat](https://docs.blockscout.com/devs/verification/hardhat-verification-plugin), or do it through the [UI](https://docs.blockscout.com/devs/verification/blockscout-ui).

You will need the Blockscout URL for the environment you're targeting:

- [Katana Blockscout Explorer](https://explorer.katanarpc.com/)
- [Bokuto Blockscout Explorer](https://explorer-bokuto.katanarpc.com)

## Debugging

Katana has Tenderly as one of its infrastructure partners. This means you can leverage their transaction debugging and simulation tooling.

Here's an [example of a failed transaction](https://dashboard.tenderly.co/tx/0x1c0ddb1e37b1bb70208dd7d0f6df7fc5b41fe43324666f51e608df037c44eef9) and associated stack trace.
