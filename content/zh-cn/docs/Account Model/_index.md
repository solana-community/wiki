---
title: "Account Model"
description: >
  A high-level overview of the account model in Solana's runtime called Sealevel
---

In account based chains like Ethereum and Solana, arbitrary state can be stored on-chain
to create complex and powerful decentralized applications. However, one of the major
differences between the EVM and Sealevel is how that state is actually stored. On Ethereum,
only smart contracts have storage and naturally they have full control over that storage.
On Solana, *any* account can store state but the storage for smart contracts is
only used to store the immutable byte code. On Solana, the state of a smart contract is
actually completely stored in other accounts. To ensure that contracts can't
modify another contract's state, each account assigns an owner contract which has exclusive
control over state mutations.

To visualize this difference here's what the storage a token contract would look like on
either platform.

On Ethereum, token contracts typically have a `mapping` which defines the balance for each
owner address:

```sol
mapping (address => uint256) private _balances;
```

| Token Contract | Storage                   | Owner Address | Tokens  |
|----------------|---------------------------|---------------|----------|
| [`0xa0b869…`] (USDC)  | `mapping` in [`0xa0b869…`] | [`0x47ac0f…`]  |  USDC   |
| [`0xdac17f…`] (USDT)  | `mapping` in [`0xdac17f…`] | [`0x47ac0f…`]  | USDT  |
| [`0xdac17f…`] (USDT)  | `mapping` in [`0xdac17f…`] | `0xabd99e…`  | USDT |

[`0xa0b869…`]: https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48
[`0xdac17f…`]: https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7
[`0x47ac0f…`]: https://etherscan.io/address/0x47ac0fb4f2d84898e4d9e7b4dab3c24507a6d503

On Solana, token balances are typically stored in unique accounts where the storage account
address is derived from the address of the owner account.

| Token Contract        | Storage Address       | Owner Address   | Balance    |
|-----------------------|----------------|-----------------|------------|
| [`EPjFWdd5…`] (USDC) | [`Bp9sFfdP…`] | [`JBpj7yp4…`] | ~ 1B USDC  |
| [`EPjFWdd5…`] (USDC) | [`8t7vxGWe…`] | [`5coBYaaD…`]  | ~ 23M USDC |
| [`Es9vMFrz…`] (USDT) | [`4QbFwKK2…`] | [`5coBYaaD…`]  | ~ 17M USDT |

[`EPjFWdd5…`]: https://explorer.solana.com/address/EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
[`Es9vMFrz…`]: https://explorer.solana.com/address/Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB

[`JBpj7yp4…`]: https://explorer.solana.com/address/JBpj7yp4Afvb71TmanVwJZXGeX4kqbGFvjCFCRo3EbTM
[`Bp9sFfdP…`]: https://explorer.solana.com/address/Bp9sFfdPW45c8dTzoPX9tnaHDR6xNbPX8u4SSt9PAdeR

[`5coBYaaD…`]: https://explorer.solana.com/address/5coBYaaDYd9xkMhDPDGcV2Batu51N987Um1jcrE122AY
[`4QbFwKK2…`]: https://explorer.solana.com/address/4QbFwKK2iRSmuWYyjYuJbA7s6QAgswErEPndrvrC2kzp
[`8t7vxGWe…`]: https://explorer.solana.com/address/8t7vxGWe18TPv4jKNpq9yysszaz8ZFVy1rXWrTcCMLVZ

In Ethereum's EVM, there are two types of accounts. Basic accounts which simply store
a balance of wei, and code accounts which form the basis for on-chain smart contracts.
Each code account, in addition to storing EVM code, all have an associated storage map
which can be used to read and write arbitrary data. The EVM provides instructions
for each contract to read and write to its own storage but it's impossible to read
from other contract's storage.

In Solana's Sealevel, there are also two types of accounts: executable and non-executable
accounts. Unlike the EVM, both of these accounts can store data. Executable accounts
are immutable in Sealevel and can either store their own executable byte code or a
proxy address of an account which stores mutable executable byte code. Since executable
accounts are immutable, their application state must be stored in non-executable accounts.
In the EVM, contracts can only read and write their own storage. In Sealevel, any account's
data can be read or written to by a contract. However, the runtime enforces that *only*
an account's "owner" is allowed to modify it. Changes by any other programs will be reverted
and cause the transaction to fail.

Let's take a closer look at what is actually stored in an account on each platform.

In the EVM, a "basic" account is very simple. It holds a nonce which is incremented each time
this account sends a transaction as well as a balance field which tracks the remaining wei
held by the account. The nonce field has a very important purpose. It is what prevents
any transaction from being processed twice by the EVM. This is because each transaction
specifies the nonce of the sender and this nonce *must* match the sender's nonce in the
account store to be executed. Since nonce's are incremented after each transaction,
it's impossible to run the same transaction twice. If it weren't for this `nonce` concept,
transactions could be "replayed" by processing them more than once which is often a very
undesirable outcome for users.

| Field     | Description                                        |
|-----------|----------------------------------------------------|
| `nonce`   | The number of transactions sent from this account. |
| `balance` | The number of Wei owned by this account.           |

In the EVM, "code accounts" are where all the action is. Since code accounts cannot
be used for sending transactions, the `nonce` field represents the number of contracts
this account has created. Similar to basic accounts, code accounts can hold wei and use
an EVM instruction to send that wei to other accounts. Code accounts also store an
immutable hash of the associated EVM byte code as well as a hash which tracks changes
to all data in storage. The actual EVM byte code and storage data is stored separately
from the account store but often cached locally for quick access.

| Field         | Description                                                                                           |
|---------------|-------------------------------------------------------------------------------------------------------|
| `nonce`       | The number of contract-creations made by this account.                                                |
| `balance`     | The amount of Wei owned by this account.                                                              |
| `codeHash`    | The hash of the immutable EVM code of this account.                                                   |
| `storageRoot` | The 256-bit hash of the root node of a merkle tree that encodes the storage contents of this account. |

In Sealevel, the main similarity to EVM is the `lamports` field which tracks the balance
of each account. There is a notable lack of anything like EVM's `nonce` field. This is because
nonces are handled differently on Solana [TODO](Discuss elsewhere). The key field to take
note of in Sealevel accounts is the `owner` field. This field stores the address of an
on-chain program and represents *which* on-chain program is allowed to *write* to the
account's data and subtract from its lamport balance. The concept of program-owned accounts
roughly maps to account-specific storage maps in the EVM. However, it comes with added
flexibility of allowing any on-chain program to read the data from accounts it doesn't own.

| Field        | Description                                     |
|--------------|-------------------------------------------------|
| `lamports`   | The number of lamports owned by this account.   |
| `owner`      | The program owner of this account.              |
| `executable` | Whether this account can process instructions. |
| `data`       | The raw data byte array stored by this account. |
| `rent_epoch` | The next epoch that this account will owe rent. |

## Account Storage

In the EVM, only "code accounts" have storage. This storage is implemented as a map with a
256 bit key space where each key maps to a 256 bit value. For non-code accounts,
the `storageRoot` is set to a special "null" hash which indicates the account has no storage.

In the Solana Sealevel VM, *all* accounts can store data. However, executable account data is
used exclusively for immutable byte code which is used to process transactions. So where can
smart contract developers store their data? They can store the data in non-executable accounts
which are *owned* by the executable account. Developers can create new accounts with an
assigned owner equal to the address of their executable account to store data.

## Account Signing Authority

Question: Who actually is allowed to create the accounts needed for storing program state?

1) Solana accounts can only be assigned to a program if the account's signing authority approves
the change. Typically, the signing authority just means that the corresponding private key
must sign the transaction.

Question: What happens when a program wishes to create an account?

2) Since program execution state is entirely public and known to every validator, there's no
way for it to secretly sign a message to create an account. To allow account creation by programs,
the Sealevel runtime provides a syscall which allows a program to derive an address from its own
address which the program can freely claim to sign.

Question: How to create multiple accounts in the same transaction if each one requires a signature?

Answer: Solana transactions specify a list of signatures and contain as many signatures as can
fit in a 1232 byte blob. Each of these signatures must pass verification or the transaction will be
rejected. Each signature increases the fee as well.

Question: What are signatures used for?

Answer: System-owned account must sign most system instructions. This includes assigning the account
to a new program, allocating storage, and transferring lamports.

Question: What's the equivalent in the EVM?

Ethereum transactions have a field for exactly one signature which must be verified against the address
which sent the message. Any additional signatures must be passed in transaction binary data and verified
using the `ecrecover` crypto function which executes natively using EVM precompiles.

Question: Can the Solana VM verify secpk2561 instructions from Ethereum?

Answer: Yes, it was added to support the Wormhole Ethereum / Solana bridge

## Account Owner

Every account in Sealevel has a specified owner. Since accounts can be created by simply
receiving lamports, each account must be assigned a default owner when created. The
default owner in Sealevel is called the "System Program". The System Program is
primarily responsible for account creation and lamport transfers.

Account types

| Name           | Owner         | Description                                                                                                          |
|----------------|---------------|----------------------------------------------------------------------------------------------------------------------|
| Sysvar         | Sysvar        | An account used for loading blockchain state like the latest block and current rent fees                             |
| Native Program | Native Loader | An account used to indicate native programs like the System, Stake, and Vote programs which do not use BPF byte code |
| BPF Program    | BPF Loader    | An account used for processing BPF byte code                                                                         |

## Sealevel Runtime Account Rules

### Immutability

1. Executable accounts are fully immutable.

The above rule takes precedence over all following rules. Meaning that programs
cannot add lamports to executable accounts and their data can never be modified
or deleted.

### Data Allocation

1. Only the System Program is allowed to change the size of account data.
2. Newly allocated account data is always zeroed out.
2. Account data size cannot be decreased.

At the time of writing, programs cannot increase the data size of accounts they own.
They must copy data from one account to a larger account if they need more data. For this
reason, most programs do not store dynamically sized maps and arrays in account data.
Instead, they store this data in many accounts. For example, each key-value pair of an
EVM mapping can be stored in a new account.

### Data

1. Only the owner of an account may modify its data.
2. Accounts may only be assigned a new owner if their data is zeroed out.

The rules above guarantee that a program can always fully trust the data
stored in an account that it owns. The data is either zeroed out or previously
modified by the program. These guarantees work together to form the same
trust guarantees as the EVM's account storage mechanism.

In Sealevel, executable byte code is stored in account data unlike the EVM which
stores code in a separate data store. 

### Balance

1. Only the owner of an account may subtract its lamports 
2. Any program account may add lamports to an account

This means that once an account is owned by a program, the private key
cannot be used to transfer lamports with the System Program since the
System Program no longer has permission to send lamports from the account.

### Ownership

1. Only the owner of an account may assign a new owner

Since all accounts are owned by the System Program by default, the System Program
is most often used to assign accounts to other programs.

### Rent

1. Rent fees are charged every epoch (~2 days) and are determined by account size
2. Accounts with sufficient balance to cover 2 years of rent are exempt from fees.

Because rent fees can slowly drain an account's balance, programs must consider whether
to enforce that accounts they use for storage should be required to be rent-exempt.
If accounts are not required to be rent-exempt, they may eventually run out of lamports
and be deleted by the runtime. Once deleted, accounts can be recreated. For this reason,
accounts which rely on certain data storage to be present should enforce that accounts
are exempt from rent before writing data.

### Zero Balance

1. Accounts with zero balance will be deleted at the end of transaction processing.
2. Temporary accounts with zero balance may be created during a transaction.

Programs which close accounts should consider that account data is not deleted until
a transaction has been fully processed. Simply subtracting lamports to a zero balance
is not sufficient to delete an account. This means that if a program is called from
another program, it may be called again with the same accounts which have not yet
been deleted.

### New Executable Accounts

1. Only designated loader programs may change account executable status

In Sealevel, executable accounts are created just like normal accounts but
their owner must be set to a loader program. The loader program processes
transactions to write byte code into account data and only once the program
passes the loader's verification process, will it be marked as executable.

Since executable accounts are immutable, any lamports in the account will be
frozen when the account is marked executable. The lamport balance should
therefore be no more than the minimum balance required for rent-free storage.
