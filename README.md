# TON security bug bounty

If you find a critical bug or vulnerability in the TON Blockchain (in the C++ code of the main repository) or TON main services (standard wallets, standard smart contracts), you can send its description and exploitation scenario and receive a reward.

We are interested in critical vulnerabilities: crash, loss/theft of coins, etc.

You can target a reward of up to $100,000 in Toncoins for vulnerability, depending on the severity; large bounties for severe vulnerabilities are given with 1 year lock-up.

Let's perfect the safety and security of TON together!

Send general reports to [@ton_bugs_bot](https://t.me/ton_bugs_bot). Reports about frontend should be sent to [hackenproof page](https://hackenproof.com/programs/ton-society) (check Frontend section below).

_We reserve the right not to review some reports._

## Out of scope / not accepted cases

The following are generally out of scope or will not be accepted without a clear escalation to a real security impact on normal network/service operation:

- Issues that require the attacker to already control the local host, local files/libraries, runtime parameters, environment variables, startup flags, or other trusted operator inputs.
- Client-side SDK misuse by the integrating application, including loading attacker-controlled local code/data, when no privilege boundary is crossed.
- Reports against components not listed below, or against stubs / preliminary-testing implementations / deprecated components not intended for production use.
- Input-validation, error-message, or status-code hygiene issues (for example 5xx vs 4xx/405) when the service remains healthy.
- Crashes or odd behavior in local tools, debug-only paths, or other user-side workflows that do not affect normal node/service/blockchain operation.
- Behavior with no realistic manifestation or exploitation path in normal network operation.
- Long-known implementation or design peculiarities, unless you demonstrate a new security impact.

Priority list:

## TON Blockchain Core (C++)

https://github.com/ton-blockchain/ton.

Simplex, ~Catchain,~ Validator Node, Full Node, DHT Node, TonLib, ~FunC compiler, Fift compiler~ **(issues in **FunC** and **Fift** that do not cause critical problems during normal node operation are no longer in scope for this bug bounty)**. 

The testnet branch is considered experimental and often undergoes tests and audits. We are interested in identifying problems at this stage, thus bugs in the testnet branch are accepted, in some cases report review maybe postponed till the end of audit/tests (you will be immediately notified upon report submission).

*Extracurrency* related bugs are considered but has low priority in terms of processing speed and bounty rewards, due to not being practically deployed on mainnet (with not firm plans about them).

## Smart Contracts (FunC)

Standard smart contracts - https://github.com/ton-blockchain/ton/tree/master/crypto/smartcont:

- Network config - `config-code.fc`;

- Elector - `elector-code.fc`;
  - Known peculiarities of Elector are not bounty issues by themselves, including `max_stake`-related stake accounting/recovery behavior and recover-stake bounce semantics.

- Wallets - `simple-wallet-code.fc`, `wallet3-code.fc`;
  - Deleted-wallet redeploy/reuse behavior by itself is not considered a vulnerability; wallet deletion should be treated as final, and old externals are expected to expire via short `valid_until`.

Wallet V4 and subscription smart contracts - https://github.com/ton-blockchain/wallet-contract. 

Multisig - https://github.com/ton-blockchain/multisig-contract

Nominator pool - https://github.com/ton-blockchain/nominator-pool.

Fungible, Non-Fungible, Semi-Fungible tokens - https://github.com/ton-blockchain/token-contract. 

TON DNS - https://github.com/ton-blockchain/dns-contract

## Python

MyTonCtrl validator tools - https://github.com/ton-blockchain/mytonctrl.

HTTP API - https://github.com/toncenter/ton-http-api, https://toncenter.com.

Python SDK - https://github.com/toncenter/pytonlib.

## Frontend

All issues related to sites and frontend should be sent to [hackenproof page](https://hackenproof.com/programs/ton-society).

Note that the standard recommendations from the OWASP (e.g. adding the recommended HTTP headers) will not be highly appreciated or will rejected.

The most valuable are real vulnerabilities and critical bugs.

We do not accept reports about open IP addresses or open ports, if this does not lead to an attack (ddos is not considered in this case).

Web Site - https://ton.org.

[Web Wallet](https://wallet.ton.org) and [Browser Extension](https://chrome.google.com/webstore/detail/ton-wallet/nphplpgoakhhjchkkhmiggakijnkhfnd) - https://github.com/toncenter/ton-wallet, 

JavaScript SDK - https://github.com/toncenter/tonweb.

JavaScript Mnemonic SDK - https://github.com/toncenter/tonweb-mnemonic. 

## Third-party

Third-party TON services that you can find for example on https://ton.app are also interested in finding security bugs.

In particular, popular products like https://tonkeeper.com, [@wallet](https://t.me/wallet), [@cryptobot](https://t.me/cryptobot), [@donate](https://t.me/donate), https://tonwhales.com, https://getgems.io, https://dton.io, https://tonscan.org.

Please contact them directly if you find a vulnerability.
