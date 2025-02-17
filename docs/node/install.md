---
sidebar_position: 1
---

# Node Installation

This guide explains how to install and run an Archway full node. 

## Installing Prerequisites 
Below are the packages that your machine needs to complete the installation process. 

```bash 
# updates and upgrades the list of local packages 
sudo apt-get update && sudo apt upgrade -y

#installs docker, build-essential package and git 
sudo apt-get install build-essential docker-ce docker-ce-cli containerd.io docker-compose-plugin git

``` 

## Installing Go
Go Version 1.16 or higher is required to run an Archway node. Please find a [guide here](https://golang.org/doc/install) on how to install Go. 

Ensure the Go environment variables are [set properly](https://golang.org/doc/gopath_code#GOPATH) on your system.

## Build Archway Daemon from Source
The next step is to get the source code from the [Archway Repository](https://github.com/archway-network/archway). 

```bash

git clone https://github.com/archway-network/archway.git

cd archway

git fetch

git checkout <version-tag>

```
:::info
For connecting to the [Constantine Developer Testnet](https://docs.archway.io/docs/overview/network#constantine-dapp-developer-testnet), the version tag v0.0.5 should be used:

`git checkout v0.0.5` 
:::



Build and install that Archway Daemon  

```bash
make install
```

Confirm that the installation has been completed by running the following command: 

```bash 
archway --version

#1.2.3
``` 


## Run `archwayd` using Docker

Make sure you have [Docker](https://docs.docker.com/get-docker "Install Docker") installed on your machine. For Linux users, it's recommended to run the Docker daemon in [Rootless Mode](https://docs.docker.com/engine/security/rootless/ "Docker Rootless mode").

Pull the image from [Docker Hub](https://hub.docker.com/r/archwaynetwork/archwayd):

```bash
docker pull archwaynetwork/archwayd:latest
```

Each Archway network uses a different version of Archway. To connect your node to a network, you should use a tag with the corresponding network name. For example, to connect to the Constantine testnet:

```bash
docker pull archwaynetwork/archwayd:constantine
```

:::info
Make sure to always use the image tag that points to the network you want to connect.
:::

By default, the Docker image runs the `archwayd` binary, so you should specify the arguments for `archwayd` right after the image name. For better usage, mount an external volume at `/root/.archway` to persist the daemon home path across different runs. For example, if you want to add a key:

```bash
docker run --rm -it \
  -v ~/.archway:/root/.archway \
  archwaynetwork/archwayd:latest \
  keys add test-key
```

And then list all keys:

```bash
docker run --rm -it \
  -v ~/.archway:/root/.archway \
  archwaynetwork/archwayd:latest \
  keys list
```

It's also important to notice that, when running a node in a network, you'll need to expose the container ports for external connectivity. The image exposes the following ports:

- `1317`: Rest server
- `26656`: Tendermint P2P
- `26657`: Tendermint RPC

:::tip
To simplify using the Docker container, set an alias with the home path and the proper image tag (replacing `<network-name>`), like:

```bash
alias archwayd="docker run --rm -it -v ~/.archway:/root/.archway archwaynetwork/archwayd:<network-name>"
```

After setting this alias, you can use the other `archwayd` commands without typing the verbose Docker run command.
:::

## Initialize the Node

Initialize the `genesis.json` file that is required to establish a network.

```bash
archwayd init my-node --chain-id my-chain
```

If you recieve a `archwayd: command not found` Error message, run the follwoing command: 

```bash 
sudo cp /$HOME/go/bin/archwayd /usr/local/bin
```

If the command is run successfully, you will see the information about your chain starting with a similar message: 

```bash 
{"app_message":{"auth":{"accounts":[],"params":{"max_memo_characters":"256","sig_verify_cost_ed25519":"590","sig_verify_cost_secp256k1":"1000","tx_sig_limit":"7","tx_size_cost_per_byte":"10"}}....

```

## Prepare the Account and Keys

Create a key to hold your account. After you run this command, you are prompted with a password dialogue. Enter a new password for your account.

```bash
archwayd keys add my-account
```

You see an output similar to the following:

```text
- name: my-account
  type: local
  address: archway12ntzpk9fjt2x39pvll8ufma9tuhhnkh8g4zzc2
  pubkey: archwaypub1addwnpepqfgjegqxxv9srfe359t93tu9l86tpkwwjk7w63xtpwq05wmlq9emjmxfmmv
  mnemonic: ""
  threshold: 0
  pubkeys: []


**Important:** Write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

resource regret any wet stable body alcohol spring horse valve ritual top music salad gesture can earn casino example drive surface mix senior flag
```

Here you can see your account details and the mnemonic phrase that is very crucial to recover the account.

## Starting the Node

Starting a node now will give you an error message like the one below. This is because at least one validator node must be connected for the network to run. 

```bash
archwayd start

Error: error during handshake: error on replay: validator set is nil in genesis and still empty after InitChain
```

## Next Steps 
To get your node fully running, you can [run a local testnet](https://docs.archway.io/docs/node/running-a-local-testnet) with two nodes.

If you would like to connect to a network, see the [Validator Overview](../validator/overview.md) on how to run a validator node. 
