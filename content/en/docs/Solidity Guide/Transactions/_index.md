---
title: "Transactions"
weight: 40
description: >
  Major differences between Ethereum and Solana transactions
---

Ethereum supports a few different types of transactions which can be created using a common set of parameters. These
include ETH transfers, smart contract calls, and new contract deploys. On Solana, all transactions are treated the
same and so all call on-chain programs (Solana has special programs for deploying contracts and transferring SOL).

Let's dig into the differences by looking at the structure of a transaction from each chain...

### Ethereum Transaction Structure

| Field      | Description                                                    |
|------------|----------------------------------------------------------------|
| `nonce`    | Number equal to the count of sender's processed transactions.  |
| `gasPrice` | The amount of Wei to be paid per unit of gas.                  |
| `gasLimit` | Max gas that can be consumed while processing the transaction. |
| `to`       | The recipient of ether and smart contract to be called.        |
| `value`    | The amount of ether to transfer to the `to` address.           |
| `v, r, s`  | Represents the `signature` and used to recover the `sender`    |

### Solana Transaction Structure

| Field             | Description                                                 |
|-------------------|-------------------------------------------------------------|
| `signatures`      | List of signatures.                                         |
| `accounts`        | List of accounts (read-only / read-write)                   |
| `recentBlockhash` | Blockhash of recently produced block used as nonce.         |
| `instructions`    | List of instructions which each call an on-chain program.   |

Despite their structural differences, these transactions have a very similar goal: calling a smart contract. Let's
walk through the fields to understand each approach.

### Mapping Ethereum transaction fields to Solana

#### `sender`

On Ethereum, the `sender` is the address of the keypair which signed this transaction. Inside a smart contract, we
know the `msg.sender` is an address that approved of the smart contract call because we trust Ethereum nodes to first
verify the transaction signature. Note: in Ethereum, the `sender` is actually recovered from the signature itself.

Also, the `sender` of a transaction is the account which will pay gas fees for the smart contract. By signing a
transaction, the `sender` authorizes payment of gas fees.

On Solana, the first `account` in the transaction `accounts` list is roughly the same thing as the `sender` in an
Ethereum transaction. It is the account that will be used to pay transaction fees and Solana will verify that the
first `signature` in the transaction `signatures` list was produced by that account.

#### `signature`

On Ethereum, each transaction contains a single signature. This signature is roughly the same as the first signature
in a Solana transaction's list of signatures. So why does Solana allow multiple signatures? Well, imagine you are
using a multisig wallet and need to create a transaction which shows that multiple keypairs have signed and approve
the transaction. On Ethereum, you would need to pass signatures inside transaction data and verify them inside a
smart contract. On Solana, signatures can be appended to the transaction signatures list and, since Solana nodes use
a GPU to verify signatures, will be verified much more efficiently than they would inside a program.

#### `nonce`

On Ethereum, each transaction includes a nonce which is used to prevent a single transaction from being processed
multiple times. Every time Ethereum processes a transaction, it requires that the transaction nonce value is equal to
the sender's total transaction count. So if you have sent 10 transactions, your next transaction will have a nonce
equal to 10 and after Ethereum checks the nonce and processes the transaction, it will increment the your transaction
count to 11 and wait for a transaction with that nonce.

Solana solves this problem in another way. Relatively old transactions cannot be processed again because each
transaction must specify a "recent" blockhash to be processed. Re-processing recent transactions is avoided by
requiring each node to keep a record of all the transactions for recent blocks. So transactions with an old
`recentBlockhash` are easily ignored and other transactions are ignored if they are already included in the recently
processed transaction list.

#### `to`

Ethereum transactions use `to` to specify an address to send ETH to or a smart contract to call.

Solana transactions can actually list multiple smart contracts to call and so they don't have a single `to` field.
Instead, they may list one or more "instructions" which each represent a smart contract call. Each instruction
specifies its own smart contract address and the input parameters for the call.

#### `value`

Ethereum transactions are always explicit about how much ether may be sent from a user's account when making a
transfer or invoking a smart contract. This amount is specified in the `value` field of a transaction and does not
include the gas cost of the transaction.

Solana transactions don't have an equivalent property which specifies how much SOL can be transferred. Instead, each
on-chain program has authority to withdraw lamports from any account it owns. By default, each account is owned by
the system program which requires an account to sign the transaction to perform a withdraw. Other programs may define
their own rules and typically support a withdraw or close account instruction which requires the account to sign.

#### `gasPrice` and `gasLimit`

Every operation in the EVM has an associated gas cost which must be paid by the transaction sender. Since transaction
throughput is limited by the amount of gas allowed in each block, gas price provides a way for transaction senders to
bid a higher price in order to be included in a block more quickly. Any transaction with a gas price that's too low
will get ignored by miners because they want to maximize their earnings in each block they mine. Gas limit is
specified to prevent a buggy smart contract from using way more gas than you intended and causing lost funds.

The Solana VM doesn't have the dynamic gas model for transactions. Instead, it has a fixed maximum compute cost which
currently cannot be adjusted. This means that each transaction roughly has the fixed cost and it naturally puts
pressure on developers to optimize on-chain code to fit within the system limits. Transactions do have fees on
Solana, though. Currently transaction fee calculation is very simple, each signature in a transaction costs an
additional 5000 lamports (there are 10^9 lamports in one SOL).

#### `data`

Ethereum transactions include a single `data` field for an unlimited size byte array. This data is passed directly to
a smart contract which if written with Solidity, will be decoded into a function and its parameters.

Solana transactions may include many "sub-transactions" called instructions. Each instruction has a `data` field
which is used in the same way as an Ethereum transaction's `data` field. However, note that an Solana instruction's
data is limited in size. The entire encoded size of a Solana transaction cannot exceed 1232 bytes. This constraint
allows Solana to optimize its networking layer for quickly passing transactions between nodes (smaller packets = less
delay).

### Understanding Solana transactions

#### `signatures`

Each Solana transaction allows for one or more signatures so that they can be efficiently verified by Solana
validator GPU's. This means multiple accounts can easily authorize operations in on-chain programs in the same
transaction. This contracts with Ethereum where any additional signatures beyond the sender must be verified inside a
smart contract.

#### `recentBlockhash`

Solana transactions must include the blockhash of a recently produced block. Blockhashes are considered recent if
they were produced in about the past 60 seconds. This field is used a nonce to ensure that no transaction can be
processed more than once by the blockchain.

#### `accounts`

Solana transactions must explicitly list each account that on-chain programs may read or write to. By specifying all
of the accounts up front, Solana validators can process transactions in parallel without fear of two transactions
modifying the same account. It is important that high-throughput applications split up state into multiple accounts
because if each transaction modifies the same account, transactions will have to be processed serially.

Accounts may be annotated as read-write or read-only accounts. If an on-chain program modifies a read-only account,
the transaction will be reverted. The first account will always be read-write since it is used to cover transaction
fees.

Solana's account access list is similar to the optional access list in
[EIP-2930](https://eips.ethereum.org/EIPS/eip-2930).

#### `instructions`

Solana transactions can be thought of as a bundle of Ethereum transactions. Each Solana transaction can include one
or more instructions which each specify an on-chain program address and inputs. There is no explicit limit on the
size of an instruction but note that the total serialized size of a transaction cannot exceed 1232 bytes. The compute
limit is fixed per instruction so each on-chain program should be optimized to use a small amount of compute units or
be split across multiple instructions for expensive operations.

Each instruction specifies the address of the on-chain program, a list of account inputs, and a byte array. Since
Solana on-chain programs don't have their own mutable storage, they must read and store data in separate accounts
which are loaded for the on-chain program when invoked.
