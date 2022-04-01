# Ethereum Yellow Paper

Transaction based state-machine

state of the blockchain changes after executing transactions.

stores states in ***state root***

## Accounts

20-byte hex addresses whose state is stored on the blockchain

Two types of accounts:

1. EOA: externally owned account
2. Contract Accounts

## Each accounts has four parts:

1. Nonce: for EOA, number of transactions sent from the address. For Contracts, number of contracts creations.
2. Balance: amount of ether owned by the account.
3. storageRoot: Merkle-Patricia tree that stores data related to the account. Stored in the top-level state root tree.
4. codeHash: For EOA, hash of empty string. For contracts, hash of the init code.

## Transactions

Piece of data signed by an external actor.

Two types of txs:

1. Transaction which result in message calls
Message Calls: Done by contract account, when executing `CALL` opcode.
2. Contract creation

Fields in a tx:

1. nonce
2. gasLimit
3. gasPrice
4. to
5. value
6. v, r, s: signature identifying sender
7. init: in case of contract creating tx, returns code of the contract without constructor
8. data: in case of message call tx, data being passed in call

## Blocks

aggregate transactions and include in the blockchain

Contains:

1. parentHash
2. ommerHash
3. beneficiary
4. stateRoot
5. transactionsRoot
6. receiptsRoot
7. timestamp
8. number
9. difficulty
10. gasLimit
11. gasUsed
12. extraData
13. mixHash
14. nonce

## GHOST

`GHOST` protocol is used by ethereum to prevent mining centralization and enhance protocol security.

Longest chain isn’t just the chain with more blocks as ancestor, but it also includes other stale(uncle) blocks in the calculation.

Uncle blocks are child of the ancestor of the block and not directly related to the block.

## RLP

It is the data encoding used by the protocol to store data in tries.
There are several rules for encoding mentioned below.

[Data structure in Ethereum | Episode 1: Recursive Length Prefix (RLP) Encoding/Decoding.](https://medium.com/coinmonks/data-structure-in-ethereum-episode-1-recursive-length-prefix-rlp-encoding-decoding-d1016832f919)

## HP Encoding

This encoding is used for trie paths.

[Data structure in Ethereum | Episode 1+: Compact (Hex-prefix) encoding.](https://medium.com/coinmonks/data-structure-in-ethereum-episode-1-compact-hex-prefix-encoding-12558ae02791)

[Ethereum: Tutorials - LayerX Research](https://scrapbox.io/layerx/Ethereum:_Tutorials)

Patricia tree

[patricia-tree](https://eth.wiki/fundamentals/patricia-tree)

