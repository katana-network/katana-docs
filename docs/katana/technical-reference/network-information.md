Katana runs on Agglayer's [CDK OP Stack](https://www.agglayer.dev/cdk), which means it's powered by Geth, the settlement is secured by zk-proofs, and is connected to Ethereum and other L2s canonically through Agglayer's Unified Bridge.

Learn more on [L2Beat](https://l2beat.com/scaling/projects/katana).

### Basic Info

| Chain Name              | Katana                                                                     |
| :---------------------- | :------------------------------------------------------------------------- |
| Chain ID                | [747474](https://chainlist.org/chain/747474)                               |
| Official Public RPC URL | [https://rpc.katana.network/](https://rpc.katana.network/)                 |
| (Tenderly Public RPC)   | [https://katana.gateway.tenderly.co/](https://katana.gateway.tenderly.co/) |
| (Conduit Public RPC)    | [https://rpc.katanarpc.com/](https://rpc.katanarpc.com/)                   |
| Gas Token               | ETH                                                                        |
| Block explorer          | [https://katanascan.com/](https://katanascan.com/)                         |
| Canonical Bridge UI     | [https://bridge.katana.network/](https://bridge.katana.network/)           |

Notes: Our official public RPC is fronted by a load balancer. Users are rate limited at 100 requests/second.
WSS is not supported on it, but you can use the Tenderly/Conduit public RPCs (or use another [RPC provider](https://docs.katana.network/katana/technical-reference/infra-partners/#rpc-providers)).

### Chain Configuration

<table>
    <tbody>
        <tr>
            <td>Block time</td>
            <td>1 second</td>
        </tr>
        <tr>
            <td>Block Gas Limit</td>
            <td>60M units</td>
        </tr>
        <tr>
            <td>Block Gas Target</td>
            <td>30M units</td>
        </tr>
        <tr>
            <td>Gas Pricing</td>
            <td>EIP1559</td>
        </tr>
        <tr>
            <td>EIP-1559 elasticity multiplier</td>
            <td>60</td>
        </tr>
        <tr>
            <td>EIP-1559 denominator</td>
            <td>250</td>
        </tr>
        <tr>
            <td>Data Availability</td>
            <td>EIP4844</td>
        </tr>
        <tr>
            <td>Account Abstraction</td>
            <td>EIP7702</td>
        </tr>
    </tbody>
</table>
