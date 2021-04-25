---
title: "Accounts"
date: 2021-04-25
weight: 2
description: >
  Major differences between Ethereum and Solana accounts
---

On Solana, programs have state, but that state must be stored inside separate accounts. In order to differentiate
which account is used as storage for which program, each account has an `owner` field. The Solana VM restricts most
account modifications to the owner of the account but it freely allows any program to *read* storage from an account
that it doesn't own. This contrasts with the EVM where a smart contract cannot read the storage of another contract,
the contract must expose a public api for reading storage information that may be useful.

To be continued...
