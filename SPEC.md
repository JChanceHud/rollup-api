# Rollup JSON RPC API

This specification is modeled after the [Ethereum RPC API](https://eth.wiki/json-rpc/API). It is designed to be stateless and transport agnostic.

## Terms

**L1** - Layer 1 network, offers security guarantees like state finality (generally an Ethereum network)

**L2** - Layer 2 network, the chain of blocks containing transactions within the rollup

## Methods

## l2_address

Returns the address of the current L2 contract on the L1 chain.

#### Parameters

None

#### Returns

`DATA` - 20 bytes contract address on L1 chain

## l1_chainId

Returns the chainId of the blockchain containing the L2 contract.

#### Parameters

None

#### Returns

`NUMBER` - integer representing the chain ID

#### Example return

```js
{
  "result": "0x4", // rinkeby
  "jsonrpc": "1.0",
  "id": "6292c5d3-d83e-4de0-b54d-99a109362f88"
}
```

## l2_blockNumber

Returns the highest L2 block number broadcast and confirmed on the L1 chain. Transactions in this block may still be in the challenge period.

#### Parameters

None

#### Returns

`NUMBER` - integer of the current block number

#### Example return

```json
{
  "result": "0x2328",
  "jsonrpc": "1.0",
  "id": "0bbfe282-0630-4561-bf74-0e1e7efee340"
}
```

## l2_getBlockByNumber

Returns block data by number.

#### Parameters

1. `NUMBER|TAG` - integer of a block number, or the string `earliest` or `latest`.
2. `Boolean` - If `true` returns the full transaction objects, if `false` only the hashes

```js
params: [
  "0x1C8", // 456
  false
]
```

#### Returns

See `l2_getBlockByHash`

## l2_getBlockByHash

Returns block data by hash.

#### Parameters

1. `DATA` - bytes hash of block
2. `BOOLEAN` - If `true` returns the full transaction objects, if `false` only the hashes

```js
params: [
  "0xfbcf3e2927bccc1deaa0e96031df0e8ef377278379ff388bac136a87f509b670",
  true
]
```

#### Returns

`Object` - A block object or `null` if no block is found:

- `number`: `NUMBER` - the block number
- `hash`: `DATA` - bytes - hash of the block
- `parentHash`: `DATA` - bytes - hash of the parent block
- `l1BlockHash`: `DATA` - 32 bytes - hash of the L1 network block
- `l1TxHash`: `DATA` - 32 bytes - hash of the L1 network transaction including this L2 block
- `l1Gas`: `NUMBER` - The gas consumed in the L1 transaction
- `l1GasPrice`: `NUMBER` - The gas price in the L1 transaction
- `sender`: `DATA` - 20 bytes - Address of the L1 transaction sender
- `calldata`: `DATA` - bytes - Call data of L1 transaction
- `timestamp`: `NUMBER` - Unix timestamp of block confirmation (seconds)

## l2_getEthBalance

Returns the Ether balance for a target address in the L2 network.

#### Parameters

1. `DATA` - 20 bytes - Address to check
2. `NUMBER|TAG` - block number or the string `earliest` or `latest`

```js
[
  '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
  '0x492'
]
```

#### Returns

`NUMBER` - Balance in wei

## l2_getERC20Balance

Returns the ERC20 token balance for a given address in the L2 network.

#### Parameters

1. `DATA` - 20 bytes - Address to check balance
2. `DATA` - 20 bytes - L1 address of the ERC20 token contract
3. `NUMBER|TAG` - block number or the string `earliest` or `latest`

```js
[
  '0x407d73d8a49eeb85d32cf465507dd71d507100c1',
  '0x6B175474E89094C44Da98b954EedeAC495271d0F', // DAI contract
  'latest'
]
```

#### Returns

`NUMBER` - Token balance with decimals

#### Example

``` js
// Request
{
  "method": "l2_getERC20Balance",
  "params": [
    "0x407d73d8a49eeb85d32cf465507dd71d507100c1",
    "0x6B175474E89094C44Da98b954EedeAC495271d0F",
    "latest"
  ],
  "jsonrpc": "1.0",
  "id": "e91e6a69-b7f2-42ce-9e66-be562bbc5dec"
}

// Response
{
  "result": "0xD02AB486CEDC0000", // 15 DAI
  "id": "e91e6a69-b7f2-42ce-9e66-be562bbc5dec",
  "jsonrpc": "1.0"
}
```

## l2_getTransactionByHash

Returns data for an L2 transaction.

#### Parameters

1. `DATA` - bytes - The transaction hash

#### Returns

`Object` - A transaction object, or `null` if no tx found:
  - `blockHash`: `DATA` - bytes - hash of the L2 block containing this transaction
  - `blockNumber`: `NUMBER` - number of the L2 block containing this transaction
  - `l1TxHash`: `DATA` - 32 bytes - hash of the L1 transaction containing the L2 block containing this transaction
  - `l1BlockHash`: `DATA` - 32 bytes - hash of the L1 block containing the `l1TxHash`
  - `from`: `DATA` - 20 bytes - sender of the transaction (not the coordinator who submitted)
  - `hash`: `DATA` - bytes - hash of the transaction
  - `input`: `DATA` - bytes - data included with the transaction
  - `transactionIndex`: `NUMBER` - number of the transaction index position in the L2 block
  - `challenged`: `BOOLEAN` - true if the transaction validity has been challenged
  - `invalid`: `BOOLEAN` - true if the transaction has been determined to be invalid
  - `challengePeriodEnd`: `NUMBER` - unix timestamp of challenge period end, at which point the transaction is irreversible
  - `applicationData`: `Object` - human readable application specific transaction information encoded as JSON
