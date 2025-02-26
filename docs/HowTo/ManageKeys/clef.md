---
description: Clef Ethereum account manager
---

# Using Clef

## What is Clef?

`clef` was introduced in Quorum `v2.6.0`.

Clef for GoQuorum is the standard `go-ethereum` `clef` tool, with additional support for GoQuorum-specific
features, including:

* Support for private transactions
* Ability to extend functionality with [`account` plugins](AccountPlugins.md)

`clef` runs as a separate process to `geth` and provides an alternative method of managing accounts
and signing transactions/data.

Instead of `geth` loading and using accounts directly, `geth` delegates account management
responsibilities to `clef`.

!!!important
    Account management will be deprecated within `geth` in the future and replaced with `clef`.

Using `clef` instead of `geth` for account management has several benefits:

* Users and DApps no longer have a dependency on access to a synchronised local node loaded with accounts.
* Transactions and DApp data can instead be signed using `clef`
* Future account-related features will likely only be available in `clef` and not found in `geth`
    (for example, [EIP-191 and EIP-712 have been implemented in `clef`, but there is no intention of implementing them in `geth`](https://github.com/ethereum/go-ethereum/pull/17789/))
* User-experience improvements to ease use and improve security.

## Installing

`geth` and all included tools (`clef`, `bootnode`, …) can be installed to `PATH` by
[building GoQuorum from source with `make all`](../../GetStarted/Install.md).

Verify the installation with:

```bash
clef help
```

## Getting started

See [`cmd/clef/tutorial.md`](https://github.com/ConsenSys/quorum/blob/master/cmd/clef/tutorial.md)
for an overview and step-by-step guide on `clef` initialisation, startup and automation rules configuration.

## Using

`clef` can be used in one of two ways:

1. As an external signer
1. As a `geth` signer

!!! warning
    In the long term, the preferred way of using `clef` will be as an external signer. However, whilst
    waiting for tooling to support the `clef` API, the `go-ethereum` project have included the option
    to use `clef` as a `geth` signer. This ensures existing tooling and user flows can remain unchanged.
    The option to use `clef` as a `geth` signer **will be deprecated** in a future release of `go-ethereum`
    once the migration of account management from `geth` to `clef` is complete.

### As an external signer

Using `clef` as an external signer requires interacting with `clef` through its RPC API. By default
this is exposed over IPC socket. The API can also be exposed over HTTP by using the `--rpcaddr` CLI flag.

!!!example

    An example workflow would be:

    1. Start `clef` and make your accounts available to it
    1. Sign a transaction with the account by using `clef`'s `account_signTransaction` API.  `clef` will return the signed transaction.
    1. Use `eth_sendRawTransaction` or `eth_sendRawPrivateTransaction` to send the signed transaction to a GoQuorum node that does not have your accounts available to it
    1. The GoQuorum node validates the transaction and propagates it through the network for minting.

    === "List accounts"

        ```bash
        echo '{"id": 1, "jsonrpc": "2.0", "method": "account_list"}' | nc -U /path/to/clef.ipc
        ```

    === "Sign data"

        ```bash
        echo '{"id": 1, "jsonrpc": "2.0", "method": "account_signData", "params": ["data/plain", "0x6038dc01869425004ca0b8370f6c81cf464213b3", "0xaaaaaa"]}' | nc -U /path/to/clef.ipc
        ```

### As a geth signer

Using `clef` as a `geth` signer does not require direct interaction through the `clef` API. Instead
`geth` can be used as normal and will automatically delegate to `clef`.

To use `clef` as a `geth` signer:

1. Start `clef`
1. Start `geth` with the `--signer /path/to/clef.ipc` CLI flag

An example workflow would be:

1. Start `clef` and make your accounts available to it
1. Start `geth` and do not make your accounts available to it
1. Use `eth_sendTransaction` to sign and submit a transaction for validation, propagation, and minting

### Extending with account plugins

By default, `clef` manages file-stored `keystore` accounts. Alternative account management options
can be enabled through the use of [`account` plugins](AccountPlugins.md). See the
[Pluggable Architecture Overview](../../Concepts/Plugins/Plugins.md) for more info on using plugins with `clef`.

```bash
clef --plugins file:///path/to/plugin-config.json
```

## More information

More information can be found in the `.md` files in the [`cmd/clef directory`](https://github.com/ConsenSys/quorum/tree/master/cmd/clef).
