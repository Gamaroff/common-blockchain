# common-blockchain

[![Version](http://img.shields.io/npm/v/common-blockchain.svg)](https://www.npmjs.org/package/common-blockchain)

A standard REST API specification for comprehensive bitcoin block explorers.

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


#### Addresses.Transactions

**Parameters**: Array of addresses, (optional) minimum block hash/height

Returns a array of transactions related to this Address (as an input or output).
Optionally filtered by a minimum block hash/height.

Block information is subjective to the node.

``` javascript
[
	{
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
		txHex: "01000000011c1020c1114820e7c44e12e804aec5f4af1e8a6aad3c446c4cfc8aa53e61f73d010000008a47304402200fea124cecd36e92cb0b549b62740a26f374629b26f16292a3e858753035172802205ba172966addddbbe8181af6cd7fb6e9c53414fb6727c4f15589c74567e48ab30141040cfa3dfb357bdff37c8748c7771e173453da5d7caa32972ab2f5c888fff5bbaeb5fc812b473bf808206930fade81ef4e373e60039886b51022ce68902d96ef70ffffffff02204e0000000000001976a91461120f6e004c7a2e20ecdedf461f1eb032c2e5c388acabfb423d000000001976a91461b469ada61f37c620010912a9d5d56646015f1688ac00000000",
		blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
		blockHeight: 318573
	},
	...
]
```


#### Addresses.Unspents

**Parameters:** Array of addresses

Returns a array of unspents transaction outputs, combined with the vout, value and related address.
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


### Blocks

`blockHex` is hex string for the binary representation of the block headers.  Transactions can be retrieved via the `Blocks.Transactions` end point. 

#### Blocks.Get

**Parameters:** Array of block ids

Idempotent.

``` javascript
[
	{
		"blockHex": "020000003bef0d0b88737698572aa5c78 ...",
		"blockId": "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13"
	}
	...
]
```


#### Blocks.Latest

Returns the tip of the nodes best-chain

Subjective to the node.

``` javascript
{
	"blockHex": "020000003bef0d0b88737698572aa5c78 ...",
	"blockId": "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13"
}
```


#### Blocks.Propagate

**Parameters:** Block hex

``` javascript
No response body
```


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


### Transactions

#### Transactions.Get

**Parameters:** Array of transaction ids (big-endian transaction hashes)

Idempotent.

``` javascript
[
	{
		txHex: "01000000011c1020c1114820e7c44e12e804aec5f4af1e8a6aad3c446c4cfc8aa53e61f73d010000008a47304402200fea124cecd36e92cb0b549b62740a26f374629b26f16292a3e858753035172802205ba172966addddbbe8181af6cd7fb6e9c53414fb6727c4f15589c74567e48ab30141040cfa3dfb357bdff37c8748c7771e173453da5d7caa32972ab2f5c888fff5bbaeb5fc812b473bf808206930fade81ef4e373e60039886b51022ce68902d96ef70ffffffff02204e0000000000001976a91461120f6e004c7a2e20ecdedf461f1eb032c2e5c388acabfb423d000000001976a91461b469ada61f37c620010912a9d5d56646015f1688ac00000000",
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba"
	},
	...
]
```


#### Transactions.Latest

Returns the latest unconfirmed transactions

Subjective to the node.

``` javascript
[
	{
		txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
		txHex: "01000000011c1020c1114820e7c44e12e804aec5f4af1e8a6aad3c446c4cfc8aa53e61f73d010000008a47304402200fea124cecd36e92cb0b549b62740a26f374629b26f16292a3e858753035172802205ba172966addddbbe8181af6cd7fb6e9c53414fb6727c4f15589c74567e48ab30141040cfa3dfb357bdff37c8748c7771e173453da5d7caa32972ab2f5c888fff5bbaeb5fc812b473bf808206930fade81ef4e373e60039886b51022ce68902d96ef70ffffffff02204e0000000000001976a91461120f6e004c7a2e20ecdedf461f1eb032c2e5c388acabfb423d000000001976a91461b469ada61f37c620010912a9d5d56646015f1688ac00000000"
	},
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


#### Transactions.Propagate

**Parameters:** Transaction hex

``` javascript
No response body
```


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
