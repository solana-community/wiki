---
title: "Dictionary"
weight: 1000
description: >
  A list of commonly used terms on Solana and how they map to Ethereum
---

| Term                     | Description                                                                                         |
|--------------------------|-----------------------------------------------------------------------------------------------------|
| Associated token account | Wallet's default address for the holdings for a certain token.                                      |
| ATA                      | Associated token account.                                                                           |
| Confirmed                | Commitment level which indicates that 2/3 of the active stake has voted on a block.                 |
| Epoch                    | Length of time equal to 432,00 slots (~2 days) used for scheduling leaders, warming up stake, etc.  |
| Finalized                | Commitment level which indicates that 2/3 of the active stake has finalized a block.                |
| Fee payer                | The first account which signed a Solana transaction, similar to `msg.sender`.                       |
| Instruction              | Represents a call to an on-chain program. Solana transactions can contain one or more instructions. |
| Lamport                  | The smallest unit of SOL. Similar to Ethereum's "wei" unit.                                         |
| Leader                   | Validator whose turn it is to produce a block.                                                      |
| Native program           | Solana program which is pre-compiled, examples include the System, Stake, and Vote programs.        |
| On-chain program         | Solana's name for smart contracts.                                                                  |
| Processed                | Commitment level which can be used to interact with the most recently produced block.               |
| PDA                      | Program derived account.                                                                            |
| Program derived account  | Special account who's address is derived from a program id.                                         |
| Program ID               | Address of a deployed on-chain program.                                                             |
| Slot                     | Length of time (~500ms) in which a designated leader may optionally produce a block.                |
| SOL                      | The native token of Solana's blockchain. Similar to ETH on Ethereum.                                |
| SPL                      | Solana program library, a collection of reference programs similar to Open Zeppelin.                |
| SPL token                | The ERC20 equivalent on Solana. Each SPL token uses the same deployed SPL Token program.            |
| Syscall                  | Similar to Ethereum's precompiles.                                                                  |
| System program           | Native program which is responsible for SOL transfers and account allocation.                       |
| Sysvar                   | Special Solana account which stores blockchain context like current slot or epoch.                  |
| Transaction              | Signed list of instructions that are executed atomically on Solana.                                 |
