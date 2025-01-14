---
sidebar_position: 4
---

# Deploying your contract on chain

When you are ready to deploy an on-chain build, we can use either granular commands or the bundled `archway deploy` command. While the granular commands give you total control of the deployment workflow, `archway deploy` bundles them into one command, but will be harder to navigate if you have a failing deployment.

It's recommended to avoid relying on `archway deploy` unless you already have a good understanding of the granular commands.

The granular workflow follows this process:

1. **CREATE ON CHAIN WASM** - _Upload CosmWasm `wasm` executable on chain_
2. **VERIFY UPLOAD INTEGRITY** - _Download stored `wasm` file and verify it matches the checksum of your local build_
3. **INSTANTIATE CONTRACT** - _Create an instance of the uploaded contract_
4. **SET CONTRACT METADATA** - _Configure developer rewards and admin settings_

The commands for achieving this workflow are:

1. `archway store` - Stores and verifies upload integrity
2. `archway instantiate` - Creates an instance of a contract that's been stored on chain
3. `archway metadata` - Configures contract parameters for developer rewards and contract admins


## Storing the contract on chain

Only `wasm` files optimized with the `cosmwasm/rust-optimizer` can be stored on chain. If your local project does not have an `artifacts` folder, or, if the `artifacts` folder is empty. Go back to the [previous guide step](./wasm.md) for information on producing _CosmWasm_ optimized executables.

When the executable is ready to be stored on chain, run the following command:

```
archway store
```

## Instatiating the contract

To skip the CLI asking for default values required by your dapp, include constructor arguments using the `--args` parameter. Format your constructor parameters as a `JSON` encoded string.

```bash
archway instantiate --args '{"my_key":"my value"}'
```

Since we cloned the `Increment` starter template, try instantiating with your `counter` argument set to `0`:

Example:

```bash
archway instantiate --args '{"count":0}'
```

:::note
If you instantiate without using `--args` you'll be prompted by the CLI to enter that information. If your dapp contract doesn't take any constructor arguments use `'{}'` to denote `null` arguments.
:::

So why are we sending our constructor as `{"count":0}` and how can we verify it's correct?

From your project files open `src/contract.rs`. Near the top, is the function `pub fn instantiate`, which works as a constructor and sets the initial state of the contract:

```rust
pub fn instantiate(
  deps: DepsMut,
  _env: Env,
  info: MessageInfo,
  msg: InstantiateMsg,
) -> Result<Response, ContractError> {
  let state = State {
    count: msg.count, // Here's our count declaration
    owner: info.sender.clone(), // Contract owner is wallet that sent tx
  };
  STATE.save(deps.storage, &state)?; // Save the state
  // More code...
}
```

If you're unsure, or suspect something may have gone wrong, you can always check your deployments history using the `archway history` command, or by opening `config.json` at the root of the project and looking at the `deployments` array. 

In your history you should see 2 transactions were created:

- the `create` transaction when it was uploaded to the chain
- the `instantiate` transaction which made it possible to be queried and transacted with

```bash
archway history
```

Example output:

```bash
Printing deployments...

[
  {
    type: 'instantiate',
    chainId: 'constantine-1',
    codeId: 26,
    txhash: 'A060BA9CD256BF3E8C11C2640AEBF8B50EE42D76F2C586E604B581ACE834C76B',
    address: 'archway1u6clujjm2qnem09gd4y7hhmulftvlt6mej4q0dd742tzcnsstt2q70lpu6',
    admin: 'archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq'
  },
  {
    type: 'store',
    codeId: 26,
    chainId: 'constantine-1',
    txhash: '9EAD3FF16B1321E6A4E37EFB07BDAA0143602F28F64CBC5159A925D8ACEB7528'
  }
]
```

## Configuring the deployed contract

Now that the dapp is deployed it's recommended to set its metadata. This will configure the smart contract to collect developer premiums, rewards and can be used to enable gas rebates with a pooling account.

To set contract metadata, use the command:

```bash
archway metadata
```

Example output:

```bash
$ archway metadata
✔ Send tx from which wallet in your keychain? (e.g. "main" or "archway1...") … docker
✔ Developer address which can change the metadata later on (e.g. "archway1...") … archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq
✔ Enter an address to receive developer rewards (e.g. "archway1...") … archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq
✔ Enable a premium on rewards? (enabling this feature will automatically disable gas rebate) … no
✔ Use the contract rewards for gas rebates to the user? … yes
Setting metadata for contract archway1aacn8927thr0cuw9jdw2wvswhlyfm4z05e6uhtr2hqx6wkgq5enszqhhvx on constantine-1...
Enter keyring passphrase: *[hidden]*

# Tx. logs and confirm broadcast...

Successfully set contract metadata on tx hash 1C30EB87FD947CF8D3843E110E4752181E05012EBC2B2C3FCD870DB707EB36F3
```

Checking the deployments history again, another item, of type `'set-metadata'`, has just been added:

```bash
archway history
```

Example output:

```bash
Printing deployments...

[
  {
    type: 'set-metadata',
    chainId: 'constantine-1',
    codeId: 26,
    txhash: '1C30EB87FD947CF8D3843E110E4752181E05012EBC2B2C3FCD870DB707EB36F3',
    contract: 'archway1u6clujjm2qnem09gd4y7hhmulftvlt6mej4q0dd742tzcnsstt2q70lpu6',
    contractMetadata: {
      developerAddress: 'archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq',
      rewardAddress: 'archway1f395p0gg67mmfd5zcqvpnp9cxnu0hg6r9hfczq',
      collectPremium: false,
      gasRebate: false
    }
  },
  // Our previous history entries, for `store` and `instantiate`, appear below...
]
```