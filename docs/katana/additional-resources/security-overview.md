Katana is leveraging OP Stack and zero-knowledge technology to provide secure, scalable, and interoperable infrastructure for decentralized applications.

As a project incubated and supported by GSR and Polygon Labs, Polygon Labs is providing services to support Katana Foundation on all security matters. As a result, this page outlines the processes that are followed.

### Security

Engineering personnel are mandated to adhere to secure coding guidelines and industry best practices for secure development, including standards promulgated by OWASP, which furnishes developers with guidelines, tools, and resources to discern and remediate security vulnerabilities.

Initiating with practices such as threat modeling and risk assessments, prospective security threats and vulnerabilities within systems and applications are methodically identified and prioritized. These anticipatory strategies permit efficient resource allocation, concentrating on domains that present the most substantial risks.

### Internal Security Assessments

A multidisciplinary team of security engineers maintains an internal security organization. This team has conducted rigorous reviews and audits of all components concurrently with external vendor assessments. Such a process ensures supplementary evaluations and assessments for each component, while also incorporating a diverse range of expertise and perspectives.

## Core Components Security

### OP Stack Integration

Katana utilizes a minimally customized version of the OP Stack, augmented with Succinct’s SP1 prover. This combination ensures compatibility with OP Stack while incorporating zk-based security and proof mechanisms.

Security Assessments:

- OP Stack: [https://docs.optimism.io/stack/security/audits-report](https://docs.optimism.io/stack/security/audits-report)
- Succinct SP1: [https://github.com/succinctlabs/sp1/tree/dev/audits](https://github.com/succinctlabs/sp1/tree/dev/audits)

### Agglayer

Agglayer is the foundational interoperability protocol, enabling secure, atomic interactions across heterogeneous chains using a common messaging format.

- October 2024 – Security Code Audit by SigmaPrime
- March 2025 – Security Code Audit by SigmaPrime
- April 2025 – Security Code Audit by SigmaPrime

Public security reports: [https://github.com/agglayer/agglayer/tree/main/docs/audits](https://github.com/agglayer/agglayer/tree/main/docs/audits)

### Agglayer Unified Bridge

The Unified Bridge powers interoperability between Layer 2 chains and Ethereum (L1), enabling secure, efficient, and trust-minimized cross-chain messaging and asset transfers.

Security Assessments:

- Numerous security code audits performed throughout its lifecycle by reputable firms such as SigmaPrime, Separbit, Hexens and others

Public security reports: [https://github.com/agglayer/agglayer-contracts/tree/main/audits](https://github.com/agglayer/agglayer-contracts/tree/main/audits)

### KAT Token

KAT is the native ERC-20 token of the Katana Network. It is central to governance, staking, and ecosystem incentives.
Security Assessments:

- March 2025 – Security Code Audit & Formal Verification by Certora
- April 2025 – Security Code Audit & Formal Verification by Certora

Public Security final report: [https://github.com/katana-network/kat-token/tree/main/audit](https://github.com/katana-network/kat-token/tree/main/audit)

### Vault Bridge

The VaultBridge, a component of the Agglayer, provides customizable, yield-generating mechanisms. It is designed to offer sustainable revenue for ecosystem growth by reducing extractive models.

Security Assessments:

- March 2025 – Security Code Audit by SigmaPrime
- April 2025 – Security Code Audit by ChainSecurity
- May 2025 – Security Code Audit & Formal Verification by Certora

Public security reports: [https://github.com/agglayer/vault-bridge/tree/main/audits](https://github.com/agglayer/vault-bridge/tree/main/audits)

## Disclaimer

_The information presented by Katana Services (Cayman) Ltd. (“Katana Services”) in this document and in any associated communications (the "Information") is provided for general informational purposes only, is subject to change, and should not be considered financial advice, a recommendation, or an offer to make investments. The Information is subject to the terms and conditions of the Katana Protocol, linked in this site._

_Any references to the KAT token implying a value are based on approximate projections at the time of unlock, approximately 6-9 months from Katana Mainnet launch._

_The Information has not been reviewed or approved by any regulatory authority. The dissemination of the Information does not imply compliance with any legal, regulatory, or jurisdictional requirements. Providing liquidity or using protocols on Katana Network involves risks and uncertainties. We strongly advise consulting a qualified financial advisor to evaluate the suitability of acquiring any tokens mentioned here in alignment with your investment objectives._

_The Information is purely informational and is not personalized for any individual or token holder. Katana ServicesFoundation makes no representations or warranties as to the accuracy or completeness of the Information and accepts no liability for any errors or omissions._
