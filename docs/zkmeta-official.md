# ZkMeta and X-Matrix Labs

Authors: Chris Guo, Kyrie Lu, Nathan Yang

## Table of Contents
* [What is ZkMeta](#what-is-zkmeta)
* [What goals is ZkMeta trying to accomplish](#what-goals-is-zkmeta-trying-to-accomplish)
* [What makes ZkMeta so special](#what-makes-zkmeta-so-special)
* [ZkMeta architecture](#zkmeta-architecture)
* [What is X-Matrix Labs](#what-is-x-matrix-labs)
* [What are X-Matrix Labs's main focuses](#what-are-x-matrix-labss-focuses)
    * [Gateway](#gateway)
    * [Privacy](#privacy)
    * [DeFi](#defi)
    * [Storage](#storage)
* [References](#references)
    * [Privacy related](#privacy-related)
    * [Labs design](#labs-design)

## What is ZkMeta?
ZkMeta is a privacy protection computing framework developed by X-Matrix Labs.
In essence, ZkMeta is a Web3.0 privacy protection infrastructure that aims to protect user and business data
in lieu of the General Data Protection Regulation (GDPR).
ZkMeta provides customized privacy preserving solutions suitable for various businesses.
In addition, ZkMeta offers pre-optimized technical selections and solutions.
Currently, ZkMeta includes Zero-Knowledge Proof (ZKP) protocols such as ZK-3D and ZK-Rollup.

## What goals is ZkMeta trying to accomplish?
ZkMeta has a modularized design that fully embraces operability and configurability.
With ZkMeta, you can get a full package with the most secure, reliable and pluggable
privacy-preserving services with formidable cryptographic support.
Finally, ZkMeta ensures that data is censored, trackable, verifiable and not tempered with.

## What makes ZkMeta so special?
The ZK3D protocol of ZkMeta realizes value transfer in a setting where data is fully private.
This protocol is capable of performing 4 arithmetic operations, range proof and size comparison on encrypted data.
There are 4 outstanding features of the ZK3D protocol:
1. Straightforward: no overhead on user operations; no additional knowledge is needed to use the protocol;
1. Decentralized: users fully own their data without compromise;
1. Fast: proofs are generated and verified within milliseconds;
1. Secure: rigorous cryptographic proof.

In addition, the ZK-Rollup protocol of ZkMeta utilizes the most up-to-date PLONK ZKP algorithm as PLONK does Trust Setup for only once.
The top 3 features of the ZK-Rollup protocol are:
1. Extremely low transaction fees;
1. Trustless protocols;
1. Cryptographically secure, similar to the Ethereum mainnet.

The Rollup validator(s) can never corrupt the state or steal funds (unlike Sidechains).
Users can always retrieve the funds from the Rollup even if validators stop cooperating
   because the data is available (unlike Plasma).
Thanks to validity proofs, neither users nor a single trusted party needs to be online to monitor Rollup blocks
   in order to prevent fraud (unlike payment channels or Optimistic Rollups).

## ZkMeta Architecture
* **Core layer**
  is built on top of the newest theoretical breakthroughs of Zero-Knowledge-Proof (ZKP)
  such as optimizations of performance, trust setting and security,
  all of which will be published in academic papers.

* **Platform layer**
  is where wallets with novel features can be developed.
  It supports privacy-preserving and offline transactions and
  multi-party signing, and is adaptable to multiple blockchains.
  On the platform, you can improve the current ZKP toolkits as well as their documentation.
  Similar concepts are borrowed from the zkSync and AZTEC projects.
  
  The platform has its own value exchange protocols to leverage privacy-preserving data.
  In addition, the platform can perform the 4 basic arithmetic operations,
  range proof and size comparisons on encrypted data.

* **Application layer**
  captures the latest market trend and supports fast prototyping.
  In a short term, this layer aims at Non-Fungible Tokens (NFT) and anonymous auction.

## What is X-Matrix Labs?
X-Matrix Labs is a research and development team passionate about cryptography, blockchain, and math.
The team consists of researchers from prestigious institutes and professionals from engineering and financial industries.
Our mission is to build a decentralized network to empower Web 3.0.
We focus on open finance, cross-chain compatibility, and privacy-preserving transparency.
We aim to empower the Web 3.0 networks by making the network smoother, more secure, and more boundaryless.

## What are X-Matrix Labs's focuses?
### Gateway
We aim to build gateways among data, digital assets, and smart contracts to maximize the value of data through blockchain technologies and eventually to make Web3 smoother.

### Privacy
X-Matrix Labs has an industry-leading cryptography and privacy research lab that provides ZKP-based solutions for Web3.\
Users take full ownership of their private data in a trustless setting.

### DeFi
Stone Protocol is the only yield management protocol focused on creating *Rock Solid Yield* for all users in the DeFi ecosystem.
X-Matrix Labs is dedicated to building the next-generation cross-chain DeFi protocols on top of the Substrate framework.
We aim to boost the value flow between multiple platforms and create *Rock Solid Yield* for millions of users.

### Storage
X-Matrix Labs has sufficient experience in distributed file systems such as IPFS and Filecoin to empower our data storage systems.

## References
### Privacy-related
* [Findora](https://findora.org)
* [Horizen](https://www.horizen.io)
* [zkSync](https://zksync.io/)
* [aztec](https://aztec.network/index.html)
* [WeDPR](https://wedpr-lab.readthedocs.io/zh_CN/latest/docs/introduction.html)

### Labs design
* [Parity](https://www.parity.io/)
* [Polkadot](https://polkadot.network/)
* [VeuTelescope](https://vuetelescope.com/explore?ui.slug=buefy&framework_null=true&_sort=lastDetectedAt%3Adesc)
* [Leasy](https://leasy.co/)
