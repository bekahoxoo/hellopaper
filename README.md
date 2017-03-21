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

### Section 5. Gas and Payment (Page 6)

- Gas is used for the EVM to protect itself against endlessly looping, or
  malicious participants executing functions that take so long the network goes
  out of sync, and acts as a reward to the people who mine the blocks, the same
  way transaction fees do in Bitcoin (and Ethereum!).
- In a transaction, the user can specify:
    - gasLimit, the upper bound on the amount of gas allowed to be used up in
      the transaction
    - gasPrice, the price (in Wei) per 1 unit of gas used (usually sits at
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

### Section 8. Message Call (Page 9)

### Section 9. Execution Model (Page 10)

### Section 10. Blocktree to Blockchain (Page 12)

### Section 11. Block Finalisation (Page 12)

### Section 12. Implementing Contracts (Page 13)

- Explains 'data feeds', which make data from the outside (non-blockchain) world
available to the Ethereum blockchain. Generally contracts using/offering such data
feeds are called oracles.
- Also explains randomness in contracts -- due to the deterministic nature of the EVM,
there is no urandom or similar. There are ways to get some sort of randomness, for
example using the blockhash of a previously mined block

### Section 13. Future Directions (Page 14)

- Scalability!! Aahhhh!!

## Appendices

The appendices are where most of the interesting/essential information is.
There are many of them.

### Appendix A. Terminology (Page 15)

### Appendix B. Recursive Length Prefix (Page 15)

- Ethereum's encoding method for arbitrarily structured binary data.

### Appendix C. Hex-Prefix Encoding (Bottom of Page 16)

### Appendix D. Modified Merkle Patricia Tree (Page 17)

### Appendix E. Precompiled Contracts (Page 18)

### Appendix F. Signing Transactions (Bottom of Page 18)

- ECDSA over secp256k1.
- Unnecessary declarations.

### Appendix G. Fee Schedule (Page 20)

- Prices of executing each of the precompiles/opcodes on the EVM (defined in gas)

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
