---
sidebar_position: 3
title: arch3.js
---

# arch3.js 

[`arch3.js`](https://www.npmjs.com/package/@archwayhq/arch3-core) is a JavaScript library that enables the interaction between front-end web applications and the Archway Protocol. It is a replacement for using the [CosmJS](https://github.com/cosmos/cosmjs) client. 


## Installation 
### NPM  
```bash 
npm install --save @archwayhq/arch3.js
```

### Yarn 
```bash 
yarn add @archwayhq/arch3.js
```

## Sample Usage 

### Querying 
Querying the blockchain is a way to understand the events and statuses that happen inside a smart contract. Below is an example using the increment contract template to Constantine. 

```js 
import { ArchwayClient } from '@archwayhq/arch3.js';

const client = await ArchwayClient.connect('https://rpc.constantine-1.archway.tech');

const contractAddress = 'archway1dd8xfz4msg7aufxcc4sjjzusg4rsp98kza2z7zjprhgwvmpdzncq6jrg99';
const msg = {
  get_count: {},
};
const { count } = await client.queryContractSmart(
  contractAddress,
  msg
);
```

### Executing a transaction 
Executing a transaction includes actions such as transferring NFTs between addresses or interacting with a smart contract. In the below code example, we are sending a [`msg` object](https://docs.cosmos.network/main/building-modules/messages-and-queries#messages) to execute the [increment function](https://docs.cosmwasm.com/dev-academy/develop-smart-contract/intro/#increment) inside the smart contract. 

```js 
import { SigningArchwayClient } from '@archwayhq/arch3.js';
import { DirectSecp256k1HdWallet } from '@cosmjs/proto-signing';

const network = {
  chainId: 'constantine-1',
  endpoint: 'https://rpc.constantine-1.archway.tech',
  prefix: 'archway',
};

const alice = {
  // This is an incomplete mnemonic used for demo purposes only. Please, never hard code your seed phrases.
  mnemonic: 'culture blossom ten thing bar ...',
  address0: 'archway1cw3vd33zxyy5jk38azn3l8ytw53dwh8h73jugf',
};

const wallet = await DirectSecp256k1HdWallet.fromMnemonic(alice.mnemonic, { prefix: network.prefix });
const client = await SigningArchwayClient.connectWithSigner(network.endpoint, wallet, {
  ...defaultSigningClientOptions,
  prefix: network.prefix,
});

const contractAddress = 'archway14v952t75xgnufzlrft52ekltt8nsu9gxqh4xz55qfm6wqslc0spqspc5lm';
const msg = {
  increment: {},
};
const { transactionHash } = await client.execute(
  alice.address0,
  contractAddress,
  msg,
  'auto'
);

```

