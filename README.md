# TON security bug bounty

If you find a critical bug or vulnerability in the TON Blockchain (in the C++ code of the main repository) or TON main services (standard wallets, bridge, standard smart contracts), you can send its description and exploitation scenario and receive a reward.

We are interested in critical vulnerabilities: crash, loss/theft of coins, etc.

You can target a reward of up to $100,000 in Toncoins for vulnerability, depending on the severity.

Let's perfect the safety and security of TON together!

Send reports to [@ton_bugs_bot](https://t.me/ton_bugs_bot).

_We reserve the right not to review some reports._

Priority list:

## TON Blockchain Core (C++)

https://github.com/ton-blockchain/ton.

Catchain, Validator Node, Full Node, DHT Node, TonLib, FunC compiler, Fift compiler.

## Smart Contracts (FunC)

Standard smart contracts - https://github.com/ton-blockchain/ton/tree/master/crypto/smartcont:

- Network config - `config-code.fc`;

- Elector - `elector-code.fc`;

- Multisig - `multisig-code.fc`;

- Wallets - `simple-wallet-code.fc`, `wallet3-code.fc`;

Wallet V4 and subscription smart contracts - https://github.com/ton-blockchain/wallet-contract. 

TON-Ethereum bridge and TON-BSC bridge - https://github.com/ton-blockchain/bridge-func. 

Nominator pool - https://github.com/ton-blockchain/nominator-pool.

Fungible, Non-Fungible, Semi-Fungible tokens - https://github.com/ton-blockchain/token-contract. 

## Solidity 

TON-Ethereum bridge and TON-BSC bridge - https://github.com/ton-blockchain/bridge-solidity.

## Python

MyTonCtrl validator tools - https://github.com/ton-blockchain/mytonctrl.

HTTP API - https://github.com/toncenter/ton-http-api, https://toncenter.com.

Python SDK - https://github.com/toncenter/pytonlib.

## Frontend

Note that the standard recommendations from the OWASP (e.g. adding the recommended HTTP headers) will not be highly appreciated or will rejected.

The most valuable are real vulnerabilities and critical bugs.

Web Site - https://ton.org.

[Web Wallet](https://wallet.ton.org) and [Browser Extension](https://chrome.google.com/webstore/detail/ton-wallet/nphplpgoakhhjchkkhmiggakijnkhfnd) - https://github.com/toncenter/ton-wallet, 

JavaScript SDK - https://github.com/toncenter/tonweb.

JavaScript Mnemonic SDK - https://github.com/toncenter/tonweb-mnemonic. 

TON-EVM bridge [frontend](https://ton.org/bridge) - https://github.com/ton-blockchain/bridge.

## Third-party

Third-party TON services that you can find for example on https://ton.app are also interested in finding security bugs.

In particular, popular products like https://tonkeeper.com, [@wallet](https://t.me/wallet), [@cryptobot](https://t.me/cryptobot), [@donate](https://t.me/donate), https://tonwhales.com, https://getgems.io,  https://disintar.io/, https://tonscan.org.

Please contact them directly if you find a vulnerability.
