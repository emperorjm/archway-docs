---
sidebar_position: 1
title: About Archway
description: A self-sovereign blockchain network that allows developers to express and get rewarded for their creativity.
---

# About Archway

Archway is an incentivized smart contract platform that rewards developers that build on the network with baked in incentives and rewards.

The protocol gives developers the tools to quickly build and launch scalable cross-chain dapps and get rewarded for the value the dapps contribute to the network.

## The Archway Mission

The overarching goal of Archway is to foster an array of diverse and sustainable applications by empowering and rewarding developers that build on the network.

The internet helped democratize access to information, but it hasn't fully democratized access to capital and value. The mission of Archway is to give access to entrepreneurs and developers worldwide who lack access to the same opportunities as their counterparts in more developed nations.

Built into the protocol, Archway rewards developers based on the value and impact of their dapp rather than based on someone's connections or close ties to funding. This will level the playing field for many locked out of the world of raising capital.  

Unlike early Web 3 and Web 2.0 platforms, entrepreneurs and developers building on Archway get an actual stake in the growth and governance of the protocol itself, as all contributors are rewarded.

<!-- Architecture -->

## Architectural Overview

Archway is a Cosmos SDK blockchain powered by [Tendermint](https://tendermint.com/) for the networking and consensus layers. The network uses modified Cosmos modules to manage inflation and rewards system, including: 

- **Minting** - Responsible for minting tokens and managing inflation.
- **CosmWasm** - A runtime for WebAssembly Smart Contracts.
- **Staking** - Manages the changes to validators.
- **Governance** - Supports the one-vote-per-token voting and governance system for token holders.
- **IBC** - Inter-Blockchain Communication protocol that enables asset transfers across Cosmos blockchains.

In addition to using the predefined Cosmos modules, Archways uses two custom modules for additional features of the network:

- **Rewards** - Calculates and distributes rewards to smart contracts based on the transaction volume it brings to the network.
- **Tracking** - Enables the tracking of gas consumption on a transaction basis.

Archway creates a Cosmos SDK-based chain with support for WASM smart contract execution that rewards developers for using the deployed smart contracts.

The combination of both predefined Cosmos modules and custom Archway modules allows the network to deliver the following features:

- [Fee reward to the developers](#economic-overview)
- [Gas rebate to end users](#gas-rebates)
- [Smart contract premium fee](#smart-contract-fees)
- [Inflation rewards](#inflation)

## Smart Contracts

The Archway protocol uses [CosmWasm](./glossary.md#cosmwasm), WebAssembly (Wasm), Go, and Rust. Over 40 high-level programming languages support Wasm, including C and C++, TypeScript, Python, and Java.

### **Difference Between Smart Contracts and Cosmos Chains**

While Cosmos makes it easier for developers to create their self-sovereign chains, developers still need to attract and sufficiently incentivize a strong community of validators to run the network. In many cases, it makes sense for a project to deploy as a dapp first.

Consider the following tradeoffs between building your core logic as a smart contract versus building your logic as an independent Cosmos blockchain.

|       Goal               | Cosmos Chain | Smart Contract|
|      :----:              |    :----:    |    :----:     |
|Speed of development      | -            | +             | 
|Ease of development       | -            | +             | 
|Complexity of logic       | +            | +             | 
|Maintenance overhead      | -            | +             | 
|Level of customization    | +            | -             | 
|Strict resource control   | -            | +             | 
|Native chain features     | +            | -             | 
|Scalability               | +            | +             | 


<!-- Architecture : Modules -->

## Module Specifications

These Archway-specific modules extend the functionality of the Cosmos SDK.

### Gas Tracking

To achieve the Archway architecture [desired properties](#the-archway-mission), the gas tracking module follows this design:

1) Wrap the [CosmWasm](./glossary.md#cosmwasm) VM with an Archway GasMeter to allow interception and tracking of information passed by the VM.
  
2) During BeginBlock, the recorded information is brought to memory and processed to determine rewards for each of the contracts executed in the last block. 

3) Rewards are calculated at a later block than the correspondent transaction with the smart contract

Rewards need to be intentionally claimed through a separate transaction. Also note that while the owner address is allowed to change smart contract metadata, rewards can be claimed **only** by the address specified for receiving rewards

#### Drawbacks

The Cosmos SDK does not support post-transaction processing. Archway takes into account GasLimit instead of actual gas consumption by the transaction. The good news is that the Cosmos SDK could potentially have post-transaction functionality in the future.


<!-- Economics -->

## Economic Overview

With Archway, dapps can earn a portion of transaction fees and inflationary rewards in exchange for their contributions to the network. This earning model is similar to how validators are rewarded for operating nodes in a typical Proof-of-Stake (PoS) chain.
 
Archway seeks to provide three potential funding sources for dapps:

- [Gas fee rebates](#gas-rebates) 
- [Share of inflationary rewards](#inflation)
- [Smart contract fees (optional)](#smart-contract-fees)

Archway allows each dapp developer and their community to configure how rewards are managed and distributed. When a contract is instantiated, the dapp creator specifies an `owner` address where all fees and rewards are automatically deposited. This target address can be the creator's address, a multisig address, or an address that is controlled by a separate custom contract. Ownership can then be transferred to a new address as needed. Ownership transfer requires only the signature of the previous owner. 

<!-- Economics : Gas Rebates -->

### Gas Rebates 

Unlike other smart contract platforms, Archway does not burn gas fees or distribute them entirely to the validators or miners. Instead, the collected gas fees are split between dapp developers and validators.

At network launch, gas fees are divided evenly with 50% going to dapp developers and 50% to validators. The network provides configurable parameters that can be adjusted over time to determine the optimal gas rebate distribution between validators and dapps.

From the dapp developer perspective, a contract receives a 50% rebate on all gas paid. From the validator perspective, deferring a portion of rewards in the near term effectively drives transaction volumes, fees, and increases the value of the underlying network in the future.

It would not be profitable for an attacker to spam transactions on the network as gas rebates recoup only part of the fees paid (50%). As an additional safeguard against potential abuse and to prevent the deployment of spam contracts, gas fees are higher for uploading new contracts than for routine transactions. Gas fees are still sufficiently low to allow smaller projects to upload contracts.

Gas fee rebates are automatically paid out by the network on a per-block basis.

<!-- Economics : Inflation -->

### Inflation

The overall inflation rate on the Archway network follows the model of the [mint module](https://github.com/gavinly/CosmosParametersWiki/blob/master/Mint.md) in the Cosmos Hub, with total token supply increasing between 7% and 20% annually, depending on the ratio of tokens that are actively staked on the network.

The Archway protocol then shares a portion of these total inflation rewards directly with dapps. At genesis, 25% of inflation rewards go to dapp developers and 75% go to validators. For example, if the network sees total annual inflation of 8%, then 2% goes to dapps and 6% goes to validators. These distribution values are configurable parameters that can be adjusted by the Archway community through network governance.

The dapp rewards pool (2% in the previous example) is then proportionally distributed based on the relative amount of gas fees that each dapp generates within a given epoch. For example, a dapp that is responsible for 10% of gas consumption is awarded 10% of the available pool.

To mitigate potential Sybil attacks and ensure spamming transactions is not profitable, each dapp has a max inflation rewards cap. At network launch, there is a hard cap based on the total gas fees paid per dapp. A contract deployed to Archway cannot earn rewards greater than the total gas it generates within an epoch. The implementation of a dynamic rewards cap is being actively researched and can be updated through future governance. Transitioning to a floating cap provides more flexible distribution and further incentivizes developers to continuously improve their dapps.

Any surplus in rewards is contributed to the Archway community pool where the funds are managed through governance.

Inflationary rewards are paid out by the network on a per-block basis.

<!-- Economics : Fees -->

### Smart Contract Fees

Smart contract platforms today charge network fees (“gas”) based on the amount of computational processing required by on-chain transactions. While this method of measurement works for the underlying economics of a network, it does not support use cases where a developer has to cover additional costs such as distributed storage, access to off-chain processing, external data sources, or other premium features such as audited and insured contracts. 

With Archway, developers of dapps can define custom fees for interacting with their smart contracts. Also known as the _take rate_, this fee provides developers with a flexible option to charge different fee levels that are based on their specific use case and operational needs.

By default, the smart contract fee is set to 0 $ARCH. On initial deployment, the dapp developer can define their fee. The fee is configurable and the dapp owner can adjust it at any time, even after the contract has been deployed.

To streamline the user experience, the smart contract fee is embedded directly in the network fee, so end users are simply presented with a single combined fee when signing a transaction.

Since most dapps are composed of smaller, more modular pieces of code and layers of contracts, individual developers can focus on building even smaller snippets of code. They can write and monetize a single contract rather than a fully featured dapp. Since contracts can be integrated into multiple dapps, developers can earn multiple lines of fees across any user base that interacts with their code. Imagine, for example, the rapid app development that would happen if every NPM package earned fees for computational use.


<!-- Economics : Examples -->

### Example Use Cases

Dapps can choose to allocate the rewards they accrue in whatever way is most beneficial to their ecosystem. For instance, rewards can be [redistributed to governance token holders](#governance-rewards), used to [support core development teams](#support-the-core-development-team), [cover users’ gas fees](#subsidize-gas-payments), [seed liquidity pools](#boost-liquidity-mining-programs), [contribute to a community DAO](#fund-a-decentralized-autonomous-organization-dao), open project bounties, sponsor events or hackathons, and much more.

#### Governance Rewards

A recent trend has dapps issuing governance tokens to incentivize and bootstrap early communities. This token distribution has proved to be very successful in a number of cases,  despite the dapp not having any clear mechanism for value capture for fees or revenues. Some of these tokens merely grant voting rights, but have still been the subject of intense growth due to speculation of the future utility of the dapp or potential rights to cash flows by governance token holders. 

In the Archway model, dapp developers can redistribute their share of network rewards directly to their governance token holders, subject to local securities laws. The Archway model turns standard governance tokens into productive, yield-generating assets. 

While individual dapps on other networks, such as SushiSwap, have implemented similar functionality, the Archway protocol makes it possible to implement and manage governance rewards at the protocol level itself. A developer can opt-in and automatically redirect to governance token holders the funds earned by the dapp, including gas rebates, inflation rewards, and even smart contract fees. 


#### Support the Core Development Team

Rewards offered by Archway could help developers bootstrap new projects without having to dedicate excessive time and focus to fundraising. The process of raising early capital can be difficult for developers of individual dapps. 

Development teams often resort to private insider sales that skew early token distribution or are forced to rely on foundation grant programs. Rather than prioritizing the best technology and ecosystem for their dapp, developers often choose a tech stack that is based on available grants and are then locked into a single platform.

Even after a dapp is launched, developers continuing to support the dapp codebase may find it difficult to cover ongoing development costs as the blockchain industry is still nascent and winning business models have yet to materialize.

The model introduced by Archway can counter some of these early financial pressures by allocating dapp rewards directly to core development teams. This funding source could serve as a supplement to help sustain projects and allow the team to focus on what really matters — shipping the best possible product, growing their user base, and supporting the community.


#### Subsidize Gas Payments

Gas payments remain one of the critical barriers to delivering a simple, intuitive user experience for dapps. Archway supports gasless transactions through the use of pool accounts. A dapp can pull funds from a common pool to sponsor gas payments and completely abstract away that complexity and friction for their end users.

To fund the pool account balance, a developer can use the fees and rewards generated by the dapp itself. In effect, the dapp gets a 50% discount on each transaction from gas rebates along with inflation rewards and contract fees that are potentially available as a bonus. 

By recycling these funds, dapps have the option to dramatically reduce or eliminate the gas burden for end users, leading to a smoother onboarding process and stronger retention over time.

#### Boost Liquidity Mining Programs

DeFi projects could use the funds earned from Archway to boost liquidity mining rewards, fulfilling a vital strategy for attracting new users and encouraging active participation.   

For instance, a decentralized exchange (DEX) can be designed to distribute rewards on top of the fees already paid to their liquidity providers (LPs), while a lending platform can allow users to earn supplemental rewards for depositing and borrowing assets.

Although similar matching and subsidy programs have been run elsewhere as temporary months-long promotions, the steady drip of Archway rewards creates unique opportunities to serve as a continual source for boosted incentives.

#### Fund a Decentralized Autonomous Organization (DAO)

Rewards generated by a dapp can be contributed to a community-owned DAO that is focused on coordinating and funding critical work for its ecosystem. These rewards can be continuously deposited to the DAO treasury that allows members to collectively manage and deploy assets based on the specific needs of the project.

This DAO could then vote to fund core development teams, sponsor events and hackathons, commission code audits, open bug bounties, launch education programs, subsidize third-party integrations, and so on. DAO funding could be available for anything and everything that could potentially benefit and impact the ecosystem.

The DAO itself can exist and operate as a set of smart contracts on top of Archway, so the entire process is automated and transparent throughout initial rewards collection, voting, grants distribution, and so on.

Giving members collective control over a recurring funding source helps actively engage the dapp’s community and contributes toward progressively decentralizing the project itself.


#### Reduce DEX Fees

On networks such as Ethereum, DEX users incur two sets of fees: a network fee and a swap fee. The network fee incentivizes miners to operate nodes and the swap fee incentivizes liquidity providers (LPs) to provide liquidity to the DEX.

By deploying to Archway, DEXs could be constructed to effectively eliminate one of the network or swap fees. Instead, the DEX protocol could redistribute the earned gas rebates back to the LPs for providing liquidity to dramatically lower total transaction costs on automatic swaps and improve the user experience.


<!-- Governance -->

## Governance

Governance is the process by which Archway network participants and token holders can influence the future direction of the protocol through proposals and on-chain voting.

In addition to fees and staking, Archway’s native token is used for governance. This governance model helps maintain and support Archway’s decentralized community while ensuring fair and transparent participation. All holders of the native token can propose changes to the Archway protocol and vote on active proposals. Proposals that reach a consensus threshold are adopted, whether a specific feature or even changing the governance system itself.
