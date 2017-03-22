
# Hello Paper

I made the yellow paper white and changed the font -- now it's both better for printing and
a little easier to read.


This paper could badly do with a table of contents and summary, and so I'll offer the following.
We're going to skip over _all_ notation because I think it's unnecessary.

### Section 1. Intro (Page 1)

- Blockchains are generally for ensuring security of monetary transfers even when mutually
distrusting parties are transferring to one another. Ethereum is no different in this respect.
- BUT! The EVM is equipped with a Turing-complete (modulo gas limit) EVM, where functions/mini
programs can be executed alongside monetary transfers.
- Transparency is preserved.

### Section 2. Blockchain Paradigm (Page 2, first column)

- Ethereum can be viewed as a transaction-based state machine.
- Genesis block = genesis state.
- Subsequent transactions and function executions update the state!
- State includes values of accounts (with addresses as accounts), data at addresses, etc.
- With respect to Ether, we have the following:
    - 10^18 Wei = 1 Ether
    - 10^6 Szabo = 1 Ether
    - 10^3 Finney = 1 Ether

### Section 3. Conventions (Page 2, second column)

- Entirely awful notation which we don't need.

### Section 4. Blocks, State and Transactions (Page 3)

- This is actually a cool section and worth reading (up until we go back to
  state notation).
- This section is summarised really well in [this excellent diagram](https://i.stack.imgur.com/afWDt.jpg).
- Genuinely my advice is skip all notation and just look at the diagram and
  think about what can and can't occur. The section is pretty much saying
  'valid state updates result in valid trie updates which result in valid
  blocks'.

#### State

The account state comprises the following four fields:

- **nonce**: A scalar value equal to the number of transactions sent from this
  address or, in the case of accounts with associated code, the number of
  contract-creations made by this account.
- **balance**: A scalar value equal to the number of Wei owned by this address.
- **storageRoot**: A 256-bit hash of the root node of a Merkle Patricia tree that
  encodes the storage contents of the account (a mapping between 256-bit
  integer values), encoded into the trie as a mapping from the Keccak 256-bit
  hash of the 256-bit integer keys to the RLP-encoded 256-bit integer values.
  (RLP is discussed in Appendix B.)
- **codeHash**: The hash of the EVM code of this account—this is the code that gets
  executed should this address receive a message call; it is immutable and
  thus, unlike all other fields, cannot be changed after construction. All such
  code fragments are contained in the state database under their corresponding
  hashes for later retrieval.

#### Transactions

A transaction is signed with the ECDSA private key associated with the address
from which it originates. In full, the transaction is formed as follows:

- **nonce**: A scalar value equal to the number of transactions sent by the sender.
- **gasPrice**: A scalar value equal to the number of Wei to be paid per unit of
  gas for all computation costs incurred as a result of the execution of this
  transaction.
- **gasLimit**: A scalar value equal to the maximum amount of gas that should be
  used in executing this transaction. This is paid up-front, before any
  computation is done and may not be increased later.
- **to**: The 160-bit address of the message call’s recipi- ent or, for a contract
  creation, 0.
- **value**: A scalar value equal to the number of Wei to be transferred to the
  message call’s recipient or, in the case of contract creation, as an
  endowment to the newly created account.
- **v, r, s**: Values corresponding to the signature of the transaction and used to
  determine the sender of the transaction.

Additionally, if the transaction is triggering contract creation, we have the
following:

- **init** is an EVM-code fragment; it returns the body, a second fragment of
  code that executes each time the account receives a message call (either
  through a transaction or due to the internal execution of code). init is
  executed only once at account creation and gets discarded immediately
  thereafter.

In contrast, a **message call transaction**, as discussed in Section 8, contains:

- data: An unlimited size byte array specifying the input data of the message
  call.

#### Blocks

Blocks contain all of the following:

- **parentHash**: The Keccak 256-bit hash of the parent block's header.
- **ommersHash**: The Keccak 256-bit hash of the ommers (uncles) list portion
  of this block.
- **beneficiary**: The 160-bit address to which all mining fees of the given
  block are transferred.
- **stateRoot**: The Keccak 256-bit hash of the root node of the state trie,
  after all transactions are executed and finalisations applied.
- **transactionsRoot**: The Keccak 256-bit hash of the root node of the trie
  structure populated with each transaction in the transactions list portion of
  the block.
- **receiptsRoot**: The Keccak 256-bit hash of the root node of the trie
  structure populated with the receipts of each transaction in the transactions
  list portion of the block.
- **logsBloom**: The **Bloom filter** composed from indexable information
  (logger address and log topics) contained in each log entry from the receipt
  of each transaction in the transactions list.
- **difficulty**: A scalar value corresponding to the difficulty level of this
  block. This can be calculated from the previous block's difficulty level and
  the timestamp.
- **number**: A scalar value equal to the number of ancestor blocks. The
  genesis block has a number of zero.
- **gasLimit**: A scalar value equal to the current limit of gas expenditure
  per block.
- **gasUsed**: A scalar value equal to the total gas used in transactions in
  this block.
- **timestamp**: A scalar value equal to the reasonable output of Unix's time()
  at this block's inception.
- **extraData**: An arbitrary byte array containing data relevant to this
  block. This must be 32 bytes or fewer.
- **mixHash**: A 256-bit hash which proves combined with the nonce that a
  sufficient amount of computation has been carried out on this block.
- **nonce**: A 64-bit hash which proves combined with the mix-hash that a
  sufficient amount of computation has been carried out on this block.

#### Misc

This section also discusses the construction of the **transaction reciept** and
**holistic validity** checks, **serialisation**, and **block header validity**
as they apply to Ethereum.

### Section 5. Gas and Payment (Page 6)

- Gas is used for the EVM to protect itself against endlessly looping, or
  malicious participants executing functions that take so long the network goes
  out of sync, and acts as a reward to the people who mine the blocks, the same
  way transaction fees do in Bitcoin (and Ethereum!).
- In a transaction, the user can specify:
    - **gasLimit**: the upper bound on the amount of gas allowed to be used up
      in the transaction
    - **gasPrice**: the price (in Wei) per 1 unit of gas used (usually sits at
      something like 0.00000002 Ether/gas, or 200000 Wei/gas, so a normal
      transaction costs 0.00042 Ether (21000 gas * 0.00000002))

### Section 6. Transaction Execution (Page 7)

- Transaction execution checks:

    - Transaction follows Ethereum's custom encoding properly (specifically, it
      is 'well-formed RPC, with no additional trailing bytes');
    - Signature verifies
    - Transaction nonce is equal to nonce of sender address (eg, nonce = 0 for
      1st transaction sent from an address -- this is to help prevent replaying
      transactions to empty an address, and to provide some sort of ordering of
      transactions, I guess)
    - Gas limit greater than or equal to gas consumed by transaction (21,000
      minimum?)
    - Sender address balance greater than or equal to value of transaction (ie
      transaction isn't trying to transfer more money than the sender actually
      has!)

- Execution of valid transaction makes state change! The nonce associated with
  the sender address is incremented.
- If gasLimit > actual gas used by transaction, only the amount of gas that is
  used up by the transaction is taken, and the rest is refunded to the
  transaction sender.

### Section 7. Contract Creation (Page 8)

Here we use some notation that is introduced in Section 9 (???????), but there
are some interesting things you mightn't know about contract creation!

In brief, a contract creation involves:

- A number of intrinsic parameters used when creating an account:
    - sender (s),
    - original transactor (o),
    - available gas (g),
    - gas price (p),
    - endowment (value, v),
    - arbitrary length byte array, i, the initialisation EVM code and
    - the present depth of the message-call/contract-creation stack (e)

- The address of the new account is defined as the rightmost 160 bits of the Keccak hash
of the RLP encoding of the structure containing only **sender** and their **nonce**.

- Note that while the initialisation code (as explained in Section 8) is
  executing, the newly created address exists but with no intrinsic body code.
  Thus any message call received by it during this time causes no code to be
  executed. If the initialisation execution ends with a SUICIDE instruction,
  the matter is moot since the account will be deleted before the transaction
  is completed. For a normal STOP code, or if the code returned is otherwise
  empty, then the state is left with a zombie account, and any remaining
  balance will be locked into the account forever.

### Section 8. Message Call (Page 9)

This section is fairly succinct and the notation does actually aid the thinking
(maybe), but for the sake of completeness we'll try to explain here:

- Several parameters are required:
    - sender (s),
    - transaction originator (o),
    - recipient (r),
    - the account whose code is to be executed (c, usually the same as
      recipient),
    - available gas (g),
    - value (v),
    - gas price (p)
    - arbitrary length byte array, d, the input data of the call,
    - present depth of the message-call/contract-creation stack (e).

- Aside from evaluating to a new state and transaction substate, message calls
  also have an extra component — the output data, denoted by byte array o. This
  is ignored when executing transactions, however message calls can be
  initiated due to VM-code execution and in this case this information is used.

- Need to differentiate between the value that is to be transferred, v, from
  the value apparent in the execution context, ṽ, for the DELEGATECALL
  instruction.

### Section 9. Execution Model (Page 10)

This section is absolutely worth reading too :)
We'll practically just copy and paste and bullet-point for ease of reading:


#### Basics

- The EVM is a simple stack-based architecture.
- Word size of the machine (and thus size of stack item) is 256-bit.
- Chosen to facilitate the Keccak-256 hash, and elliptic-curve computations.
- Memory model is a simple word-addressed byte array.
- Stack has a maximum size of 1024.
- The machine also has an independent storage model; this is similar in concept
to the memory but rather than a byte array, it is a word-addressable word array.
- Unlike memory, which is volatile,
storage is non volatile and is maintained as part of the
system state.
- All locations in both storage and memory
are well-defined initially as zero.
- The machine does not follow the standard von Neumann architecture.
- Rather than storing program code in generally-accessible memory or storage, it is stored
separately in a virtual ROM interactable only through a specialised instruction.
- The machine can have exceptional execution for several reasons, including:
    - stack underflows,
    - invalid instructions.
- Like the out-of-gas (OOG) exception, they do not leave state changes intact.
- Rather, the machine halts immediately and reports the issue to the execution
  agent (either the transaction processor or, recursively, the
  spawning execution environment) which will deal with it
  separately.

#### Fees

- Fees (denominated in gas) are charged under three distinct circumstances, all
  three as prerequisite to the execution of an operation.
    - The first and most common is the fee intrinsic to the computation of the
      operation (see Appendix G).
    - Secondly, gas may be deducted in order to form the payment for a
      subordinate message call or contract creation; this forms part of the
      payment for CREATE, CALL and CALLCODE .
    - Finally, gas may be paid due to an increase in the usage of the memory.

- Over an account's execution, the total fee for memory- usage payable is
  proportional to smallest multiple of 32 bytes that are required such that all
  memory indices (whether for read or write) are included in the range.
- This is paid for on a just-in-time basis; as such, referencing an area of
  memory at least 32 bytes greater than any previously indexed memory will
  certainly result in an additional memory usage fee.
- Due to this fee it is highly unlikely addresses will ever go above 32-bit
  bounds. That said, implementations must be able to manage this eventuality. (????????)
- Storage fees have a slightly nuanced behaviour, to incentivise minimisation
  of the use of storage (which corresponds directly to a larger state database
  on all nodes):
    - execution fee for an operation that clears an entry in the storage is not
      only waived, a qualified refund is given;
    - this refund is effectively paid up-front since the initial usage of a
      storage location costs substantially more than normal usage.
- See Appendix H for a complete breakdown of EVM gas costs.

#### Execution Environment

- In addition to system state, and remaining gas for computation, there are
  several pieces of important information used in the execution environment
  that the execution agent must provide:
    - address of the account which owns the code that is executing.
    - sender address of the transaction that originated this execution.
    - price of gas in the transaction that originated this execution.
    - byte array that is the input data to this execution; if the execution
      agent is a transaction, this would be the transaction data.
    - address of the account which caused the code to be executing; if the
      execution agent is a transaction, this would be the transaction sender.
    - value, in Wei, passed to this account as part of the same procedure as
      execution; if the execution agent is a transaction, this would be the
      transaction value.
      - byte array, of machine code to be executed.
      - current block header.
      - depth of the present message-call or contract-creation (i.e. the number
        of CALLs or CREATEs being executed at present).
- The rest of this section might be really great and helpful but I actually
  don't really understand what it's trying to say. Functions are executed in
  the EVM :P


### Section 10. Blocktree to Blockchain (Page 12)

This is where the 'most proof-of-work chain' or **heaviest chain** rule comes
into play -- instead of simply taking the 'longest' chain as defined by block
height (or 'block number' as it is called in the yellow paper), we take into
account the **difficulty** of each block as it was mined, and the 'correct'
chain becomes the heaviest chain, as weighted by the mining difficulty of each
block included in the chain in question.

### Section 11. Block Finalisation (Page 12)

Pretty much all we need to know here is:

- Validate (or, if mining, determine) ommers;
- validate (or, if mining, determine) transactions;
- apply rewards;
- verify (or, if mining, compute a valid) state and nonce.

### Section 12. Implementing Contracts (Page 13)

- Explains 'data feeds', which make data from the outside (non-blockchain)
  world available to the Ethereum blockchain. Generally contracts
  using/offering such data feeds are called oracles.
- Also explains randomness in contracts -- due to the deterministic nature of
  the EVM, there is no urandom or similar. There are ways to get some sort of
  randomness, for example using the blockhash of a previously mined block

### Section 13. Future Directions (Page 14)

- Scalability!
- state database won't be forced to maintain all past state trie structures
  into the future.
- It should maintain an age for each node and eventually discard nodes that are
  neither recent enough nor checkpoints; checkpoints, or a set of nodes in the
  database that allow a particular block’s state trie to be traversed, could be
  used to place a maximum limit on the amount of computation needed in order to
  retrieve any state throughout the blockchain.

- Blockchain consolidation could be used in order to reduce the amount of
  blocks a client would need to download to act as a full, mining, node.
- A compressed archive of the trie structure at given points in time (perhaps
  one in every 10,000th block) could be maintained by the peer network,
  effectively recasting the genesis block. This would reduce the amount to be
  downloaded to a single archive plus a hard maximum limit of blocks.
- Finally, blockchain compression could perhaps be con- ducted: nodes in state
  trie that haven't sent/received a transaction in some constant amount of
  blocks could be thrown out, reducing both Ether-leakage and the growth of the
  state database.

## Appendices

The appendices are where most of the interesting/essential information is.
There are many of them.

### Appendix A. Terminology (Page 15)

Defines basic Ethereum-related language

### Appendix B. Recursive Length Prefix (Page 15)

Ethereum's encoding method for arbitrarily structured binary data.

### Appendix C. Hex-Prefix Encoding (Bottom of Page 16)

What are nibbles?

### Appendix D. Modified Merkle Patricia Tree (Page 17)


### Appendix E. Precompiled Contracts (Page 18)



### Appendix F. Signing Transactions (Bottom of Page 18)

- ECDSA over secp256k1.
- Unnecessary declarations.

### Appendix G. Fee Schedule (Page 20)

- Prices of executing each of the precompiles/opcodes on the EVM (defined in
  gas)

### Appendix H. Virtual Machine Specification (Page 20)

- opcodes! Mappings of hex opcodes to explanations of functions.

### Appendix I. Genesis Block (Page 31)

### Appendix J. Ethash (Page 31)

- Ethereum's PoW algo

# yellowpaper

[![Gitter](https://badges.gitter.im/ethereum/yellowpaper.svg)](https://gitter.im/ethereum/yellowpaper?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

The paper comes as a single ``latex`` file ``Paper.tex``. The latest version is
generally available as a PDF at
https://ethereum.github.io/yellowpaper/paper.pdf or just
[yellowpaper.io](http://yellowpaper.io/) .

In order to build, use the supplied build script (``build.sh``). Following the
first time, you can use the standard `pdflatex` tools like
http://latex.informatik.uni-halle.de/latex-online/latex.php for
compiling/preview. It can be viewed in ``PDF`` format after building.
