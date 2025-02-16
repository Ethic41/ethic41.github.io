---
layout: post
title:  Solidity Notes
date:   2025-02-14 15:50:05
categories: solidity, blockchain
post_id: 9
---

Notes on solidity and blockchain. This notes will InshaAllah be continuously updated. Most of the notes are from the following sites
so you can visit them for more:

- [RareSkills.io](https://www.rareskills.io/learn-solidity/)
- [Solidity By example](https://solidity-by-example.org/)

### Division in Solidity

- **When division involves fractions then the numerator and denominator need to be multiplied a factor because solidity does not have floats.**

```solidity
/// for example you need 7.5% of 200 then you can't do this
uint 256 result = 200 * 7.5 / 100  // this won't work

/// instead you multiply by a factor of 10
uint 256 result = 200 * 75 / 1000

```

- **When divding 2 numbers and the numerator is greater than the denominator then the result will be 0.**

```solidity

uint256 cityPopulation = 1000;
uint256 nationPopulation = 10000;

uint256 fractionOfPopulation = cityPopulation / nationPopulation;
//fractionOfPopulation is zero!

```

Below is a result from `chisel`, a commandline tool part of the `foundry` toolchain that provides a `repl` for solidity:

![wrong division](https://ethic41.github.io/assets/images/posts/solidity-and-blockchain/1-wrong-division.png)

This is because solidity does not allow floats as floats are not deterministic, and the blockchain needs to be deterministic.
