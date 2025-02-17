---
sidebar_position: 3
---

# Minting and sending tokens

Now that we've got our smart contract deployed we can transact with it to mint tokens that can be [hodl'd](https://academy.binance.com/en/glossary/hodl) or sent to other Archway addresses.

## Minting tokens

[MintMsg](https://github.com/CosmWasm/cw-nfts/blob/v0.9.3/contracts/cw721-base/src/msg.rs#L60-L72) is a message type from the `cw721_base` package imported by our project's [Cargo.toml](https://github.com/archway-network/archway-templates/blob/main/cw721/on-chain-metadata/Cargo.toml). It's used by our contract's execution handler to set state for an NFT with metadata corresponding to the [TokenInfo model](https://github.com/CosmWasm/cw-nfts/blob/v0.9.3/contracts/cw721-base/src/state.rs#L91-L105) we looked at previously.

To mint an NFT, we need to send a transaction to the smart contract with our `MintMsg` parameters in JSON format. For adding any custom traits, since we're using the on-chain metadata NFT template, arbitrary metadata values can be added using the `extensions` attribute of `MintMsg`.

This is the JSON string we will use to mint our test NFT:

```json
{
  "mint": {
    "token_id": "1",
    "owner": "archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq",
    "extension": {
      "name": "Archway NFT #1",
      "description": "Building With NFTs",
      "image": "ipfs://QmZdPdZzZum2jQ7jg1ekfeE3LSz1avAaa42G6mfimw9TEn",
      "attributes": [
        {
          "trait_type": "tutorial",
          "value": "https://docs.archway.io/docs/create/guides/nft-project/start"
        }
      ]
    }
  }
}
```

To execute our mint transaction we add the JSON arguments using the `--args` flag of `tx` command of the [Archway Developer CLI](https://www.npmjs.com/package/@archwayhq/cli).

```bash
archway tx --args '{"mint":{"token_id":"1","owner":"archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq","extension":{"name":"Archway NFT #1","description":"Building With NFTs","image":"ipfs://QmZdPdZzZum2jQ7jg1ekfeE3LSz1avAaa42G6mfimw9TEn","attributes":[{"trait_type":"tutorial","value":"https://docs.archway.io/docs/create/guides/nft-project/start"}]}}}'
```

:::tip
Using most Archway commands, including `tx` and `query`, requires your terminal path to be at the root, or inside, of a folder created with the Archway Developer CLI
::: 

To confirm the NFT is now correctly stored on-chain, run the `query` command, specifying the `token_id` declared in the minting transaction:

```bash
archway query contract-state smart --args '{"nft_info":{"token_id":"1"}}'
# Show output here
```

The behaviour of the `nft_info` entrypoint is defined [here](https://github.com/CosmWasm/cw-nfts/blob/v0.9.3/contracts/cw721-base/src/query.rs#L33-L39) if you want to read the response model in detail.

## Sending tokens

To transfer a token, we have to send a message of the type `TransferNft`, which we achieve by sending a transaction to the [transfer_nft](https://github.com/CosmWasm/cw-nfts/blob/v0.9.3/contracts/cw721-base/src/execute.rs#L124-L139) entrypoint exposed by the contract. The params we send to the entrypoint are: the recipient address; and the `token_id` to be sent to the receiver.

In JSON format, our transaction arguments look like this:

```json
{
  "transfer_nft": { 
    "recipient": "archway1y00hm50lffnxt5m0kuy9afk83gyuye684zwcr5", 
    "token_id": "1" 
  }
}
```

Using the [Developer CLI](https://www.npmjs.com/package/@archwayhq/cli), we broadcast the transaction and include the above parameters like this:

```bash
archway tx --args '{"transfer_nft":{"recipient":"archway1y00hm50lffnxt5m0kuy9afk83gyuye684zwcr5","token_id":"1"}}'
```

Once the transaction confirms ownership of the token will be changed from the address declared as owner at minting (`archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq` in this guides's example), to the new receiver address (`archway1y00hm50lffnxt5m0kuy9afk83gyuye684zwcr5` in this guides's example). To verify that's the case, we can query the contract again to see who owns the `token_id` with the value of `'1'`.

```bash
archway query contract-state smart --args '{"nft_info":{"token_id":"1"}}'
# Show output here
```

Now that the contract is up and running, read on to learn how to build a dapp around the minting and transfer functionality we just tested.