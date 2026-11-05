# TON Security Bug Bounty

Only critical vulnerabilities in the components listed below are eligible.

Rewards: up to **$100,000 per vulnerability**, paid in **Grams or USDt**, depending on severity. Large rewards may be subject to lock-up or vesting.

Submit a vulnerability description and exploitation scenario to [@ton_bugs_bot](https://t.me/ton_bugs_bot). We reserve the right to decline review.

Most reports are processed within 90 days; LLM-generated spam may cause delays. For duplicates, only the earliest received report is considered.

Report validation workflow: [TON Bug Bounty Self Check](skills/bug-bounty-self-check.md).

## Scope

### 1. TON Blockchain Core (C++)

[Repository](https://github.com/ton-blockchain/ton): `master` branch only, latest `global_version` only.

Covered components: **✅ Validator Node, ✅ Full Node, ✅ Simplex Consensus**.

Eligible impacts:

- ✅ Unauthorized manipulation of blockchain state or funds.
- ✅ Network halts.
- ✅ Node halts triggered by a single request.

All other components and impacts are not considered.

The absence of `MisbehaviorProof` in Simplex is known and is a low-priority work in progress. It is not critical to current operation and is planned as defense in depth. Reports about `MisbehaviorProof` are excluded.

#### Excluded issues

- ❌ All OOM and DDoS attacks.
- ❌ Any attack initiated by a validator.
- ❌ Attacks requiring prior control of the local host, local files or libraries, runtime parameters, environment variables, startup flags, or other trusted operator inputs.
- ❌ Attacks requiring previously leaked private information, including private keys for ADNL, wallets, block signing, the control interface, or FullNodeMaster.
- ❌ Attacks requiring prior control of a majority of otherwise honest validators, including malformed blocks signed by a quorum or invalid network configuration approved by validator voting. `CHECK()` failures and node crashes under a faulty quorum are intentional: halting is preferable to continued operation.
- ❌ Issues that assume an extra-currency exists.
- ❌ Sybil attacks against the public overlay.
- ❌ Crashes or unexpected behavior in local tools, debug-only paths, or user-side workflows that do not affect normal node, service, or blockchain operation.
- ❌ Future timestamps exceeding their integer type's range, including the 2038 and 2106 problems.
- ❌ Issues involving unsynchronized lite servers.
- ❌ Validator control-interface misconfiguration.

#### Excluded components

- ❌ Catchain 1.0.
- ❌ Tonlib; 
- ❌ RLDP 1/2.
- ❌ DHT Node.
- ❌ lite-client.
- ❌ Libemulator.
- ❌ Fift/FunC/Tolk compilers.
- ❌ Blockchain explorer.
- ❌ TON Storage, Payment Channels, TON Proxy, https-proxy, rldp-http-proxy.

### 2. MyTonCtrl

[Repository](https://github.com/ton-blockchain/mytonctrl).

✅ Only vulnerabilities causing loss of funds or unauthorized access to the validator server are eligible.

### 3. TON Standard Smart Contracts

✅ Only vulnerabilities that lead to unauthorized control or ownership and unauthorized manipulation of funds are considered.

- ✅ [Elector](https://github.com/ton-blockchain/ton/blob/master/crypto/smartcont/elector-code.fc).
- ✅ [Config](https://github.com/ton-blockchain/ton/blob/master/crypto/smartcont/config-code.fc).
- ✅ [Wallet v1](https://github.com/ton-blockchain/ton/blob/master/crypto/smartcont/simple-wallet-code.fc).
- ✅ [Wallet v3](https://github.com/ton-blockchain/ton/blob/master/crypto/smartcont/wallet3-code.fc).
- ✅ [Wallet v4r2](https://github.com/ton-blockchain/wallet-contract). ❌ Wallet plugins are excluded.
- ✅ [Wallet v5](https://github.com/ton-blockchain/wallet-contract-v5).
- ✅ [tg-wallet-v1](https://github.com/ton-blockchain/tg-wallet-contract).
- ✅ [Multisig V2](https://github.com/ton-blockchain/multisig-contract-v2).
- ✅ [Highload V3](https://github.com/ton-blockchain/highload-wallet-contract-v3).
- ✅ [Single Nominator 1.0 and 1.1](https://github.com/ton-blockchain/single-nominator).
- ✅ [Nominator Pool V2](https://github.com/ton-blockchain/nominator-pool-v2).
- ✅ Standard Jettons: [v2.1-tolk](https://github.com/ton-blockchain/acton-contracts/tree/master/jetton-v2.1), [v2.0-func](https://github.com/ton-blockchain/jetton-contract/tree/jetton-2.0), [v1.2-func](https://github.com/ton-blockchain/jetton-contract).
- ✅ Standard NFTs: [v1.1-func](https://github.com/ton-blockchain/nft-contract), [v1.1-tolk](https://github.com/ton-blockchain/acton-contracts/tree/master/nft-v1.1).
- ✅ [TON DNS](https://github.com/ton-blockchain/dns-contract).

❌ All other smart contracts are excluded.

#### Contract-specific notes

**Elector:** Known peculiarities of Elector are not bounty issues by themselves, including `max_stake`-related stake accounting/recovery behavior and ignoring bounce messages semantics. Also reports for elector-code issues should take into account that `election_id` is different each round, thus signatures for slashing fines can not be replayed.

**Config:** Known peculiarities of Config contract is that if validator voted positively for some proposal his vote (under specific conditions) can be replayed in different rounds without active validator participation.

**Wallets:** Deleted-wallet redeploy/reuse behavior by itself is not considered a vulnerability; wallet deletion should be treated as final, and old externals are expected to expire via short `valid_until`.

**Nominator Pool V2:** The reports below are not considered bounty issues:

- [#23](https://github.com/ton-blockchain/nominator-pool-v2/issues/23): Third-party stake recovery via `RecoverStakeUnrestricted`.
- [#24](https://github.com/ton-blockchain/nominator-pool-v2/issues/24): Unhandled `RecoverStakeError` paths causing round-rotation deadlock and permanent freezing of nominator funds.
- [#25](https://github.com/ton-blockchain/nominator-pool-v2/issues/25): Missing zero-divisor check in `mulDivQuiet`.
- [#26](https://github.com/ton-blockchain/nominator-pool-v2/issues/26): Pool-deposit DoS caused by the dictionary depth limit (`MAX_NM_DEPTH = 15`) in `contracts/storage.tolk`.
- [#27](https://github.com/ton-blockchain/nominator-pool-v2/issues/27): Unhandled division by zero in `DepositMessage` when `nominatorsAmount == 0`, in `contracts/utils.tolk`.
- [#28](https://github.com/ton-blockchain/nominator-pool-v2/issues/28).
- [#29](https://github.com/ton-blockchain/nominator-pool-v2/issues/29): Unhandled integer underflow in `get_nominator_data` causing an off-chain getter crash.

## Out of Scope

- ❌ Websites and frontends.
- ❌ SDKs and developer tools.
- ❌ Toncenter API.

## Third-Party Products

❌ Third-party TON products are not covered. Many have separate bug bounty programs; check their websites for details.
