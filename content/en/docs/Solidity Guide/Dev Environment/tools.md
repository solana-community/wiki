---
title: "Development Tools"
weight: 1
description: >
    Recommended tools for Solana program development.
---

### Anchor is the Solidity of Solana

The Solana program SDK provides low-level interfaces for writing
smart contracts. It doesn't help you define methods for you contract
or how data is stored, you can decide for yourself what tools you want to use.

The [Anchor] framework makes Solana program development *much* simpler.
Developing a contract with Anchor will a little closer to the experience of
building with Solidity. It allows programs to define human readable
method names and provides helpers for serializing contract state in many
different programming languages.

### `solana-test-validator` is the Ganache of Solana

Solana provides a tool called `solana-test-validator` which starts up
a local node on your machine for development purposes. It is installed as
part of the [Solana Tool Suite][Solana Tool Suite].

#### Useful `solana-test-validator` Options

##### `--reset` 

Running `solana-test-validator` to setup a new node with a local blockhain
ledger. Restarting from the same directory will reuse the same ledger state.
Pass the `--reset` flag to start fresh.

##### `--bpf-program`

Add your own locally built programs (smart contracts) to your local blockchain's
genesis block by using the `--bpf-program` option for as many programs as you
need. This is useful for speeding up development iterations and setting up other
programs that your program will call.

##### `--clone`

Copy the account state from another cluster by using the `--clone` option. This must
be used along with the `--url` option to specify which cluster to fetch accounts from.

### `solana-program-test` is the Truffle of Solana

Solana provides Rust package called
[`solana-program-test`][`solana-program-test`] which includes a test framework
which can be used to test programs in a local Solana VM instance. Check out the
[Solana Program Library][Solana Program Library] to learn how to use it.

[Anchor]: https://project-serum.github.io/anchor 
[`solana-program-test`]: https://github.com/solana-labs/solana-program-library
[Solana Program Library]: https://github.com/solana-labs/solana-program-library
[Solana Tool Suite]: https://docs.solana.com/cli/install-solana-cli-tools
