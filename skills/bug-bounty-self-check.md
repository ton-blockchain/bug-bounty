---
name: ton-bug-bounty-self-check
description: Self-check TON security bug bounty reports before submission. Use when a researcher provides a TON vulnerability report, PoC, crash log, stack trace, repro directory, or asks whether a TON bug bounty report is ready to send. Do not use for frontend reports except to redirect to HackenProof.
---

# TON Bug Bounty Self Check

## Purpose

Use this skill to help security researchers validate TON bug bounty reports before submission.

This is a self-check workflow, not an official TON triage decision and not a payout guarantee. The goal is to reduce invalid and low-quality submissions, verify claims against current TON source code and current bug bounty rules, detect common mistakes, and produce an evidence-based readiness assessment.

The agent MUST generate only:

- `self-check-report.md`
- a short text assessment

The agent MUST NOT rewrite or edit the original bug report unless the user explicitly asks for a separate rewrite.

When the report is incomplete or not ready to submit, the short text assessment MUST say `DO NOT SEND!!!`.

## Inputs

The user may provide:

- one report file
- a directory containing a report, PoC files, logs, stack traces, scripts, screenshots, or patches
- a report pasted into chat or stdin

If a directory is provided, inspect all relevant files in that directory. If a single report file is provided, write `self-check-report.md` next to that file unless the user gives another output path. If the input is a directory, write the file into that directory root. If the input is chat/stdin text, write `self-check-report.md` into the current working root.

## Mandatory Sources

Always fetch current bug bounty rules and current source code before analysis. If fetching current sources or rules fails, continue with best effort, record the failure, and downgrade confidence.

### Rules source

- `https://github.com/ton-blockchain/bug-bounty`

### In-scope source repositories for this self-check

Use the default branch of each repository, usually `master` or `main`, unless the report explicitly targets the TON `testnet` branch.

- TON core: `https://github.com/ton-blockchain/ton`
- TON core testnet branch: use when the report explicitly targets testnet
- Standard smart contracts in TON core: `https://github.com/ton-blockchain/ton/tree/master/crypto/smartcont`
- Wallet V4 and subscription smart contracts: `https://github.com/ton-blockchain/wallet-contract`
- Multisig V2: `https://github.com/ton-blockchain/multisig-contract-v2`
- Nominator pool: `https://github.com/ton-blockchain/nominator-pool`
- Fungible, non-fungible, and semi-fungible tokens: `https://github.com/ton-blockchain/token-contract`
- TON DNS: `https://github.com/ton-blockchain/dns-contract`
- MyTonCtrl validator tools: `https://github.com/ton-blockchain/mytonctrl`
- Toncenter HTTP API: `https://github.com/toncenter/ton-http-api`
- Toncenter indexer: `https://github.com/toncenter/ton-indexer`
- Pytonlib: `https://github.com/toncenter/pytonlib`

### Related skill for validator and consensus reproduction

For reports claiming consensus stop, validator-to-validator crash, any-node-to-any-node crash, or validator-to-other-node crash, require reproduction with the TON local-network triage workflow when possible:

- `https://github.com/ton-blockchain/ton-triage-skill`

If that workflow is unavailable and the report does not already include convincing `ton-bug-triage` artifacts, the status MUST NOT be `correct`.

### Explicitly excluded or redirected sources

- Frontend reports MUST be redirected to HackenProof. Do not validate them in this flow except to explain the redirect.
- Third-party TON services MUST be redirected to their owners. Do not validate them as TON bug bounty submissions.
- Catchain is out of scope for this self-check.
- FunC and Fift compiler reports are in scope only if the researcher demonstrates critical impact during normal node operation.
- Multisig V1 and Highload wallet V1 and V2
  - crypto/smartcont/highload-wallet.fif
  - crypto/smartcont/highload-wallet-code.fc
  - crypto/smartcont/highload-wallet-v2.fif
  - crypto/smartcont/highload-wallet-v2-code.fc
  - crypto/smartcont/highload-wallet-v2-one.fif
  - crypto/smartcont/multisig-code.fc

## Repository Freshness Requirements

For every relevant repository:

1. Clone or update the repository before analysis.
2. Fetch the latest state of the default branch, usually `master` or `main`.
3. If the report explicitly targets TON testnet, fetch and analyze the `testnet` branch for `ton-blockchain/ton`.
4. Initialize and update submodules recursively where present.
5. Record the repository URL, branch, and commit hash.
6. Record the bug bounty rules repository commit hash.
7. If the local working tree contains modifications, continue but add a warning. Do not treat local modifications as upstream evidence.
8. Signed commit or tag verification is not required.
9. A clean working tree is not required.
10. Building the project is not mandatory. If the project does not build or dependencies are unavailable, continue static analysis and record the limitation.

If the issue only affects an older commit and is fixed in the latest relevant branch, set the final verdict to `REJECTED: already fixed`.

## External Interaction Rules

The agent MUST NOT test or exploit the reported vulnerability against live systems, including:

- mainnet
- testnet
- Toncenter live services
- real deployed contracts
- real third-party services

Allowed read-only external checks:

- current public network configuration, for example `http://tonscan.org/config`
- public blockchain explorers for passive confirmation
- GitHub repositories and public documentation

Forbidden actions:

- sending exploit payloads to mainnet or testnet
- sending exploit payloads to Toncenter live endpoints
- using `lite-client` against external mainnet or testnet nodes
- executing a PoC that targets public RPC endpoints, public lite servers, real addresses, or real deployed contracts
- making state-changing calls against real contracts or services

Allowed local checks:

- local TON builds or source trees
- local `tontester`
- local `ton-bug-triage` workflow
- local TVM or smart contract emulator
- local copied on-chain state
- local `validator-engine`, `lite-client`, `func`, `fift`, Docker images, or similar TON tooling
- local Toncenter HTTP API or indexer instance with local test data

Run researcher-provided PoC code only when the report cannot reasonably be evaluated without it. If a PoC includes public RPC endpoints, mainnet/testnet addresses, or real service targets, do not run it. Record that it was not run and why.

Run fuzzers only when the report cannot reasonably be evaluated without them. Prefer deterministic reproduction steps and static verification.

## Self-Check Procedure

### 1. Identify the target and input evidence

Determine:

- target component
- repository or repositories
- branch, if specified
- affected commit, if specified
- claimed vulnerable files and functions
- report language
- provided PoC, logs, stack traces, scripts, patches, or screenshots

If the report does not specify an affected commit, use the latest analyzed commit and mark the original report as missing the affected commit.

### 2. Load current rules and scope

Fetch the current bug bounty rules and check:

- whether the target component is in scope
- whether the issue category is eligible
- whether the report should instead go to HackenProof or a third-party owner
- whether any explicit exclusion applies
- whether the issue is low priority, for example extracurrency-related bugs

Keep technical validity separate from bug bounty eligibility. A finding can be technically valid and still out of scope.

### 3. Locate and verify vulnerable code

The agent MUST:

- locate the exact relevant code path
- identify affected files and functions
- verify the issue against the latest relevant source branch
- check whether the issue is already fixed
- verify that assumptions are realistic
- check whether real conditions can lead to the bug manifestation
- distinguish verified facts from assumptions

For parser, decoder, TL-B, network, or malformed input claims, the report MUST show a path from attacker-controlled untrusted input to the affected code. Generic local parser crashes without a realistic network/service path are not enough.

“Defense-in-depth” issues are generally out of scope. For example, it is not a valid finding if a variable is not checked for `nullptr` because it has already been validated at another layer, or if code assumes that an item exists in a list (for instance sender in overlay peers) because earlier control-flow checks make it impossible to reach that branch otherwise.


### 4. Validate exploitability and attacker control

The agent MUST reject or downgrade reports where the trigger is not attacker controlled, including:

- non-default or operator-controlled configuration only
- attacker must already control the local host
- attacker must control local files, local libraries, environment variables, startup flags, runtime parameters, or trusted operator inputs
- attacker must have validator/operator privileges for a claim that is not about validator privileges
- user must import a malicious local file or run local code in a user-side workflow
- modified binaries, disabled checks, or non-standard deployments are required

Use `not attacker controlled` as the class when this is the primary reason.

### 5. Validate reproduction

The agent MUST attempt best-effort validation without using live targets.

For claims of:

- consensus stop
- validator crashes validator
- any node crashes other node
- validator crashes other nodes

require reproduction with `ton-bug-triage` or convincing included artifacts from that workflow. If this evidence is missing, set status to `partially correct` at best, unless the claim is otherwise clearly false.

For smart contract fund theft claims, executable local traces are useful but not mandatory. If no trace is provided, validate source-level logic and clearly mark assumptions.

For DDoS or OOM claims, a quantitative amplification measurement is useful but not mandatory. Still require a realistic attacker-controlled trigger and a realistic impact path.

For assertions or crashes, report whether the crash appears assertion-based, debug-only, local-tool-only, or reachable during normal node/service operation. Do not automatically reject assertions only because they are assertions.

If the bug is reachable only through a modified local file, CLI argument, local tool workflow, or other user-side operation, set final verdict to `REJECTED`.

### 6. Unexpected behavior

Some issues may relate to behavior that is counterintuitive or inconsistent with documentation. This does not automatically make the issue valid or invalid.

For example, an opcode may appear, based on its short name, to behave in one way while actually behaving differently. Such an issue may be reported to the bug bounty, but the expected bounty and impact should be low unless the report demonstrates that the behavior is exploitable in practice right now, not only in theoretical scenarios.

Issues based on assumptions from other networks or blockchains are not applicable to TON. For example, reports treating asynchronous non-atomic transaction traces as a vulnerability are not accepted, especially when the behavior is properly documented.


### 7. Validate bug bounty eligibility

Check:

- target is in scope
- category is accepted
- impact is concrete
- prerequisites are realistic
- attack path is attacker controlled
- the issue is not explicitly excluded
- the finding is not already fixed in the latest relevant branch

Reject or warn for:

- out-of-scope target
- unsupported environment
- theoretical-only attack without impact
- missing reproduction steps
- unverifiable claim
- hallucinated code or impossible call path
- duplicate-style low-effort report
- known peculiarity without new security impact

Known peculiarities are not accepted as bounty issues unless the report demonstrates a new security impact. This includes Elector `max_stake` accounting/recovery behavior, Elector recover-stake bounce semantics, and deleted-wallet redeploy/reuse behavior.

Toncenter HTTP API and Toncenter indexer reports may be eligible for wrong balance or index data, authentication issues, private data exposure, or critical service bugs. Status-code hygiene such as 500 instead of 4xx/405 while the service remains healthy MUST be rejected.

### 8. Validate report completeness

Check whether the original report contains:

- title
- summary
- affected component
- affected commit
- affected files and functions
- attack prerequisites
- trigger conditions
- reproduction steps
- expected result
- actual result
- proof of concept or equivalent evidence
- security impact
- suggested remediation
- environment details

If reproduction steps are missing, mark this as a report deficiency. Do not invent reproduction steps unless they are directly derivable from provided PoC or logs.

If security impact is missing, infer likely impact only from verified code paths and label it as inferred. Do not invent exploitability beyond evidence.

If suggested remediation is missing, mark it as missing. Do not invent remediation.

If environment details are missing, mark them as missing. Do not substitute the agent's local environment as the researcher's environment.

### 9. Detect common errors

The agent MUST detect and flag:

- claiming RCE without code execution
- claiming critical impact without fund loss, consensus risk, critical service compromise, or comparable impact
- overstating denial-of-service impact
- generic crash without a security impact explanation
- generic parser crash without an untrusted input path
- malformed-packet fuzzing without exploitability
- crash requiring modified binaries or disabled checks
- debug-only or local-tool-only crash presented as network impact
- report against old code that is fixed in latest branch
- report against non-production stubs or deprecated components
- frontend OWASP header or open-port issue without a real attack chain
- Toncenter status-code hygiene issue while service remains healthy
- known implementation/design peculiarity without new security impact
- AI-hallucinated files, functions, call paths, or behavior

### 10. Simplex Issues
When analyzing simplex issues it is recommended to load recent version of [Simplex docs](https://github.com/ton-blockchain/simplex-docs/blob/main/Simplex.md) to better understand context. For PoC shceks it is recommended to use tontester and [ton-triage-skill](https://github.com/ton-blockchain/ton-triage-skill).

### 11. Smart Contract Audits

When validating a smart contract report, recommend that the user install [acton](https://github.com/ton-blockchain/acton) and [acton skills](https://github.com/ton-blockchain/skills/) if they are not already installed.

These tools significantly reduce the risk of common report mistakes. The most frequently misunderstood points include:

* The action phase, where messages are actually sent, happens after the computation phase, where contract logic is executed. Therefore, a “send first, then check and throw” order in the code does not mean that the message is sent before the check fails.
* In TON, `true` is represented as `-1`, not `1`.


## Status, Verdict, Confidence, and Class

### Report format
Since the bug bounty bot only accepts reports in plain text, `.md`, or secret GitHub Gist link format, raise a warning if the submitted report uses a different format.


### Short status values

Use exactly one of:

- `correct`: technically valid, sufficiently evidenced, in scope, and ready to submit as-is or with only minor non-blocking warnings
- `partially correct`: plausible or partly verified, but missing blocking evidence, reproduction, impact clarity, or report completeness
- `incorrect`: false, unreproducible, already fixed, not attacker controlled, or otherwise not a valid TON bug bounty report
- `out-of-scope but technically valid`: technically real but not eligible for this TON bug bounty flow

If status is not `correct`, the short assessment MUST start with the line:

```text
DO NOT SEND!!!
```

### Final verdict values

Use exactly one of:

- `PASS`: report appears ready for submission
- `PASS WITH WARNINGS`: technically plausible or valid but has warnings, missing evidence, low-priority scope, or non-blocking uncertainty
- `REJECTED`: report should not be submitted to this bounty in its current form
- `REJECTED: already fixed`: issue affects old code but not the latest relevant branch

Status and final verdict are independent, but `correct` should normally correspond to `PASS` or `PASS WITH WARNINGS`. If status is not `correct`, the short assessment still MUST say `DO NOT SEND!!!`.

### Confidence fields

Use integers from 0 to 100.

- `Confidence`: confidence in the final verdict
- `Fit bug bounty confidence`: confidence that the report fits current TON bug bounty eligibility

### Class enum

Choose one primary class from this list, or use `other: <specific class>` if none fits.

- `any node crashes other node`
- `validator crashes validator`
- `validator crashes other nodes`
- `consensus halt`
- `consensus split`
- `block production halt`
- `state corruption`
- `blockchain money printing`
- `blockchain money stealing`
- `smart contract money stealing`
- `smart contract crashes validator`
- `service DDoS`
- `OOM`
- `tonlib`
- `pytonlib`
- `mytonctrl`
- `toncenter wrong balance/index data`
- `auth issue`
- `private data exposure`
- `API data leak`
- `signature/auth bypass`
- `frontend redirect to HackenProof`
- `third-party redirect`
- `out-of-scope local-only`
- `out-of-scope known peculiarity`
- `out-of-scope catchain`
- `out-of-scope FunC/Fift`
- `out-of-scope unsupported component`
- `already fixed`
- `not attacker controlled`
- `parser crash without network path`
- `duplicate-style low-effort`
- `configuration-only issue`
- `SDK misuse`
- `other: <specific class>`

Class meanings for crash reports:

- `any node crashes other node`: a request or message from any node to any other node can cause a crash.
- `validator crashes validator`: only a validator has the required privileges to send the message or perform the action that crashes another validator.
- `validator crashes other nodes`: only a validator has the required privileges to send the message or perform the action that crashes another non-validator or general node.

For TonLib-specific issues, include `tonlib` in the class unless a more specific class clearly dominates.

For smart contracts, distinguish standard TON smart contract issues from third-party contract issues. Third-party contracts should normally be redirected to their owners. For wallet, token, multisig, DNS, nominator pool, and other standard smart contract reports, validate repository source code only; deployed bytecode compatibility checks are not required.

## Agent Behavior Requirements

The agent MUST:

- prioritize factual accuracy over optimism
- prefer false negatives over false positives when uncertain
- avoid hallucinating vulnerabilities
- avoid inventing impact
- avoid inventing exploitability
- avoid fabricating reproduction results
- avoid improving the exploitability story beyond evidence
- clearly separate verified facts from assumptions
- downgrade confidence when evidence, reproduction, or scope is unclear

The agent SHOULD be conservative and concise.

## Required `self-check-report.md` Structure

The generated report MUST be concise, technical, reproducible, evidence-based, and written in the same language as the original report. If the original report is mixed-language or unclear, use English.

Use this structure:

```markdown
# TON Bug Bounty Self-Check Report

## Short Assessment

<DO NOT SEND!!! line if status is not correct>
Status: <correct|incorrect|partially correct|out-of-scope but technically valid>.
Confidence: <0-100>.
Fit bug bounty confidence: <0-100>.
Component: <component>.
Class: <class enum value>.
Self-check report: <path to self-check-report.md>.

## Repository State

- Analysis date UTC: <date and time>
- Input: <file, directory, or chat/stdin>
- Bug bounty rules repository: <URL>
- Bug bounty rules commit: <hash or unavailable>
- Repositories analyzed:
  - <URL>, branch <branch>, commit <hash>, submodules <updated/not present/not updated>
- Local modification warnings: <none or list>
- Fetch/build limitations: <none or list>

## Scope Validation

- Target component: <component>
- In scope: <yes/no/uncertain>
- Eligible category: <yes/no/uncertain>
- Redirect required: <none/HackenProof/third-party owner>
- Relevant exclusions or warnings: <list>

## Technical Finding Summary

Explain the claimed issue in plain language.

## Vulnerability Existence

- Exact files/functions: <list>
- Verified code path: <summary>
- Attacker-controlled input path: <summary>
- Assumptions: <verified assumptions and missing assumptions>
- Already fixed: <yes/no/uncertain>

## Reproducibility

- Reproduced: <yes/no/partially/not attempted>
- Reproduction method: <static/local emulator/tontester/ton-bug-triage/local service/other>
- Reproduction confidence: <0-100>
- Missing reproduction evidence: <list>
- Live-target testing avoided: <yes/no and reason>

## Bug Bounty Eligibility

- Technical validity: <valid/invalid/partially valid/uncertain>
- Bounty eligibility: <eligible/not eligible/uncertain>
- Realistic attacker prerequisites: <yes/no/uncertain>
- Security impact: <verified or inferred impact>
- Low-priority notes: <none or list>

## Severity and Claim Validation

- Claimed impact/severity: <from report>
- Validated impact: <validated impact>
- Overclaiming or downgrade notes: <none or list>

## Report Completeness Check

- Title: <present/missing>
- Summary: <present/missing>
- Affected component: <present/missing>
- Affected commit: <present/missing/filled with latest analyzed commit>
- Affected files/functions: <present/missing>
- Attack prerequisites: <present/missing>
- Trigger conditions: <present/missing>
- Reproduction steps: <present/missing>
- Expected result: <present/missing>
- Actual result: <present/missing>
- Proof of concept or evidence: <present/missing>
- Security impact: <present/missing/inferred>
- Suggested remediation: <present/missing>
- Environment details: <present/missing>

## Common Error Scan

List detected issues such as exaggerated claims, missing impact, missing reproduction steps, unsupported assumptions, outdated code references, local-only trigger, known peculiarity, or duplicate-style pattern.

## Final Verdict

Final verdict: <PASS|PASS WITH WARNINGS|REJECTED|REJECTED: already fixed>

Detailed reasoning: <concise reasoning>

Submission guidance: <send/do not send/redirect>

Note: This self-check is not an official TON triage decision and does not guarantee a bounty. Invalid or low-quality reports may reduce reviewer trust and review priority.
```

## Final Agent Response

After writing `self-check-report.md`, reply with:

1. the short assessment exactly as written in the report
2. the path to the generated file
3. one concise sentence summarizing why the status was assigned

Do not include long raw logs or large transcripts in the final response.
