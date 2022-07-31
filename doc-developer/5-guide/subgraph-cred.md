---
sidebar_position: 2
---

# How to setup a Subgraph credential through dashboard

## Introductions
Subgraph type credential takes a single wallet address as input and outputs 0(false)/1(true) to indicate whether the wallet address is eligible. It requires 3 fields to be filled in: **Queries(HTTP)**, **Query** and **Expression**.
### Queries(HTTP): 
It's the HTTPs endpoint where Subgraph queries go to.
### Query: 
The GraphQL query, and it requires a single wallet address as input. For more detials, please refer to [Querying The Graph](https://thegraph.com/docs/en/querying/querying-the-graph/). In dashboard, once you finish your query, fill in a test address and click 'Run' button to check if the query's return is good.
### Expression: 
A JavaScript (ES6) function with this type signature: `(object) => int`. The function should return either number 1 or 0, representing if the address is eligible for this subgraph credential.. Once you have the 'Query' output, click 'Run' button to check if it processes correctly. Behind the scenes, first, we send the query with the user's address to the GraphQL endpoint, and then we will apply the function against the 'data' field of the response. If the returned value is 1, then user can own this credential, otherwise, not. Once the query's output is good, click 'Run' button to check if expression processes query's output correctly.

## Subgraph Examples

### Endpoint
```
https://api.thegraph.com/subgraphs/name/hyd628/nomad-and-connext
```
### Query
```graphql
query info($address: String!) {
  receiveds(
    where: {
      recipient: $address
      block_gt: 1400000
      token_in: [
        "0x8f552a71EFE5eeFc207Bf75485b356A0b3f01eC9"
        "0x1DC78Acda13a8BC4408B207c9E48CDBc096D95e0"
        "0x30D2a9F5FDf90ACe8c17952cbb4eE48a55D916A7"
      ]
    }
  ) {
    id
  }
  fulfilleds(where: { user: $address, timestamp_gt: 1651986604 }) {
    id
    timestamp
  }
}

```
### Query output
```json
{
  "receiveds": [
    {
      "id": "0x000abcdefg..."
    }
  ],
  "fullfilleds": []
}
```
### Expression
```javascript
function(resp){
  if (resp != null && (resp.fulfilleds != null && resp.fulfilleds.length > 0 || resp.receiveds != null && resp.receiveds.length > 0)) {
     return 1
  }
  return 0
}
```
### Expression output
```
1
```