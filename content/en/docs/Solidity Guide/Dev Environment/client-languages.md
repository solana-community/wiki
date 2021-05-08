---
title: "Client Languages"
weight: 3
description: >
    Supported languages for Solana application client development.
---

## Rust

Solana provides a Rust SDK for creating bots and tools.

- [`solana-sdk`][solana-sdk] is used to create Transactions
- [`solana-client`][solana-client] is used to interact with a Solana cluster
- [`solana-cli-config`][solana-cli-config] is used to create CLI tools

## TypeScript / JavaScript

Solana provides a web3 SDK for writing browser applications in TypeScript
and JavaScript. Check out the [documentation][web3] for how to use it.

- [`@solana/web3.js`][web3] is used to create transactions and connect to a Solana cluster
- [`@project-serum/anchor`][anchor-js] is used to interact with Solana programs built with [Anchor]

## Golang

Community created Go SDK: https://github.com/portto/solana-go-sdk

## Swift

Community created Swift SDK: https://github.com/crewshin/solana-swift 

## Python

Community created Python SDK: https://michaelhly.github.io/solana-py/

[Anchor]: https://project-serum.github.io/anchor
[solana-client]: https://docs.rs/solana-client/latest/solana_client/
[solana-cli-config]: https://docs.rs/solana-cli-config/latest/solana_cli_config/
[solana-sdk]: https://docs.rs/solana-sdk/latest/solana_sdk/
[web3]: https://solana-labs.github.io/solana-web3.js/
[anchor-js]: http://npmjs.com/package/@project-serum/anchor