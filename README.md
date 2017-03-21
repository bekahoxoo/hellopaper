I made the yellow paper white and changed the font -- now it's both easier to print and
a little easier to read. Shout out to Gavin Wood for producing the densest blockchain
paper in all history.


This paper could badly do with a table of contents and summary, and so I'll offer the following.
We're going to skip over _all_ notation because I think it's unnecessary at best.

### Section 1, Intro (Page 1)

- Blockchains are generally for ensuring security of monetary transfers even when mutually
distrusting parties are transferring to one another. Ethereum is no different in this respect.
- BUT! The EVM is equipped with a Turing-complete (modulo gas limit) EVM, where functions/mini
programs can be executed alongside monetary transfers.
- Transparency is preserved.

### Section 2, Blockchain Paradigm (Page 2, first column)

- Ethereum can be viewed as a transaction-based state machine.
- Genesis block = genesis state.
- Subsequent transactions and function executions update the state!
- State includes values of accounts (with addresses as accounts), data at addresses, etc.
- With respect to Ether, we have the following:
    - 10^18 Wei = 1 Ether
    - 10^6 Szabo = 1 Ether
    - 10^3 Finney = 1 Ether

### Section 3, Conventions (Page 2, second column)

- Entirely awful notation which we don't need.

### Section 4, Blocks, State and Transactions (Page 3)

- This is actually a cool section and worth reading (up until we go back to
  state notation).
- This section is summarised really well in [this excellent diagram](https://i.stack.imgur.com/afWDt.jpg).
- Genuinely my advice is skip all notation and just look at the diagram and
  think about what can and can't occur. The section is pretty much saying
  'valid state updates result in valid trie updates which result in valid
  blocks'.

### Section 5, Gas and Payment (Page 6)

- Gas is used for the EVM to protect itself against endlessly looping, or malicious
participants executing functions that take so long the network goes out of sync, and
acts as a reward to the people who mine the blocks, the same way transaction fees do
in Bitcoin (and Ethereum!).
- In a transaction, the user can specify:
    - gasLimit, the upper bound on the amount of gas allowed to be used up in the transaction
    - gasPrice, the price (in Wei) per 1 unit of gas used (usually sits at something like 0.00000002 Ether/gas, or 200000 Wei/gas, so a normal transaction costs 0.00042 Ether (21000 gas * 0.00000002))


### Section 6, Transaction Execution (Page 7)




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
