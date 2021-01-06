# Notes on Ethereum

## What is the Ethereum blockchain?

The Ethereum blockchain is a replicated state machine where every node processes events
called "transactions" and updates its state accordingly.  A consensus mechanism enforces
eventual consistency between the nodes.  The current consensus mechanism is based on
Proof-of-Work (PoW) but will be upgraded to a Proof-of-Stake (PoS) protocol, code-named
"Casper".  

The blockchain enables *smart contracts*, computer programs that once deployed to
the blockchain, cannot be changed and are accessible to anyone willing to pay for the cost
of code execution by *miners*.

These smart contracts maintain their own state, including balances, and so are able to act
as counterparties in financial transactions or even operate autonomous organizations through
voting or other mechanisms.


## The world state

The blockchain maintains a globally consistent state through consensus, often called the
*world state*, due to Ethereum's nickname as the "world computer".  The world state is a
mapping from addresses to accounts.

The world state is not literally stored in the blocks of the blockchain; however, any node
can re-create this state by executing the transactions of every block.  This is why it's
important to remember that Ethereum is a state machine.  Every node must process every
transaction in order to update to the latest state.  This sacrifices ease-of-use for security,
as every state is then validated by every node in the network. 

The world state is stored on a node as a trie.  Many of the important data in Ethereum is
stored using the trie data structure.  This includes *storage* for each smart contract, and
transaction logs, called *receipts*. Ethereum uses a particular form of trie called a
*Merkle-Patricia trie*.  This is a highly efficient data structure that allows *Merkle proofs*.


## Account
Each address maps to an account, which is stateful. An account consists of:

- balance
- nonce
- code hash
- storage root

*Balance* is the ether balance the account owns, in Wei.    

*Nonce* is a counter.  Its meaning differs depending on the kind of account.  Accounts can be
of two types: externally-owned (EOA) or a contract (CA).  An EOA nonce counts how many
transactions have originated from that account.  A CA nonce is one more than the number of
contract creations initiated by the CA.

*Storage root* is the root hash of the storage trie belonging to the account.  This field is
empty for an EOA and for CAs without any storage variables set.

*Code hash* is the hash of the EVM bytecode of the account.  This field is the hash of the
empty string for an EOA.


### Externally-owned accounts (EOA)
We may think of an EOA as belonging to an actor external to the Ethereum network.  From the
viewpoint of Ethereum itself, the only difference between an EOA and a CA is that an EOA has:

- a private key
- no EVM bytecode
- no storage 

On the other hand, a CA has:

- no private key
- EVM bytecode
- storage

An EOA's private key is used to sign transactions.  Every transaction must be signed by an
EOA and every change to the world state is initiated by a transaction.


### Smart contract accounts (CA)

A smart contract gets created by a transaction which contains all the contract bytecode in its `init` field.  Upon creation, it is assigned an address, which allows value transfers to and from it, in addition to message calls to it from either EOAs or other smart contracts.

The smart contract's *storage* maintains variables, such as its ether balance and others that may be used in the program.


## Transactions

A *transaction* is an atomic operation on the world state initiated by an EOA.  A transaction can succeed or fail but can never execute partially.

Every transaction costs *gas*, which is the unit of computation on the Ethereum blockchain.  Each gas unit for transaction execution must be paid for with ether.  A transaction sender will specify a *gas price* s/he is willing to pay and also a *gas limit*, an upper bound for the maximum amount of gas he or she is willing to pay to execute the transaction.

In the event of a failed transaction, any effects on the state are rollbacked.  However, the EOA will not get refunded the gas cost, as that is required to pay the miners; this is protection against DoS. Failed, but mined, transactions are still stored on the blockchain however.

A transaction is one of two types:

- a *message call*: sent to an account (EOA or CA), either to change its state or transfer value (the latter
is a special case of the former).
- *contract creation*: creates a contract at a new address (sometimes called contract deployment)

Note that calling a contract can invoke further message calls to other accounts, but the entire transaction is atomic, all calls succeed or they all get rollbacked.

Transaction fields:

- *nonce*:
  number of transactions sent by originating account
- *gas price*:
  value in wei paid per unit of gas for executing the transaction
- *gas limit*:
  max amount of gas to be spent on transaction execution
- *recipient (to)*:
  recipient address, EOA or CA
  for contract creation, this is empty (null address)
- *value:*
  value in wei paid to recipient
  for contract creation, this is the starting balance of the created contract
- *data:*
  usually has the ABI-encoded data needed to invoke a smart contract function, but
  for contract creation, will have the constructor initilization and smart contract
  bytecode
- *v*:
  elliptic curve signature component
- *r*:
  elliptic curve signature component
- *s*:
  elliptic curve signature component

The last three fields constitute the signature used to authenticate the sender of transaction.

Note a transaction must be sent by an EOA, not a CA.  This is because it requires a
signature, which only EOAs have.  As mentioned above, a transaction may spawn
multiple message calls as side-effects but these do not have signatures.

The "data" field contains the encoded information to call the appropriate
function on a smart contract with the necessary arguments.  This encoding is necessary
since the EVM processes bytecodes, not the higher-level abstractions of the smart
contract language.  This is analogous to how a CPU on a PC understands assembly code,
not higher-level programming languages like Java or Python.  This encoding is called
the ABI.


## Blocks

Transitions to the world state happen through transactions, but transactions are not processed
individually by miners but in a batch called a *block*.  Each miner assembles a block from a
pool of available transactions and then engages in proof-of-work on the block.  If the miner
succeeds in finding a solution to the proof-of-work puzzle, it will submit the block (with
solution) to the network for validation and propagation.  

Thus the world state updates one batch of transactions at a time.  The resulting state may
depend on the ordering of transactions within the block and it is important to note that this
order is completely determined by the miner.


### Block headers

An Ethereum block header consists of:

- parenthash: previous header hash
- ommershash: Merkle hash of the ommers headers
- beneficiary: account of recipient of transaction fees
- stateroot: root hash of world state trie
- transactionsroot: root hash of transactions trie
- receiptsroot: root hash of transaction receipts trie
- logsbloom: bloom filter to check what event logs were generated in this block 
- difficulty
- number
- gaslimit
- gasused
- extradata
- mixhash
- nonce


## Merkle-Patricia tries

Benefits of using Merkle-Patricia tries to implement mappings:

- allows Merkle proofs, i.e. a proof can be provided for every key-value pair, allowing light clients
  to trust the given key corresponds to the value, without having to maintain the world state
- O(log n) insert, lookup, delete
- fully deterministic, same key-value pairs generate the same tries, down to the byte level
- more space / time efficient than regular tries, due to Patricia path compression


## References / Resources

### Comprehensive
https://takenobu-hs.github.io/downloads/ethereum\_evm\_illustrated.pdf

### World state, Merkle Trees
https://pegasys.tech/ethereum-explained-merkle-trees-world-state-transactions-and-more/
https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/
https://github.com/ethereum/wiki/wiki/Patricia-Tree

### Transactions
https://programtheblockchain.com/posts/2017/12/29/how-ethereum-transactions-work/

### Smart contract storage
https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/
https://medium.com/coinmonks/a-practical-walkthrough-smart-contract-storage-d3383360ea1b

