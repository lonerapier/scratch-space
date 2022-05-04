# [Nomad](https://nomad.xyz)

Protocol for cross-chain communication. Follows **optimistic** design inspired from _Optimistic Rollups_. Nomad's design aims to solve the interoperability trilemma famously coined by Connext but introduces a new component, `latency`.

## Design

Architectural design is divided into two main components:

- On-chain components
  - Home
  - Replica
- Off-chain components
  - Updater
  - Relayer
  - Watcher
  - Processor

## Optimistic Flow

Nomad works with both of these components to facilitate communication between any number of chains that support user-defined computations. It takes use of _sparse merkle trees_ as data structure to pass message between the chains.

There is a source domain with `Home` contract and a destination ~~chain~~ domain with the `Replica` contract. `Home` is reponsible for generating the message on the source domain and `Replica` on the destination domain keeps a copy of the tree which gets updated when new messages are received from the source domain.

> Note: We'll use `domain` here instead of `chain` as Nomad is a base layer that can be used with any chains, rollups, etc.

How these messages gets passed securely is handled by the off-chain components which helps in ease of implementation and reducing the cost of these transfers by 80%. The `updater` component polls messages from the `Home` contract in the form of a merkle tree and attests it. It is then sent to the destination chain by the `Relayer`. There, it waits for some time before being added in the Replica contract which holds the current root. Since, `updater` can attest fraudulent messages, the timeout gives anyone the chance to prove the fraud which results in bond slashing of updater and all the attestations being marked as invalid.

Since the messages are just a merkle tree being committed to newer messages, it can be easily rolled back. The process of timeout and `Watchers` submitting a fraud proof is done on the receiving chain because a `Relayer` doesn't know about messages at all, it's job is just to relay whatever message it receives from source chain. These fraud can be proven back to the source chain as that is the original `Source of Truth`, which slashes the `Updater` bonds.

Nomad rather than handling globally verified fraud proofs on the sending chain, allows fraudulent messages to pass through as then these frauds become public due to the timeout at the receiving chains. These can then easily be proven to the source chain which becomes the source of truth that these fraudulent messages where in fact, passed by the `Updater`.

## Components

### On-Chain Components

#### `Home`

- Used by other contracts to send messages using `send message` API
- enfore message format
- updates root by committing messages
- maintains queue of tree roots
- slashes `Updater` bonds
  - Double update proofs
  - Improper update proofs

#### `Replica`

- maintains queue of pending updates
- add to new tree root after timeout elapses
- accepts fraud proofs to validate messages
- ensure processing of messages in proper order
- sends messages to end recipients

### Off-Chain Components

#### `Updater`

- polls home for new updates
- signs or attests to new updates
- publish to home chain

#### `Relayer`

relays updates to new

- Observes home to check for new updates
- forwards the signed updates to one or more replicas
- Observes replicas for timeout passed updates and updates relayer current root

#### `Watcher`

Provides security to the protocol

- Submits Double/Invalid update proof
- Observes home, to check the interaction of updater's with the Home contract to check for any malicious attestations
- Observes replicas, so that updater doesn't directly go to replica

#### `Processor`

processes pending updates

- maintains the old tree with all the details
- creates proof for new updates
- send proven messages to end recipients

## Frauds

Currently, there are two types of fraud that can occur in the system.

1. ### Improper Update

Occurs when an `Updater` attests to a fake root that was not in the `Home` root's queue. Updater purpose is to send fake messages to the destination chain.

This can be easily proven by the `Watcher` by submitting the fake root and Updater sig to Home, eventually slashing Updater's bonds or at worst halting the transfers to destination chain.

2. ### Double Update

An `Updater` can attest two identical roots, i.e. the roots share same sig in order to double spend the receiving chain. This gets detected by `Watcher` as the duplicate root submitted by Updater is not the part of the queue as it's already sent to the destination chain.

## Token Bridge xApp Example

**Source**: Chain A

**Destination**: Chain B

**Local Contract**: Contract on same chain

**Remote Contract**: Contract on other chain

- **Source**

  - User approve tokens to local `BridgeRouter`
    - If native token, held in escrow and sent to local `Home`
    - If non-native, token burned as the contract is deployed by `BridgeRouter`
  - Message constructed by local `BridgeRouter` to send tokens to `Destination` on `Remote BridgeRouter`
    - It keeps mapping of bridge routers on other chains, to send message to desired receiver.
  - enqueues message on `Home`

- Off-Chain

  - Updater attests to the root
  - relayer forwards it to `Remote Replica`
  - processor creates proof

- **Destination**
  - `Replica` processes message after timeout ends and sends message to `BridgeRouter`
  - `BridgeRouter` verifies that it was sent by `Source BridgeRouter` as it keeps mapping of `BridgeRouter` of other chains
  - Looks for ERC20 token in registry
  - Sends token to recipient
    - If native, send from tokens held in escrow
    - If non-native, mints new tokens as the representative token contract is deployed by the `BridgeRouter`

## Questions

1. Why is an `Updater` called an `Updater` even tho its job is more like a validator?

2. Can a `Relayer` send fraudulent updates? What if it does, and who gets slashed in that case?

No, it can't as it's job is just to relay whatever updates it polls to receiving chain.

4. What are the future plans of tackling this optimistic timeout delay?

Right now, this is more of a feature than a disadvantage as it makes system more robust and for implementation ease as well. Need more idea about future plans. Moreover, connext plays out an important role here to partner with Nomad and reduce this to nearly 2 mins.

5. Does this `30 min` delay quantified on a basis? How is it chosen? Can it be variable on the basis of watcher numbers or some other factors? or is it fixed?

Research: https://medium.com/offchainlabs/fighting-censorship-attacks-on-smart-contracts-c026a7c0ff02

6. How does a bridge handles fork on a sending chain? Does the updates get rolled back?

Maybe here the timeout delay plays out as an advantage, if a fork occurs on a sending chain, the `Relayer` can mark update as invalid and it is no longer processed by `Processor`.

7. Nomad assumes security on the presence of even 1 watcher, doesn't that create centralization risk? Can that 1 watcher be the same updater?

8. Can Nomad leverage ETH consensus mechanisms like RANDAO to pseudo-randomly select updators and watchers?

## Links

- [Nomad Docs](https://docs.nomad.xyz)
- [Optimistic Brides](https://blog.connext.network/optimistic-bridges-fb800dc7b0e0)
- [The Cheater Checking Problem: Why the Verifier’s Dilemma is Harder Than You Think](https://medium.com/offchainlabs/the-cheater-checking-problem-why-the-verifiers-dilemma-is-harder-than-you-think-9c7156505ca1)
