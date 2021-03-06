**ledger-wallet /**
[Readme](https://cosmic.plus/#view:js-ledger-wallet)
• [Examples](https://cosmic.plus/#view:js-ledger-wallet/EXAMPLES)
• [Contributing](https://cosmic.plus/#view:js-ledger-wallet/CONTRIBUTING)
• [Changelog](https://cosmic.plus/#view:js-ledger-wallet/CHANGELOG)

# Readme

![Licence](https://img.shields.io/github/license/cosmic-plus/js-ledger-wallet.svg)
[![Dependencies](https://badgen.net/david/dep/cosmic-plus/js-ledger-wallet)](https://david-dm.org/cosmic-plus/js-ledger-wallet)
![Vulnerabilities](https://snyk.io/test/npm/@cosmic-plus/ledger-wallet/badge.svg)
![Bundle](https://badgen.net/badgesize/gzip/cosmic-plus/js-ledger-wallet-web/master/ledger-wallet.js?label=bundle)
![Downloads](https://badgen.net/npm/dt/@cosmic-plus/ledger-wallet)

Easy Ledger Wallet support for Stellar applications and command line.

(Weekly updates: [Reddit](https://reddit.com/r/cosmic_plus),
[Twitter](https://twitter.com/cosmic_plus),
[Keybase](https://keybase.io/team/cosmic_plus),
[Telegram](https://t.me/cosmic_plus))

## Introduction

This library is a convenient wrapper around the official Ledger libraries for
Stellar:

- [Stellar app API](https://www.npmjs.com/package/@ledgerhq/hw-app-str)
- [Transport Node HID](https://www.npmjs.com/package/@ledgerhq/hw-transport-node-hid) - Node.js support
- [Transport U2F](https://www.npmjs.com/package/@ledgerhq/hw-transport-u2f) - Browser support

It provides a way to support Ledger Wallets with a few one-liners:

```js
// Step 1: Connect
await ledgerWallet.connect()

// Step 2: Get public key
const pubkey = ledgerWallet.publicKey

// Step 3: Sign transaction
await ledgerWallet.sign(transaction)

// Extra: Event handlers
ledgerWallet.onConnect = connectionHandler
ledgerWallet.onDisconnect = disconnectionHandler
```

This library is compatible with both Node.js and browser environments.

## Installation

### NPM/Yarn

- NPM: `npm install @cosmic-plus/ledger-wallet`
- Yarn: `yarn add @cosmic-plus/ledger-wallet`

In your script: `const ledgerWallet = require("@cosmic-plus/ledger-wallet")`

### Bower

`bower install cosmic-plus-ledger-wallet`

In your HTML page:

```HTML
<script src="./bower_components/cosmic-plus-ledger-wallet/ledger-wallet.js"></script>
```

### CDN

In your HTML page:

```HTML
<script src="https://cdn.cosmic.plus/ledger-wallet@2.x"></script>
```

_Note:_ For production release it is advised to serve your copy of the library.

## Usage

### Functions

#### await ledgerWallet.connect([account])

Waits for a connection with the Ledger Wallet application for Stellar. If
**account** is not provided, account 1 is used. The library will stop
listening for a connection if `await ledgerWallet.disconnect()` is called.

Once the connection is established, you can use `await ledgerWallet.connect(account)` again at any time to ensure the device is
still connected.

When switching to another account, you can `await ledgerWallet.connect(new_account)` without prior disconnection.

_Note:_ To stay consistent with the way Trezor numbers accounts, **account**
starts at 1 (derivation path: `m/44'/148'/0'`).

| Param     | Type                 | Default | Description                                                                         |
| --------- | -------------------- | ------- | ----------------------------------------------------------------------------------- |
| [account] | `Number` \| `String` | `1`     | Either an account number (starts at 1) or a derivation path (e.g: `m/44'/148'/0'`). |

#### await ledgerWallet.sign(transaction)

Prompts the user to accept **transaction** using the connected account of
their Ledger Wallet.

If the user accepts, adds the signature to **transaction**. Else, throws an
error.

| Param       | Type          | Description              |
| ----------- | ------------- | ------------------------ |
| transaction | `Transaction` | A StellarSdk Transaction |

#### await ledgerWallet.disconnect()

Close the connection with the Ledger device, or stop waiting for one in case
a connection has not been established yet.

#### ledgerWallet.newAccount([horizon])

Connects the first unused account.

_Note:_ merged accounts are considered as used.

| Param     | Type                 | Default                                   | Description                                                                                                       |
| --------- | -------------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| [horizon] | `String` \| `Server` | `&quot;https://horizon.stellar.org&quot;` | The Horizon server where to check for account existence. It can be either an URL or a _StellarSdk.Server_ object. |

#### ledgerWallet.scan([params]) ⇒ `Array`

Scans the ledger device for accounts that exist on **params.horizon**. The
scanning stops after encountering **params.attempts** unused accounts.

Merged accounts are considered as existing accounts and will reset the
**params.attempts** counter when encountered.

Returns an _Array_ of _Objects_ containing `account` number, `publicKey`,
`path`, and `state` (either `"open"` or `"merged"`).

| Param                  | Type                 | Default                                   | Description                                                                                                       |
| ---------------------- | -------------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| [params]               | `Object`             |                                           | Optional parameters.                                                                                              |
| [params.horizon]       | `String` \| `Server` | `&quot;https://horizon.stellar.org&quot;` | The Horizon server where to check for account existence. It can be either an URL or a _StellarSdk.Server_ object. |
| [params.attempts]      | `Number`             | `3`                                       | The number of empty accounts before scanning stops.                                                               |
| [params.includeMerged] | `Boolean`            | `false`                                   | List merged accounts as well.                                                                                     |

#### ledgerWallet.getPublicKeys([start], [length]) ⇒ `Array`

Request multiple public keys from the Ledger device. The request will return
**length** accounts, starting by **start** (minimum 1).

Returns an _Array_ of _Objects_ with properties `account`, `publicKey`, and
`path`.

| Param    | Type     | Default | Description                     |
| -------- | -------- | ------- | ------------------------------- |
| [start]  | `Number` | `1`     | Starting account number         |
| [length] | `Number` | `1`     | Number of account to be listed. |

### Events

#### ledgerWallet.onConnect : `function`

_Function_ to execute on each connection.

#### ledgerWallet.onDisconnect : `function`

_Function_ to execute on each disconnection.

### Members

#### ledgerWallet.publicKey : `String`

Public key of the connected account.

#### ledgerWallet.path : `String`

Derivation path of the connected account (default: `m/44'/148'/0'`).

#### ledgerWallet.version : `String`

Version of the Stellar application installed on the connected device.

#### ledgerWallet.multiOpsEnabled : `Boolean`

Whether or not the user accepts to sign transactions without checking them on
the device first. This allows to sign long transactions (10+ ops) that could
normally not be handled by the device memory, but this is insecure.

#### ledgerWallet.transport : `Transport`

The Ledger Transport instance. (internal component)

#### ledgerWallet.application : `StellarApp`

The Ledger Stellar application instance. (internal component)

## Links

**Organization:** [Cosmic.plus](https://cosmic.plus/) | [@GitHub](https://git.cosmic.plus) | [@NPM](https://www.npmjs.com/search?q=cosmic-plus)

**Follow:** [Reddit](https://reddit.com/r/cosmic_plus) | [Twitter](https://twitter.com/cosmic_plus) | [Medium](https://medium.com/cosmic-plus) | [Codepen](https://codepen.io/cosmic-plus)

**Talk:** [Telegram](https://t.me/cosmic_plus) | [Keybase](https://keybase.io/team/cosmic_plus)
