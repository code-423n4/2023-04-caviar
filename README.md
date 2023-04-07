# Caviar Private Pools contest details

- Total Prize Pool: \$47,000 USDC
  - HM awards: \$25,500 USDC
  - QA report awards: \$3,000 USDC
  - Gas report awards: \$1,500 USDC
  - Judge awards: \$6,000 USDC
  - Lookout awards: \$2400 USDC
  - Scout awards: \$500 USDC
  - Mitigation review contest: \$8,100 USDC (_Opportunity goes to top 5 certified wardens based on placement in this contest._)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2023-04-caviar-private-pools/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts April 07, 2023 20:00 UTC
- Ends April 13, 2023 20:00 UTC

# Caviar Private Pools

A private pool is a an NFT AMM controlled by a single owner. Each private pool is highly customizable with concentrated liquidity, custom fee rates, stolen NFT filtering, custom NFT weightings, royalty support, and flash loans. Liquidity providers deposit NFTs and ETH into these pools to enable trading. Docs are available [here](https://docs.caviar.sh/technical-reference/custom-pools). And a demo is available on [beta.goerli.caviar.sh](https://beta.goerli.caviar.sh/).

## Quickstart command

```
rm -Rf 2023-04-caviar || true && git clone https://github.com/code-423n4/2023-04-caviar.git --recurse-submodules -j8 && cd 2023-04-caviar && yarn && foundryup && forge install && forge test --ffi --gas-report
```

## Getting started

```
yarn
forge install
forge test --gas-report --ffi
```

## System overview

- The [Factory](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol) contract allows users to create and initialize new custom pools that are minimal proxies which point to a reference implementation. It is responsible for issuing NFTs that represent ownership of each custom pool. All protocol fees accrue to the factory contract and can be withdrawn by the protocol admin. Initially the protocol fee rate will be set to be 0% however it may be increased in the future, with advanced notice.

- The [PrivatePool](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol) contract contains all of the core logic for custom pools. It allows users to set concentrated liquidity, custom fee rates, NFT weightings, change/flashloan fee rates, royalty fee support, and stolen NFT filtering. Traders can buy, sell, and change NFTs for other NFTs within the pool.

- The [EthRouter](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol) contract is responsible for taking in a sequence of actions and executing them against the various pools. This is useful if a user wants to buy N amount of NFTs that belong to Y different pools. For example, Bob wants to buy token #1, #2, and #3. Token #1 belongs to pool A. Tokens #2, and #3 belong to pool B. Bob can submit an array of buys to the EthRouter and it will execute a buy from both pool A and pool B in one transaction. The EthRouter also interfaces with caviar public pools, which can be found [here](https://github.com/outdoteth/caviar).

- The [PrivatePoolMetadata](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePoolMetadata.sol) contract is responsible for generating an on-chain svg and metadata representation of the NFT that represents ownership of a custom pool. This is used to display the NFT across various marketplaces and wallets.

## Contracts overview

| File                                                                                                                                                                                                                                                                                   |       [SLOC](#nowhere "(nSLOC, SLOC, Lines)")       | Description                                             | Libraries                                                              |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------: | :------------------------------------------------------ | :--------------------------------------------------------------------- |
| _Contracts (4)_                                                                                                                                                                                                                                                                        |
| [src/Factory.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol) [💰](#nowhere "Payable Functions") [📤](#nowhere "Initiates ETH Value Transfer")                                                                                                             |   [82](#nowhere "(nSLOC:69, SLOC:82, Lines:171)")   | Creates new pools and also accrues protocol fees        | `solady/*` `solmate/*`                                                 |
| [src/PrivatePoolMetadata.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePoolMetadata.sol)                                                                                                                                                                      |   [90](#nowhere "(nSLOC:90, SLOC:90, Lines:120)")   | Generates NFT metadata and svgs for each pool           | `@openzeppelin/*` `solmate/*`                                          |
| [src/EthRouter.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol) [💰](#nowhere "Payable Functions")                                                                                                                                                       | [179](#nowhere "(nSLOC:168, SLOC:179, Lines:317)")  | Routes trades to various pools                          | `solmate/*` `@openzeppelin/*` `caviar/*` `royalty-registry-solidity/*` |
| [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol) [🖥](#nowhere "Uses Assembly") [💰](#nowhere "Payable Functions") [📤](#nowhere "Initiates ETH Value Transfer") [🧮](#nowhere "Uses Hash-Functions") [♻️](#nowhere "TryCatch Blocks") | [379](#nowhere "(nSLOC:325, SLOC:379, Lines:794)")  | Core AMM logic for each newly deployed private pool     | `solmate/*` `solady/*` `@openzeppelin/*` `royalty-registry-solidity/*` |
| _Interfaces (1)_                                                                                                                                                                                                                                                                       |
| [src/interfaces/IStolenNftOracle.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/interfaces/IStolenNftOracle.sol)                                                                                                                                                      |   [11](#nowhere "(nSLOC:10, SLOC:11, Lines:22)")    | Interface for validating whether NFTs are stolen or not |                                                                        |
| Total (over 5 files):                                                                                                                                                                                                                                                                  | [741](#nowhere "(nSLOC:662, SLOC:741, Lines:1424)") |                                                         |                                                                        |

## External imports

- **caviar/Pair.sol**
  - [src/EthRouter.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol)
- **openzeppelin/interfaces/IERC2981.sol**
  - [src/EthRouter.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol)
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
- **openzeppelin/interfaces/IERC3156FlashLender.sol**
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
- **openzeppelin/utils/Base64.sol**
  - [src/PrivatePoolMetadata.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePoolMetadata.sol)
- **openzeppelin/utils/Strings.sol**
  - [src/PrivatePoolMetadata.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePoolMetadata.sol)
- **royalty-registry-solidity/IRoyaltyRegistry.sol**
  - [src/EthRouter.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol)
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
- **solady/utils/LibClone.sol**
  - [src/Factory.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol)
- **solady/utils/MerkleProofLib.sol**
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
- **solmate/auth/Owned.sol**
  - [src/Factory.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol)
- **solmate/tokens/ERC20.sol**
  - [src/Factory.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol)
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
  - [src/PrivatePoolMetadata.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePoolMetadata.sol)
- **solmate/tokens/ERC721.sol**
  - [src/EthRouter.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol)
  - [src/Factory.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol)
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
  - [src/PrivatePoolMetadata.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePoolMetadata.sol)
- **solmate/utils/FixedPointMathLib.sol**
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)
- **solmate/utils/SafeTransferLib.sol**
  - [src/EthRouter.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/EthRouter.sol)
  - [src/Factory.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/Factory.sol)
  - [src/PrivatePool.sol](https://github.com/code-423n4/2023-04-caviar/blob/main/src/PrivatePool.sol)

## Scoping Details

```
- If you have a public code repo, please share it here:
- How many contracts are in scope?:   4
- Total SLoC for these contracts?:  725
- How many external imports are there?:  12
- How many separate interfaces and struct definitions are there for the contracts within scope?:  3
- Does most of your code generally use composition or inheritance?:   inheritance
- How many external calls?:   10
- What is the overall line coverage percentage provided by your tests?:  N/a
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?:   Caviar public pools: https://github.com/outdoteth/caviar
- Please describe required context:   The EthRouter contract routes trades to caviar public pools (in addition to private pools)
- Does it use an oracle?:  no
- Does the token conform to the ERC20 standard?:  N/a
- Are there any novel or unique curve logic or mathematical models?: no
- Does it use a timelock function?:  no
- Is it an NFT?: Yes
- Does it have an AMM?:   Yes
- Is it a fork of a popular project?:   No
- Does it use rollups?:   No
- Is it multi-chain?:  No
- Does it use a side-chain?: No
```

## Known issues

- There is a bug related to slither that prevents it from running correctly. More info can be found here: https://github.com/crytic/slither/issues/1737

- There is a bug related to generating coverage reports with forge. More info can be found here: https://github.com/foundry-rs/foundry/issues/3357
