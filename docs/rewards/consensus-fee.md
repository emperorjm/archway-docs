---
sidebar_position: 4
---
> **Warning**
>The Rewards module is currently enabled **ONLY** on [Titus](https://docs.archway.io/docs/overview/network/#titus-experimental-testnet)

>We are in the process of releasing an updated version of Constantine, which will include the Rewards module too. In the meantime, if you want to try the Rewards module, make sure to set up your node for the Titus experimental testnet
# Minimum Consensus Fee Overview  

The minimum consensus fee is an important part of transacting on the Archway Network. This guide will explain what the minimum consensus fee is and how to get this information.

## Minimum Consensus Fee Definition 

The minimum consensus fee is the lowest required amount a user pays in transaction fees. Transactions with a lower fee amount than the minimum fee are declined. 

This minimum fee protects the economic model of Archway. It prevents the incentive of sending low/no fee transactions to gain higher dapp rewards. 

This minimum fee is shown in one gas unit, for example, `0.002 uarch`. The client should query the fee before submitting a new transaction. 

## Minimum Fee Calculation 

The minimum consensus fee is calculated for each new block and shown in one gas unit like `0.002` arch. The formula for the calculating the fee is below; 

 `Inflation Block Rewards` /  `Block Gas Limit` * `Transaction Fee Rebate Ratio` - `Block Gas Limit` 

 `Inflation Block Rewards` - Inflationary rewards per block.  <br /> 
 `Block Gas Limit` - Maximum gas limit per block. <br /> 
 `Transaction Fee Rebate Ratio`- Ratio of split fees between Validators and dapps.



  ## Querying the Minimum Consensus Fee 

Use the below query to get the estimated transaction fee for a given transaction. This will return the minimum consensus fee for the current block: 

``bash 
 archwayd query rewards estimate-fees [gas-limit] [flags]
``

### Example Query 
``bash
archwayd q rewards estimate-fees 1 --node 'https://rpc.titus-1.archway.tech:443' --output json | jq -r '.gas_unit_price | (.amount + .denom)'
``