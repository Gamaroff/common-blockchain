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

Returns an array of address summaries.
Summaries must not exclude statistics from unconfirmed transactions.

Subjective to the node.

``` javascript
[
	{
		address: "mpNDUWcDcZw1Teo3LFHvr8usNdwDLKdTaY",
		balance: 100000,
		totalReceived: 430000,
		txCount: 3
	},
	...
]
```

If the any address is badly formatted or the wrong network, an `Error` must be returned with the message: `<address> is not a valid <network> address`


#### Addresses.Transactions

**Parameters**: Array of addresses, (optional) minimum block id

Returns an array of transactions related to this Address (as an input or output).
Optionally filtered [by height] with a minimum block id.

Subjective to the node.

``` javascript
[
	{
		blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	},
	...
]
```

If the minimum block id is unknown, an `Error` must be returned with the message: `Unknown blockId: <blockId>, it may have been orphaned`
If the any address is badly formatted or the wrong network, an `Error` must be returned with the message: `<address> is not a valid bitcoin address`


#### Addresses.Unspents

**Parameters:** Array of addresses

Returns an array of unspents transaction outputs, combined with the vout, value and related address.
Unspents will include unconfirmed, unspent transactions outputs.

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

If the any address is badly formatted or the wrong network, an `Error` must be returned with the message: `<address> is not a valid bitcoin address`


### Blocks

#### Blocks.Get

**Parameters:** Array of block ids

Returns an array of blocks including the hex representation of their headers.
Block transactions are not included in the response; they can be retrieved independently through the `Blocks.Transactions` end point.

Idempotent.

``` javascript
[
	{
		"blockHeight": 314246,
		"blockHex": "020000003bef0d0b88737698572aa5c78 ...",
		"blockId": "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13"
	}
	...
]
```

If any block id is unknown, an `Error` must be returned with the message: `Unknown blockId: <blockId>`


#### Blocks.Latest

Returns the tip of the nodes best-chain

Subjective to the node.

``` javascript
"00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13"
```


#### Blocks.Propagate

**Parameters:** Block hex

``` javascript
No response body
```

If the block hex is badly formatted, an `Error` must be returned with the message: `Invalid blockHex`
If the block hex is rejected, an `Error` must be returned with the message: `Block rejected: <reason, if known>`


#### Blocks.Transactions

**Parameters:** Array of block ids (big-endian block hashes)

Idempotent.

``` javascript
[
	{
		blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	},
	...
]
```

If any block id is unknown, an `Error` must be returned with the message: `Unknown blockId: <blockId>`


### Transactions

#### Transactions.Get

**Parameters:** Array of transaction ids (big-endian transaction hashes)

Idempotent.

``` javascript
[
	{
		txHex: "01000000011c1020c1114820e7c44e12e804aec5f4af1e8a6aad3c446c4cfc8aa53e61f73d010000008 ...",
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	},
	...
]
```

If any transaction id is unknown, an `Error` must be returned with the message: `Unknown txId: <txId>`


#### Transactions.Latest

Returns a list of the latest unconfirmed transactions ids, equivalent to the nodes mempool.

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

If any transaction output is unknown, an `Error` must be returned with the message: `Unknown output: <txId>:<vout>`


#### Transactions.Propagate

**Parameters:** Transaction hex

``` javascript
No response body
```

If the transaction hex is rejected, an `Error` must be returned with the message: `Transaction rejected: <reason, if known>`


#### Transactions.Status

**Parameters:** Array of transaction ids (big-endian transaction hashes)

Subjective to the node.

``` javascript
[
	{
		blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	},
	{
		blockId: null,
		txId: "a0ff943d3f644d8832b1fa74be4d0ad2577615dc28a7ef74ff8c271b603a082a"
	},
	...
]
```

If any transaction id is unknown, an `Error` must be returned with the message: `Unknown txId: <txId>`
