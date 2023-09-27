# ZEROX Privacy Solution

### A fork of Tornado Cash for usable networks

Tornado Cash pioneered non-custodial Ethereum and ERC20 privacy solutions by implementing zkSNARKs, thereby breaking the on-chain link between recipient and destination addresses. The breakthrough innovation of Tornado Cash was inspirational, yet ZEROX $EROX has taken it a step further by offering a more versatile, efficient, and secure platform that supports multiple networks, including Binance Smart Chain. This advancement has made privacy more accessible and affordable, with added incentives through $EROX tokens.

EROX is based off Tornado Cash, yet supports [Binance Smart Chain](https://docs.binance.org/smart-chain/guides/bsc-intro.html). This makes it much more affordable to access the privacy critical to our lives. It also comes native with incentivisation to improve the privacy pool, where every deposit triggers earning of WIND.

## Specs

- Deposit gas cost: 991423
- Withdraw gas cost: 817089
- Circuit Constraints = 28271 (1869 + 1325 \* tree_depth)
- Circuit Proof time = 10213ms (1071 + 347 \* tree_depth)
- Serverless

![image](docs/diagram.png)

## Whitepaper

**[TornadoCash_whitepaper_v1.4.pdf](https://tornado.cash/audits/TornadoCash_whitepaper_v1.4.pdf)**

## Was it audited?

Tornado Cash was audited by a group of experts from [ABDK Consulting](https://www.abdk.consulting), specializing in zero-knowledge, cryptography, and smart contracts.

During the audit, no critical issues were found and all outstanding issues were fixed. The results can be found here:

- Cryptographic review https://tornado.cash/audits/TornadoCash_cryptographic_review_ABDK.pdf
- Smart contract audit https://tornado.cash/audits/TornadoCash_contract_audit_ABDK.pdf
- Zk-SNARK circuits audit https://tornado.cash/audits/TornadoCash_circuit_audit_ABDK.pdf

Underlying circomlib dependency is currently being audited, and the team already published most of the fixes for found issues

## Requirements

1. `node v11.15.0`
2. `npm install -g npx`

## Usage

You can see example usage in cli.js, it works both in the console and in the browser.

1. `npm install`
1. `cp .env.example .env`
1. `npm run build` - this may take 10 minutes or more
1. `npx ganache-cli`
1. `npm run test` - optionally runs tests. It may fail on the first try, just run it again.

Use browser version on Kovan:

1. `vi .env` - add your Kovan private key to deploy contracts
1. `npm run migrate`
1. `npx http-server` - serve current dir, you can use any other static http server
1. Open `localhost:8080`

Use the command-line version. Works for Ganache, Kovan, and Mainnet:

### Initialization

1. `cp .env.example .env`
1. `npm run build:contract`

### Ganache

1. make sure you complete steps from Initialization
1. `ganache-cli -i 1337`
1. `npm run migrate:dev`
1. `./cli.js test`
1. `./cli.js --help`

Example:

```bash
./cli.js deposit BNB 0.1 --rpc https://bsc-dataseed.binance.org/
```

> Your note: whirlwind-bnb-0.1-42-0xf73dd6833ccbcc046c44228c8e2aa312bf49e08389dadc7c65e6a73239867b7ef49c705c4db227e2fadd8489a494b6880bdcb6016047e019d1abec1c7652
> Whirlwind BNB balance is 8.9
> Sender account BNB balance is 1004873.470619891361352542
> Submitting deposit transaction
> Whirlwind BNB balance is 9
> Sender account BNB balance is 1004873.361652048361352542

```bash
./cli.js withdraw whirlwind-bnb-0.1-42-0xf73dd6833ccbcc046c44228c8e2aa312bf49e08389dadc7c65e6a73239867b7ef49c705c4db227e2fadd8489a494b6880bdcb6016047e019d1abec1c7652 0x8589427373D6D84E98730D7795D8f6f8731FDA16 0x2696fF371403abb43bF063264bDdFFCDeB6442ff --rpc https://bsc-dataseed.binance.org/
```

> Getting current state from whirlwind contract
> Generating SNARK proof
> Proof time: 9117.051ms
> Submitting withdraw transaction
> View transaction on bscscan https://bscscan.io/tx/0xcb21ae8cad723818c6bc7273e83e00c8393fcdbe74802ce5d562acad691a2a7b
> Transaction mined in block 17036120
> Done

## Deploy Native Asset Tornado Cash

1. `cp .env.example .env`
1. Tune all necessary params
1. `npx truffle migrate --reset --f 2 --to 5`

## Deploy ERC20 Tornado Cash

1. `cp .env.example .env`
1. Tune all necessary params
1. `npx truffle migrate --reset --f 2 --to 4`
1. `npx truffle migrate --reset --f 6`

**Note**. If you want to reuse the same verifier for all the instances, then after you deployed one of the instances you should only run the 5th or 6th migration for the native asset or ERC20 contracts respectively (`--f 5 --to 5` or `--f 6`).

## Credits

A huge shoutout to the pioneers, the creators of Tornado Cash, and the brilliant minds behind Circom & Websnark frameworks. The ZEROX $ZX project builds upon this foundational work to bring privacy solutions to the next level.

## Setting Up and Testing
Follow the provided steps in the documentation for a minimal demo example and to emulate MPC trusted setup ceremony. The detailed guide ensures smooth setup and testing, paving the way for developers and users to experience the cutting-edge privacy solutions offered by ZEROX $EROX.

In conclusion, ZEROX $EROX represents the next step in privacy-focused solutions in the cryptocurrency space, addressing the gaps left by Tornado Cash and enhancing functionality, accessibility, and security for users across multiple networks. The future of transactional privacy is here with ZEROX $EROX.

## Minimal demo example

1. `npm i`
1. `ganache-cli -d`
1. `npm run build:contract`
1. `cp .env.example .env`
1. `npm run migrate:dev`
1. `node minimal-demo.js`

## Emulate MPC trusted setup ceremony

```bash
cargo install zkutil
npx circom circuits/withdraw.circom -o build/circuits/withdraw.json
zkutil setup -c build/circuits/withdraw.json -p build/circuits/withdraw.params
zkutil export-keys -c build/circuits/withdraw.json -p build/circuits/withdraw.params -r build/circuits/withdraw_proving_key.json -v build/circuits/withdraw_verification_key.json
zkutil generate-verifier -p build/circuits/withdraw.params -v build/circuits/Verifier.sol
sed -i -e 's/pragma solidity \^0.6.0/pragma solidity 0.5.17/g' ./build/circuits/Verifier.sol
```
