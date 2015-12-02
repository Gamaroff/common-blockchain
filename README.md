# common-blockchain

[![Version](http://img.shields.io/npm/v/common-blockchain.svg)](https://www.npmjs.org/package/common-blockchain)

A standard REST API specification for comprehensive bitcoin block explorers.

**NOTE**: For the mocha testing kit that previously resided in this module, please see [cb-tester](https://github.com/dcousens/cb-tester).

### Compliant npm modules

* [`cb-http-client`](https://github.com/common-blockchain/cb-http-client) - `v0.2.1`


## API Specification

Any bitcoin value is to be represented as a [satoshi](https://bitcoin.stackexchange.com/questions/114/what-is-a-satoshi), that is, the smallest fraction of a Bitcoin that can currently be sent: 0.00000001 BTC.
To convert between BTC and satoshis, see the [proper money handling page on the Bitcoin wiki](https://en.bitcoin.it/wiki/Proper_Money_Handling_(JSON-RPC))


### Addresses

#### Addresses.Summary

**Parameters**: Array of addresses

Returns an array of address summaries.
Each summary must not disclude unconfirmed transactions from its information.

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

Returns a array of transactions related to this Address (as an input or output).  Optionally filtered by a minimum block hash/height.

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


### Transactions

#### Transactions.Get

**Parameters:** Array of transaction ids (big-endian transaction hashes)

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


#### Transactions.Latest

Returns the latest unconfirmed transactions (subjective to the node)

``` javascript
[
  {
    txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
    txHex: "01000000011c1020c1114820e7c44e12e804aec5f4af1e8a6aad3c446c4cfc8aa53e61f73d010000008a47304402200fea124cecd36e92cb0b549b62740a26f374629b26f16292a3e858753035172802205ba172966addddbbe8181af6cd7fb6e9c53414fb6727c4f15589c74567e48ab30141040cfa3dfb357bdff37c8748c7771e173453da5d7caa32972ab2f5c888fff5bbaeb5fc812b473bf808206930fade81ef4e373e60039886b51022ce68902d96ef70ffffffff02204e0000000000001976a91461120f6e004c7a2e20ecdedf461f1eb032c2e5c388acabfb423d000000001976a91461b469ada61f37c620010912a9d5d56646015f1688ac00000000"
  },
  ...
]
```


#### Transactions.Propagate

**Parameters:** Array of transaction hex strings in the bitcoin protocol format.

Returns the Array of propagated transaction ids (big-endian transaction hashes)

``` javascript
[
  "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
  ...
]
```


#### Transactions.Summary

**Parameters:** Array of transaction ids (big-endian transaction hashes)

``` javascript
[
  {
    txId: "c7736a0a0046d5a8cc61c8c3c2821d4d7517f5de2bc66a966011aaa79965ffba",
    blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
    blockHeight: 318573,
    nInputs: 2,
    nOutputs: 7,
    totalInputValue: 130000,
    totalOutputValue: 100000
  },
  ...
]
```


### Blocks

#### Blocks.Get

**Parameters:** Array of block ids and/or block heights

``` javascript
[
  {
    "blockId": "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
    "blockHex": "020000003bef0d0b88737698572aa5c78 ... much hex"
  }
  ...
]
```


#### Blocks.Latest

Returns the best-chain latest block summary (subjective to the node)

``` javascript
{
  blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
  merkleRootHash: "82203172bab3f9b90543e98eef4acdf5ab9daf3b6df80806092f9715fe72ba63",
  prevBlockId: "000000000025896a3409c57493aa7020a4cc24232a8ee9da10cbe857953d27bd",
  nonce: 4210027488,
  blockHeight: 318573,
  blockSize: 456643328,
  timestamp: 1382046624,
  txCount: 3
}
```


#### Blocks.Propagate

**Parameters:** Block hex

``` javascript
No response body
```


#### Blocks.Summary

**Parameters:** Array of block ids and/or block heights

``` javascript
[
  {
    blockId: "00000000000000001b701ecd0cf2b7a7742a320e9a06a506227ee345b5735d13",
    prevBlockId: "000000000025896a3409c57493aa7020a4cc24232a8ee9da10cbe857953d27bd",
    merkleRootHash: "82203172bab3f9b90543e98eef4acdf5ab9daf3b6df80806092f9715fe72ba63",
    nonce: 4210027488,
    version: 2,
    blockHeight: 318573,
    blockSize: 456643328,
    timestamp: 1382046624,
    txCount: 3
  }
]
```
