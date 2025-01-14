---
sidebar_position: 3
---

# Rewards Reference 
This guide is a list of the availabe queries and commands related to the rewards system.

## Rewards Queries 

``archwayd query rewards block-rewards-tracking`` - Query rewards tracking data for the current block height

``archwayd query rewards contract-metadata`` - Query contract metadata (contract rewards parameters)

``archwayd query rewards estimate-fees`` - Query transaction fees estimation for a given gas limit

``archwayd query rewards outstanding-rewards``-  Query current credited rewards for a given address (the address set in contract(s) metadata rewards_address field)

``archwayd query rewards params`` - Query module parameters

``archwayd query rewards pool`` - Query the undistributed rewards pool (ready for withdrawal) and the treasury pool funds

``archwayd query rewards rewards-records`` - Query rewards records stored for a given address (the address set in contract(s) metadata rewards_address field) with pagination 

## Transaction Commands 

``archwayd tx rewards set-contract-metadata`` -  Create / modify contract metadata (contract rewards parameters)

``archwayd tx rewards withdraw-rewards`` - Withdraw current credited rewards for the transaction sender
