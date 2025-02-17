---
sidebar_position: 1
title: Creating an NFT project
description: Build NFT projects with Archway's robust code scaffolding templates
---

This guide follows the below workflow:

1. [Creating your project](#creating-your-project)
2. [Customizing your token parameters](#designing-your-tokens)
3. [Deploying your token contract](./deploy.md)
4. [Minting and sending tokens](./interact.md)
5. [Building the NFT dapp](./dapp.mdx)


## Introduction

Non-Fungible Tokens, or NFTs are an important part of the decentralized digital world. 

In this guide, we will learn how to write, deploy, mint and transfer our own NFTs. We will also learn how to build a dapp website we can share with other users, so they can also mint and transfer tokens.

## Creating your project

In the [Setup](../../getting-started/setup.md) section we learned how to create and configure a new Archway project.

If you have not created a project before, navigate to [Setup](../../getting-started/setup.md) to learn what to install and familiarize yourself with so you can complete this step.

Now let’s create a new project, and on the template selection list select the _CW721 with on-chain metadata_ template.

```bash
$ archway new basic-nft
Creating new Archway project...
✔ Do you want to use a starter template? … yes
? Choose a template › - Use arrow-keys. Return to submit.
    Increment
    CW20
    CW721 with off-chain metadata
❯   CW721 with on-chain metadata
    [https://github.com/archway-network/archway-templates/tree/main/cw721/on-chain-metadata]
 Select a testnet to use › Constantine
🔧   Generating template ...
[ 1/35]   Done: .cargo/config
# and so on, until...
 create mode 100644 schema/tokens_response.json
 create mode 100644 src/lib.rs

Successfully created project basic-nft with network configuration constantine-1
$ cd basic-nft
```

## Designing Your Tokens

The asset metadata is a critical element for any NFT. It defines information like the name, image URL, and other properties that can be pulled by NFT marketplaces to show relevant information for the users. Information like rarity, custom traits, etc., all are stored here.

In this example, we will keep our metadata on-chain. In other words, the contract will store the metadata in its internal state.

In the cw721-base code, NFT metadata is contained in the extension property of the `TokenInfo` [struct](https://github.com/CosmWasm/cw-nfts/blob/v0.9.3/contracts/cw721-base/src/state.rs#L91-L105):


```cpp
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct TokenInfo<T> {
    /// The owner of the newly minted NFT
    pub owner: Addr,
    /// Approvals are stored here, as we clear them all upon transfer and cannot accumulate much
    pub approvals: Vec<Approval>,

    /// Universal resource identifier for this NFT
    /// Should point to a JSON file that conforms to the ERC721
    /// Metadata JSON Schema
    pub token_uri: Option<String>,

    /// You can add any custom metadata here when you extend cw721-base
    pub extension: T,
}
```

To use this `extension` property, here's how we define our metadata in the `CW721 with on-chain metadata` template we cloned into our project.

```cpp
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug, Default)]
pub struct Trait {
    pub display_type: Option<String>,
    pub trait_type: String,
    pub value: String,
}

#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug, Default)]
pub struct Metadata {
    pub image: Option<String>,
    pub image_data: Option<String>,
    pub external_url: Option<String>,
    pub description: Option<String>,
    pub name: Option<String>,
    pub attributes: Option<Vec<Trait>>,
    pub background_color: Option<String>,
    pub animation_url: Option<String>,
    pub youtube_url: Option<String>,
}
```

:::info
This code is located in `src/lib.rs` of your project, you can also view it at [https://github.com/archway-network/archway-templates/blob/main/cw721/on-chain-metadata/src/lib.rs](https://github.com/archway-network/archway-templates/blob/main/cw721/on-chain-metadata/src/lib.rs#L9-L30).
:::

