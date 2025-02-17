---
sidebar_position: 5
---

# Interacting with your contract

Now, let's try querying and transacting with our deployed contract.

## Querying

Queries read from the blockchain. They don't modify anything stored on chain, so they don't cost a fee.

There are several types of queries we could do, but a common type we're interested in is `contract-state`, which we will call in `smart` mode. This lets us run queries with arguments, as opposed to dumping the entire contract data or metadata.

If we query the `count` before modifying any state, we get the value we set during instantiation:

```bash
archway query contract-state smart --args '{"get_count": {}}'
```

Outputs:
```bash
Attempting query...

{"data":{"count":0}}

Ok!
```

Why was the query argument `'{"get_count": {}}'`?

If we open `src/contract.rs` and inspect the function `pub fn query`, we will see the case matching statement that matches our JSON query:
```rust
pub fn query(deps: Deps, _env: Env, msg: QueryMsg) -> StdResult<Binary> {
  match msg {
      QueryMsg::GetCount {} => to_binary(&query_count(deps)?), // Here it is
  }
}
```

:::info
**Note:** `QueryMsg` is an `enum` with the `GetCount` property, defined in the `src/msg.rs` file. It is good to be aware of the format here, as the enum attribute is uppercase without spaces in Rust, but lowercase with snake case when converted to JSON arguments. This is controlled by the attribute `#[serde(rename_all = "snake_case")]` right above the `QueryMsg` definition.
:::

## Transacting

Transactions write to the blockchain and cost a gas fee for modifying a contract's state securely.

Before submitting a transaction, it is required to know the Minimum Consensus fee of the current block. This is the lowest amount of gas fee that is required to be paid for a transaction to be successful. 

This fee can be found by using the below command. The fee is shown in on transaction gas unit. 

```bash 
archwayd query rewards estimate-fees [gas-limit] [flags]
``` 

By default, gas estimation mode is `auto`, but you've got granular control. To modify gas settings edit the `gas` values inside the `network` object of your `config.json`. However, for most cases, the default gas settings are preferable.

To increment our counter value, we'll be executing a transaction that calls the function `pub fn try_increment` in `src/contract.rs`. This function is already public, but the transaction execution is handled by the function `pub fn execute` in `src/contract.rs`, which does pattern matching to call `try_increment`.

Sending an Increment transaction:

```bash
archway tx --args '{"increment": {}}'
```

Example output:

```bash
Attempting transaction...

Send tx from which wallet in your keychain? (e.g. "main" or ctrl+c to quit): my-wallet
Enter keyring passphrase:
gas estimate: 115945
{"body":{"messages":[{"@type":"/cosmwasm.wasm.v1.MsgExecuteContract","sender":"archway1j6aldkw59usszphp2jc9jlczxjzc76jdzspf8a","contract":"archway1mkymgyhkdly5enpeq7tlyntnxvl539qnam2v3d","msg":"eyJpbmNyZW1lbnQiOnt9fQ==","funds":[]}],"memo":"","timeout_height":"0","extension_options":[],"non_critical_extension_options":[]},"auth_info":{"signer_infos":[],"fee":{"amount":[{"denom":"upebble","amount":"116"}],"gas_limit":"115945","payer":"","granter":""}},"signatures":[]}

confirm transaction before signing and broadcasting [y/N]: y
{"height":"689581","txhash":"FE6CA15FB3B8295A7FFC0AA3FC307E6FE31E2AB606EB58774C2668CC1CACF6E8","data":"0A090A0765786563757465","raw_log":"[{\"events\":[{\"type\":\"execute\",\"attributes\":[{\"key\":\"_contract_address\",\"value\":\"archway1mkymgyhkdly5enpeq7tlyntnxvl539qnam2v3d\"}]},{\"type\":\"message\",\"attributes\":[{\"key\":\"action\",\"value\":\"execute\"},{\"key\":\"module\",\"value\":\"wasm\"},{\"key\":\"sender\",\"value\":\"archway1j6aldkw59usszphp2jc9jlczxjzc76jdzspf8a\"}]},{\"type\":\"wasm\",\"attributes\":[{\"key\":\"_contract_address\",\"value\":\"archway1mkymgyhkdly5enpeq7tlyntnxvl539qnam2v3d\"},{\"key\":\"method\",\"value\":\"try_increment\"}]}]}]","logs":[{"events":[{"type":"execute","attributes":[{"key":"_contract_address","value":"archway1mkymgyhkdly5enpeq7tlyntnxvl539qnam2v3d"}]},{"type":"message","attributes":[{"key":"action","value":"execute"},{"key":"module","value":"wasm"},{"key":"sender","value":"archway1j6aldkw59usszphp2jc9jlczxjzc76jdzspf8a"}]},{"type":"wasm","attributes":[{"key":"_contract_address","value":"archway1mkymgyhkdly5enpeq7tlyntnxvl539qnam2v3d"},{"key":"method","value":"try_increment"}]}]}],"gas_wanted":"115945","gas_used":"98755"}

Ok!
```

Why is the argument `'{"increment": {}}'`?

If we open `src/contract.rs` and inspect the `pub fn execute` function, we'll see a pattern matching statement that matches our JSON argument:

```rust
pub fn execute(
  deps: DepsMut,
  _env: Env,
  info: MessageInfo,
  msg: ExecuteMsg,
) -> Result<Response, ContractError> {
  match msg {
    ExecuteMsg::Increment {} => try_increment(deps), // Here it is
    ExecuteMsg::Reset { count } => try_reset(deps, info, count),
  }
}
```

:::info
**Note:** `enum` attributes again are converted. `ExecuteMsg::Increment {}` becomes `{"increment": {}}` in the CLI.
:::

:::tip
While it doesn't apply to our Increment dapp, for dapps collecting payments, the [MessageInfo](https://docs.rs/cosmwasm-std/latest/cosmwasm_std/struct.MessageInfo.html) struct is how developers can access and process incoming funds. Both native chain assets and [cw20](https://docs.cosmwasm.com/cw-plus/0.9.0/cw20/spec/) tokens are supported by the `funds` attribute of `MessageInfo`.

```rs
pub struct MessageInfo {
  pub sender: Addr,
  pub funds: Vec<Coin>,
}
```

When collecting payments in Archway's native token (e.g. `ARCH` for mainnet, `CONST` for Constantine testnet) amounts sent in `funds` should use the chain's minimum denomination (e.g. `uarch` for mainnet, `uconst` for Constantine testnet). Sending payment with other denominations (e.g. `ARCH`, or `CONST`) will fail with an error.
:::

If our `{"increment": {}}` transaction succeeded and we query `count` again, it will have increased by `1`:

```bash
archway query contract-state smart --args '{"get_count": {}}'
```

Now outputs:

```bash
Attempting query...

{"data":{"count":1}}

Ok!
```