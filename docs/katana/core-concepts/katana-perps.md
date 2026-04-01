# Katana Perps

Katana Perps is a high-performance, non-custodial perpetual futures exchange built natively on the Katana blockchain. The platform combines an off-chain order book and matching engine with on-chain custody and settlement, delivering centralized-exchange-grade speed with decentralized security guarantees. Katana Perps was built by the team behind IDEX, which has been developing non-custodial trading infrastructure since 2017.

Katana Perps is one of the core verticals in the Katana DeFi stack alongside Sushi (spot trading) and Morpho (lending and borrowing). Available markets include BTC-USD, ETH-USD, SOL-USD, KAT-USD, and XRP-USD, with vbUSDC as collateral.

## How Katana Perps Fits Into the Katana Ecosystem

Trading activity on Katana Perps generates sequencer fees, 100% of which are recycled into chain-owned liquidity, deepening core markets across the ecosystem. The vKAT Armory gauge voting system is expanding to include Katana Perps, allowing vKAT holders to direct KAT emissions toward perps liquidity pools and creating a direct link between KAT staking, governance, and perpetual futures trading. Katana Perps also operates a Points Program that rewards organic trading, sustained participation, liquidity contribution, and referrals.

## Key Features

Katana Perps pairs an off-chain central limit order book with millisecond-latency matching against on-chain smart contract settlement on Katana. All funds are held in a smart contract, every order requires a cryptographic wallet signature (EIP-712), and a built-in wallet exit function guarantees fund recovery at all times. Trade execution and settlement are gas-free; gas is only paid on deposits and withdrawals.

The exchange uses cross-margin, where all positions share a single collateral pool for capital efficiency. Order types include market, limit, stop-loss, take-profit, and conditional orders with GTC, GTX (post-only), IOC, and FOK time-in-force options. Katana Perps Connect enables mobile trading via QR code pairing with a desktop wallet using scoped, time-limited session keys.

## API

Katana Perps provides a REST API for market data, authenticated user data, and trading, as well as a WebSocket API for real-time streaming. An official TypeScript/JavaScript SDK handles wallet signatures, authentication, and real-time order book construction. A sandbox environment on the Bokuto testnet is available for development and testing. See the [API documentation](https://api-docs-v1-perps.katana.network) for full details.

## Links

| Resource | URL |
| --- | --- |
| Trade | [perps.katana.network](https://perps.katana.network) |
| Documentation | [docs-perps.katana.network](https://docs-perps.katana.network) |
| API Documentation | [api-docs-v1-perps.katana.network](https://api-docs-v1-perps.katana.network) |
| JavaScript SDK | [github.com/katanaperps/katana-perps-sdk-js](https://github.com/katanaperps/katana-perps-sdk-js) |
| Smart Contracts | [github.com/katanaperps/katana-perps-contracts-niseko](https://github.com/katanaperps/katana-perps-contracts-niseko) |
| Sandbox (Testnet) | [perps-sandbox.katana.network](https://perps-sandbox.katana.network) |
| Discord (#developers) | [discord.gg/katanaperps](https://discord.com/invite/a3HFV3ZJPQ) |
