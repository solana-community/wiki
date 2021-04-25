---
title: "Ethereum Comparison"
weight: 4
description: >
  An overview of Solana and how it compares and contrasts with Ethereum, Solidity, and the EVM
---

#### We're in this together!

Alright Solidity devs, it's about time we've had a heart to heart or shall we say "Sol to SOL" conversation about
Solana development. First, this is not meant to convince anyone that one blockchain is better than another, this
isn't about maximalism. This is about learning from each other and getting into the finer technical details about how
the two platforms are different. This guide aims to be a resource for experienced Solidity developers who decide to
build an application on Solana.

#### Why is Solana so different?

By far the biggest reason the development experience between Solana and Ethereum is so different is due to their
account model designs. Before we dig into those, it's helpful to understand *why* Solana's account model was designed
so differently from Ethereum. Unlike Ethereum, which is designed to run on consumer grade hardware, Solana was
designed to optimize transaction throughput on high-end multi-core machines. The Solana team noticed a trend that the
number of cores in computers is growing exponentially. In order to take advantage of all these cores and attempt to
future-proof the protocol, the Solana team designed transactions to be easily **parallelized**.

#### Account model design

So what is actually meant by the "account model" of a blockchain? Well, when an on-chain program is called on a
blockchain like Solana or Ethereum, the smart contract needs a way to track certain state like token balances, who
owns an NFT, or who the current highest bidder in auction is. All of this state is stored inside accounts on the
blockchain and is replicated perfectly across all the nodes in a cluster.

On Ethereum, each smart contract is an account which has its own storage. The smart contract's code specifies how to
make sense of that storage. On Solana, an on-chain program is a *completely immutable* account and its storage is
only used to store executable byte code. So where do Solana programs store state? In other non-executable accounts!
In fact, each Solana account specifies a program *owner* which is the only program allowed to make modifications to
the account.

Wait, so why does Solana force programs to store data inside other accounts, doesn't this add extra complexity? Well,
yes sure but it is also the key part that helps enable parallelization.

#### Transaction Parallelization

Each Solana transaction must list *all* of the accounts that will be either read from, written to, or invoked while
being processed. With all this information listed up front, Solana validators know ahead of time which transactions
can be processed at the same time without conflicting with each other. To fully take advantage of this
parallelization, on-chain programs themselves can split their state across many accounts so that they can be
parallelized too. For example, the Serum Dex program has separate storage accounts for each new market and so
transactions on one market won't slow down transactions on another market because they can all be run in parallel.

#### Design Constraints

Solana's design and constraints force developers to carefully consider the design of their own on-chain programs.
Learning how to write Solana programs has an arguably steeper learning curve than Solidity smart contracts. But don't
forget the upside! By doing a bit more work upfront, Solana transactions can be processed very efficiently. This
results in both higher throughput *and* lower fees. But this isn't to say Solana is always the solution. As
developers, we make tradeoffs all the time when choosing our tools. The development experience with Solidity on the
EVM is *much* more flexible than on Solana without all the overhead of figuring out which accounts will be accessed
and which contracts will be called.
