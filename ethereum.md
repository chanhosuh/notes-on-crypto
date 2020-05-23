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


### Transactions

A transaction is an atomic operation on the world state initiated by an EOA.  A transaction can succeed or fail but can never execute partially.  In the even of a failed transaction,
any effects on the state are rollbacked.  However, the EOA will not get refunded the gas
cost, as that is required to pay the miners; this is protection against DoS.  Failed, but mined, transactions
are still stored on the blockchain however.

A transaction is one of two types:

- sends a message call to an account (EOA or CA), either to change its state or transfer value (the latter
is a special case of the former).
- contract deployment, which creates a contract at a new address

Note that calling a contract can invoke further message calls to other accounts, but the entire
transaction is atomic, all calls succeed or they all get rollbacked.

Transaction fields:

- nonce:
  number of transactions sent by originating account
- gasprice:
  value in wei paid per unit of gas for executing the transaction
- gaslimit:
  max amount of gas to be spent on transaction execution
- to;
  recipient address, EOA or CA
  for contract creation, this is empty (zero address)
- value:
  value in wei paid to recipient
  for contract creation, this is the starting balance of the created contract
- v, r, s:
  signature values used to authenticate the sender of transaction
- data:
  not used for contract creation
  usually has the ABI info needed to invoke a smart contract function
- init:
  only for contract creation
  EVM code for contract initialization





### Block header

An Ethereum block header contains the root of three Merkle-Patricia tries:

- previous header hash
- transactions trie root
- receipts trie root
- state trie root



Benefits of using Merkle-Patricia tries to implement mappings:
- fully deterministic, same key-value pairs generate the same tries, down to the byte level
- O(log n) insert, lookup, delete


## References / Resources

### Comprehensive
https://takenobu-hs.github.io/downloads/ethereum_evm_illustrated.pdf

### World state, Merkle Trees
https://pegasys.tech/ethereum-explained-merkle-trees-world-state-transactions-and-more/
https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/
https://github.com/ethereum/wiki/wiki/Patricia-Tree

### Smart contract storage
https://programtheblockchain.com/posts/2018/03/09/understanding-ethereum-smart-contract-storage/
https://medium.com/coinmonks/a-practical-walkthrough-smart-contract-storage-d3383360ea1b

