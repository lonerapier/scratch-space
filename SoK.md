# SoK

Link: [https://eprint.iacr.org/2019/1128.pdf](https://eprint.iacr.org/2019/1128.pdf)

Aims to develop a guide for designing protocols bridging different types of blockchains (distributed ledgers).

Shows that CCC is impossible without ***third party.***

Presents a framework keeping these trust assumptions in mind. Classifies current CCC protocols on the basis of framework.

## Introduction

- NB-AC (*Non-Blocking Atomic Commit*) is used in distributed databases to ensure that correct processes don't have to wait for crashed processes to recover.
- Can be extrapolated to distributed ledgers by handling *byzantine failures.*

## Distributed Ledger Model

- $X, Y$: Blockchains
- $Lx$, $Ly$: ledgers with *states* as dynamically evolving sequences of *transactions*
- state of ledgers progresses in round *r.*
- $L^P[r]$:  state of ledger *L* at round *r* after all txs till *r-1*, according to some party *P.*
- Consistency is defined by the system
- ($TX$, $Lx^P[r]$): tx *TX* is valid for *Lx* at round *r* according to *P.*
- $TX$ ∈ $L^P[r]$: TX is included in *L* as position *r.*
- **Time** $L^P[t]$**:** ledger state at round r or time t.

**Persistence**: $L^P[t] <= L^Q[t’]$, $L^P$ at time $t$ is prefix of $L^Q$ at time $t$’.

**Liveness**: if tx $TX$ is included in ledger $L$ at time $t$, then it will appear in ledger at time $t$’.

### CCC System Model

- $P: TX_P, Q: TX_Q$: separate processes running on two different ledgers with txs
- $P$ possesses a description $d_Q$ which characterises the transaction $TX_Q$, while $Q$ possesses $d_P$ which characterises $TX_P$
- Thus, $P$ wants $Q$ to be written to $Ly$ and vice-versa.
- $m_P, m_Q$: boolean error variables for malicious processes

## Formalisation of correct CCC

Goal: sync of P and Q such that Q is included iff P is included. For example, they can constitute an exchange of assets which must be completed atomically.

**Effectiveness:** if both correct, then both will be included, otherwise none

**Atomicity**: no outcome in which $TX_P$ included but $TX_Q$ not at time $t$’ or vice versa.

**Timeliness**: If a process behaves correctly, $TX_P$ will be included and $Q$ will verify. It is a liveness property.

## Generic CCC Protocol

> $u_x$: liveness delay
>
> $k_x$: depth parameter

1. **Setup**: inherently done by both blockchains due to the properties defined above
2. **Pre-Commit on X**: $P$ writes $TX_P$ to $L^P_X$ at time $t$ in round $r$. Due to persistence and liveness, all honest parties report TX_P as valid in $r+u_x+k_x$.
3. **Verify**: Q verifies $TX_P$.
4. **Commit on Y:**  $Q$ writes $TX_Q$ to $L^Q_Y$ at time $t$’ in round $r$’.
5. **Abort**: revert $TX_P$ on $Lx$ in case of verification failure or $Q$ fails

![Image.png](https://res.craft.do/user/full/e83dd57b-d460-d205-2243-2f6ed8de496a/doc/2875A4D6-F00A-45A2-96EE-7222C31E634F/490D063A-EBE6-49DA-A5C7-D53342042837_2/q21xMyyBAkdRygr7FHp2PQr7J452ctL6JuxUysHHwccz/Image.png)

CCC protocols follow two-phase commit design.

Pre-commit and commit on Y is executed in parallel following verification and abortion, if required.

## Impossibility of CCC without TTP (Trusted Third Party)

Analogous to ***Fair Exchange*** Problem.

TTP is basically any entity, be it individual or a committee that either confirms a tx has been successfully included or enforce correct behaviour of $Q$ on $Ly$.

Lemma 1: Let $M$ be a system model. Let $C$ be a protocol which solves $CCC$ in $M$. Then there exists a protocol $S$ which solves *Fair Exchange* in $M$.

Sketch: to complete exchange, $TX_Q \in  Ly$ and $TX_P \in Lx$.

- *effectiveness* enforces correct transfer for correct behaviour.
- Persistence and liveness enforce both txs to be eventually written to respective ledgers.
- Atomicity $<->$ Strong Fairness in Fair exchange

Smart contracts or code based solutions can be used to write $TX_Q$ to $Y$, in this case consensus becomes TTP to execute this smart contract.

TTP either becomes the process $P$ itself or another party which submits proof of $P$ inclusion to $Q$.

Many other frameworks for designing a CCC protocol:

- Incentivizing third party
- Slashing the rewards
- Optimistic

## CCC Design Framework

Three main types of trust model:

- TTP
- Synchrony
- Hybrid

### Pre-Commit Phase

#### Model 1: TTP (Coordinators)

Can participate in two ways:

- Custody of Assets: taking control of protocol participant funds to enforce rules
- Involvement in consensus: in case of smart contracts, when consensus participants are TTP

Coordinator Implementations

- External Custodians: Committee
- Consensus Level Custodians (Consensus Committee)
- External Escrows (Multisig Contracts)

#### Model 2: Synchrony (Locking)

- Locks based on hashes
- Locks based on signatures
- Timelock puzzles & Verifiable delay fns
- Smart Contracts

#### Model 3: Hybrid

Watchtowers (Other external parties) to be used as fallback if one of the service fails or crashes

### Verification Phase

Same models but applied on verification part

1. External Validators/Smart Contracts
2. Direct Observation/Relay SCs (Using light clients)
3. Hybrid using watchtowers

### Abort Phase

## Classification of Existing Protocols

### Exchange Protocols

Atomic exchange of digital goods: $x$ on Chain $X$ again $y$ on $Y$. Both parties pre-commit, then verify and abort in case of failure.

#### Pre-Commit

Done through atomic swaps

- Both parties lock assets on-chain with identical release conditions. *Hashed Timelock contracts* are the closest implementation of symmetric locks. Signature locks using *ECDSA* are also used.
- On turing-complete blockchains, atomic swaps can be handled through smart contracts which can verify the state of chain $Y$ (*chain relay*).
- Hybrid: symmetric with TTP is used to solve usability challenges in atomic swaps.

#### Verify

Done through external validators in symmetric swaps or through chain relays in SPV based atomic swaps.

#### Abort

Timelocks are set up on assets for a pre-defined duration to prevent indefinite lock up in case of failures.

### Migration Protocols

Migrate the asset $x$ from chain $X$ using write locks on $x$ preventing further use on $X$ and creating a wrapped version of same asset on $Y$.

Four main use cases of these protocols:

- Wrapped version of assets between chains
- communication b/w shards
- sidechains
- bootstrapping a new chain

#### Pre-commit

Relies on a single/committee based external custodian for TTP or through multisigs.

**Sidechains**: same approach of depositing on chain $x$ controlled via multisigs which approve asset $y$ on chain $Y$.

**Shards**: utilises the same security and consensus model as the main chain is same for all shards.

*Bi-directional chain relays* can also be used if both chains support smart contracts and thus, locking/minting of assets can be handled through these contracts.

**Proof of Burn**: used for uni-directional flow as asset $x$ is burned on chain $X$.

#### Verify

- Chain relay contracts
- Consensus committees to sign to verify pre-commit step.

#### Abort

Migration protocol doesn’t have explicit abort phase.

## CCC Challenges

### Heterogeneous Models and Parameters Across Chains

- Different parameters used by different chains
- security models
- consensus differences: consensus execution, finality

### Cryptographic Primitives

different cryptographic algorithms for hash locks or signatures

ZK proofs may provide a workaround but increases complexity, communication costs.

### Collateralization and Exchange Rates

Using collaterals to prevent malicious behaviour among custodians or TTPs, incentivising correct behaviour but different types and rates of collateral b/w different chains.

Dynamic Collateralization based on exchange rates among different blockchains

#### Lack of Formal Security Analysis

- Replay Attacks on state verification: if proofs are submitted multiple times either on the same chain or on different chains can lead to multiple spendings of assets.
- Data availability: timely requirements of proofs and data, if not reached in time, leads to incorrect behaviour of process.

Need more research on this topic as current solution increases complexity and decreases efficiency.

#### Lack of Formal Privacy Analysis

didn’t understand perfectly

### Upcoming Research

- Interoperability chains: Cosmos and polkadot Layer 0 based ecosystems.
- Light Clients: for better verification
- Off-Chain Protocols
   - Communication across off-chain channels
   - Communication b/w on-chain and off-chain networks

