# Notes on Ethereum

## What is the Ethereum blockchain?

The Ethereum blockchain is a replicated state machine where every node processes update events
called "transactions" and updates its state accordingly.  A consensus mechanism enforces
eventual consistency between the nodes.  The current consensus mechanism is based on
Proof-of-Work (PoW) but will be upgraded to a Proof-of-Stake (PoS) protocol, code-named
"Casper".  

Smart contracts that run in the Ethereum blockchain (state machine) enable not only regular
financial transactions, but complex derivatives, and furthermore, the operation of
distributed, autonomous organizations (DAOs). 


## What is a smart contract?

A smart contract is essentially a computer program that is always running in the Ethereum blockchain.
The blockchain maintains the state of the program so that it can persist balances of *ether*, the
native currency of Ethereum, alongside other variables that are used in the program.  This state
is called *storage*.  


### The world state

As stated previously, the blockchain maintains a globally consistent state through consensus.  This is
often called the "world state", due to Ethereum's nickname as the "world computer".  This state can be
thought of as a mapping from addresses to accounts.

The world state is not literally stored in the blocks of the blockchain; however, any node can re-create
this state by playing through the transactions of every block.  This is why it's important to remember
that Ethereum is a state machine.  Every node must process every transaction in order to update to
the latest state.  The trade-off here is security versus ease-of-use.  

The world state is stored on a node as a trie.  Many of the important data in Ethereum is stored using
the trie data structure.  This includes storage and transaction logs.

#### Account
Each address maps to an account, which is stateful. An account consists of:

- balance
- nonce
- code hash
- storage root

*Balance* is the ether balance the account owns, in Wei.    

*Nonce* is a counter.  Its meaning differs depending on the kind of account.  Accounts can be of two
types: externally-owned (EOA) or a contract (CA).  An EOA nonce counts how many transactions have
originated from that account.  A CA nonce is one more than the number of contract creations initiated
by the CA.

*Storage root* is the root hash of the storage trie belonging to the account.  This field is empty for
an EOA and for CAs without any storage variables set.

*Code hash* is the hash of the EVM bytecode of the account.  This field is the hash of the empty string
for an EOA.


### Block header

An Ethereum block header contains the root of three Merkle-Patricia tries:

- transactions
- receipts
- state






## References / Resources

### World state, Merkle Trees
https://pegasys.tech/ethereum-explained-merkle-trees-world-state-transactions-and-more/
https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/

