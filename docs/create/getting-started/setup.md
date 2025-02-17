---
sidebar_position: 2
---

# Project Setup

A new Archway project is a Rust project that will compile to `wasm` using the `archwayd` binary, which is an implementation of a cosmos zone with `wasm` smart contracts enabled.

Let's setup a new project, using the [Archway developer CLI](https://github.com/archway-network/archway-cli)

## Creating an account

Use the command `archway accounts` to view wallets in your key ring, or create a new wallet using the flag `--add <account label>`

```bash
archway accounts --add "mywallet"
```

Example output:
```bash
Enter keyring passphrase:

- name: mywallet
  type: local
  address: archway1gtmpktrrnu2qwnweyulwptv6wukd3edpxugkzn
  pubkey: archwaypub1addwnpepqdhls9u834myuuajgstf8esyqttn3ph6r5y6kr5nnfx5uc06al9hut8r9ac
  mnemonic: ""
  threshold: 0
  pubkeys: []


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

# Mnemonic seed words here...
```

## Creating a project

The `archway new` command starts a new project. You can press `enter` to accept the default options for any of the project configuration questions.

```bash
archway new
```

Example Output: 
```bash
Creating new Archway dapp...

Configure environment (Y/N default: N)?: Y

1. Testnet
2. Localhost
3. Mainnet

Select environment type (1-3 default: 1): 1

1. Constantine [stable]
2. Titus [nightly]

Select a testnet to use (1-2 default: 1): 1
Use Docker to run "archwayd" daemon (Y/N default: N)?: Y
Use starter template? (Y/N default: N): Y

1. Increment [https://github.com/drewstaylor/archway-template/blob/main/README.md]

Select starter template (1-1): 1

Name of project (e.g. "My Project"): First Archway Project
💡   Using private key: `$HOME/.ssh/id_rsa` for git-ssh checkout
🔧   Generating template ...

# More GitHub Feedback here...

🔧   Moving generated files into: `/home/first-archway-project`...
✨   Done! New project created /home/first-archway-project

Successfully created new Increment project in path /home/first-archway-project with network configuration constantine-1.
Config file location: /home/first-archway-project/config.json
```

## Configuring a project

When you have created a new project, navigate to the folder where the project was installed and print your config using the command `archway configure`. 

If this configuration isn't to your liking, you can always modify it by editing the `config.json` file in the root folder of the project. 

```bash
cd my-project
archway configure
```

Outputs:
```bash
Printing environment settings...
{
  title: 'My Project',
  version: '0.0.1',
  network: {...},
  developer: {...},
  path: '/home/my-system-path/my-project',
  type: 'Increment'
} 
```

The `type` parameter simply keeps track of which "starter template" you used during project creation.

In `network` you'll find settings for the network you're working on. Supported network types are `mainnet`, `testnet` and `localhost`. Additionally, there are 2 testnet choices: `stable [constantine]` and `nightly [titus]`.

`developer` contains a history of your deployments and holds scripts for building, testing, optimizing and deploying your project. In `developer.scripts` you'll have granular control over how your project is compiled, executed and deployed, but it already "just works" without any tinkering.

## Network settings and migrating between networks

During a project's life cycle you may want to migrate or change your network settings. For example, switching from `testnet` to a `mainnet` deployment.

Migrate between networks using the command `archway network`. This will print your current settings and give the option to change networks.

```bash
archway network
```

Example output:
```bash
Printing network settings...
1. Testnet* [constantine, titus]
2. Localhost
3. Mainnet [triomphe]
Migrate to another network (Y/N default: N)?: n
Ok!
```
:::note
If you migrate between networks by answering `y` or `yes` and following the additional migration questions, your previous deployments history and script customisations will remain in tact.
:::