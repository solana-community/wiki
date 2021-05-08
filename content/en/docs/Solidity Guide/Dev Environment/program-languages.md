---
title: "Program Languages"
weight: 2
description: >
    Supported languages for Solana program development.
---

### Rust

Solana has first class support for the Rust programming language.

##### New to Rust?

It may be difficult to learn at first, but once you master it, there's a good
chance you'll never look back. Rust has consistently ranked as the moved loved
programming language in Stack Overflow's developer surveys from many years.

Most Rust developers have learned through the Rust "book". The Rust book explains
Rust's unique and tricky features in an easy to understand way. [Give it a try!][Rust book]

##### Anchor

The easiest way to write Rust programs for Solana is by using the [Anchor] framework.

### Solidity?

Solidity is not yet supported on Solana. Solidity was built to work really well
for the Ethereum Virtual Machine (EVM).  However, Solana's smart contract VM was
designed with fundamental differences from the EVM and so it currently does
**not** support Solidity. There are two ongoing efforts to support Solidity on
Solana:

1. Porting the EVM to run on Solana
   - Driven by the team at [Neon Labs][Neon Labs]
   - Track development progress in the `#cybercore` channel on the [Solana Discord][Discord] server
   - Read the latest [Solana EVM project spec][Solana EVM spec].

2. Support Solidity for writing Solana programs
   - This is a cross-chain effort to allow compiling Solidity into byte code for
    many different blockchains including Solana.
   - Track development progress in the `#solang-solidity-compiler` channel on the
    [Solana Discord][Discord] server.
   - Learn more on the [Solang documentation][Solang] site.

### C

Solana has support for C as well but the vast majority of resources are written
for Rust. C developers are recommended to use Rust for a better development
experience.

### Move

Solana has previously experimented with adding support for the [Move] language
but has set that project aside to prioritize other projects.

[Anchor]: https://project-serum.github.io/anchor
[Discord]: https://solana.com/discord
[Move]: https://move-book.com/
[Neon Labs]: https://github.com/neonlabsorg/
[Rust book]: https://doc.rust-lang.org/book/
[Solana EVM spec]: https://docs.google.com/document/d/1_Ncp-pVZlzzBnJ0a3H-BRi2lBKT5OQ6ubPfMegtgXLk
[Solang]: https://solang.readthedocs.io/en/latest/

