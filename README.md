# common-blockchain

[![Version](http://img.shields.io/npm/v/common-blockchain.svg)](https://www.npmjs.org/package/common-blockchain)

A standard API specification for comprehensive bitcoin block explorers.

**NOTE**: For the mocha testing kit that previously resided in this module, please see [cb-tester](https://github.com/dcousens/cb-tester).


## API Specification

Any bitcoin value is to be represented as a [satoshi](https://bitcoin.stackexchange.com/questions/114/what-is-a-satoshi), that is, the smallest fraction of a Bitcoin that can currently be sent: 0.00000001 BTC.
To convert between BTC and satoshis, see the [proper money handling page on the Bitcoin wiki](https://en.bitcoin.it/wiki/Proper_Money_Handling_(JSON-RPC))


### Addresses

#### Addresses.Summary

**Parameters**: Array of addresses

Warning: includes unconfirmed transactions in summary.

Subjective to the node.

``` javascript
[
	{
		address: "mpNDUWcDcZw1Teo3LFHvr8usNdwDLKdTaY",
		balance: 100000,
		received: 430000,
		txCount: 3
	},
	...
]
```

If any address is badly formatted or of the wrong network, an `Error` must be returned with the message: `{{address}} is not a valid {{network}} address`


#### Addresses.Transactions

**Parameters**: Array of addresses, (optional) minimum block id

Returns an array of transactions related to this Address (as an input or output), optionally filtered with a minimum [by height] block id.

Warning: includes unconfirmed transactions.

Subjective to the node.

``` javascript
[
	"c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
]
```

If any address is badly formatted or of the wrong network, an `Error` must be returned with the message: `{{address}} is not a valid {{network}} address`.
If the minimum block id is badly formatted, an `Error` must be returned with the message: `{{blockId}} is not a valid 256-bit hash`.
If the minimum block id is unknown, an `Error` must be returned with the message: `Unknown blockId: {{blockId}}`.


#### Addresses.Unspents

**Parameters:** Array of addresses

Warning: includes unconfirmed, unspent transaction outputs.

Subjective to the node.

``` javascript
[
	{
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
		confirmations: 6,
		address: "mpNDUWcDcZw1Teo3LFHvr8usNdwDLKdTaY",
		value: 430000,
		vout: 0
	},
	...
]
```

If any address is badly formatted or of the wrong network, an `Error` must be returned with the message: `{{address}} is not a valid {{network}} address`.


### Blocks

#### Blocks.Get

**Parameters:** Array of block ids

Returns a block id mapped result with `header` and `height` fields.
Block transactions are not included in the response; they can be retrieved independently through the `Blocks.Transactions` end point.

Idempotent.

``` javascript
[
	"00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13": {
		"header": "0400000042cb79fa6b45627ccb8d3fe2e4fde26ed58385230cc7d1020000000000000000330ff5cf54194e776241ee347c0a60bc23b8ae566b131a2b437ef2cb19cb3d0aa34f98561bb30918d68de3ae",
		"height": 314246
	}
	...
]
```

If any block id is badly formatted, an `Error` must be returned with the message: `{{blockId}} is not a valid 256-bit hash`.
If any block id is unknown, an `Error` must be returned with the message: `Unknown blockId: {{blockId}}`.


#### Blocks.Latest

Returns the tips block id for the local best-chain.

Subjective to the node.

``` javascript
"00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13"
```


#### Blocks.Propagate

**Parameters:** Block hex

``` javascript
No response body
```

If the block hex is badly formatted, an `Error` must be returned with the message: `Invalid blockHex`.
If the block hex is rejected, an `Error` must be returned with the message: `Block rejected: {{reason}}`.


#### Blocks.Transactions

**Parameters:** Array of block ids (reverse-byte-order block hashes)

Idempotent.

``` javascript
[
	"00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13": [
		"c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	],
	...
]
```

If any block id is badly formatted, an `Error` must be returned with the message: `{{blockId}} is not a valid 256-bit hash`.
If any block id is unknown, an `Error` must be returned with the message: `Unknown blockId: {{blockId}}`.


### Transactions

#### Transactions.Get

**Parameters:** Array of transaction ids (reverse-byte-order transaction hashes)

Idempotent.

``` javascript
[
	"c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba": "01000000011c1020c1114820e7c44e12e804aec5f4af1e8a6aad3c446c4cfc8aa53e61f73d010000008 ...",
	...
]
```

If any transaction id is badly formatted, an `Error` must be returned with the message: `{{txId}} is not a valid 256-bit hash`
If any transaction id is unknown, an `Error` must be returned with the message: `Unknown txId: {{txId}}`


#### Transactions.Latest

Returns a list of the latest unconfirmed transactions ids, equivalent to the local mempool.

Subjective to the node.

``` javascript
[
	"c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	...
]
```


#### Transactions.Outputs

**Parameters:** Array of `{ txId, vout }`

Idempotent.

``` javascript
[
	{
		scriptPubKey: "61b469ada61f37c620010912a9d5d56646015f16",
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
		value: 10505,
		vout: 0
	},
	...
]
```

If any transaction id is badly formatted, an `Error` must be returned with the message: `{{txId}} is not a valid 256-bit hash`.
If any transaction id is unknown, an `Error` must be returned with the message: `Unknown txId: {{txId}}`.
If a transaction vout does not exist, an `Error` must be returned with the message: `{{txId}} does not have a vout of {{vout}}`.


#### Transactions.Propagate

**Parameters:** Transaction hex

``` javascript
No response body
```

If the txHex is badly formatted, an `Error` must be returned with the message: `Invalid txHex`
If the transaction hex is rejected, an `Error` must be returned with the message: `Transaction rejected: {{reason}}`


#### Transactions.Status

**Parameters:** Array of transaction ids (reverse-byte-order transaction hashes)

Returns a `txId` mapped result to respective `blockId`s, if known.
Transactions with no respective `blockId` **must** be omitted.

Subjective to the node.
Respects local best-chain.

``` javascript
[
	"c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba": "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
	...
]
```

If any transaction id is badly formatted, an `Error` must be returned with the message: `{{txId}} is not a valid 256-bit hash`.
If any transaction id is unknown, an `Error` must be returned with the message: `Unknown txId: {{txId}}`.
