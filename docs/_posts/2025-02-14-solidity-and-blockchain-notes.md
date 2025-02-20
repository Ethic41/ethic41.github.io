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

- **When dividing 2 numbers and the numerator is greater than the denominator then the result will be 0.**

```solidity

uint256 cityPopulation = 1000;
uint256 nationPopulation = 10000;

uint256 fractionOfPopulation = cityPopulation / nationPopulation;
//fractionOfPopulation is zero!

```

Below is a result from `chisel`, a commandline tool part of the `foundry` toolchain that provides a `repl` for solidity:

![wrong division](https://ethic41.github.io/assets/images/posts/solidity-and-blockchain/1-wrong-division.png)

This is because solidity does not allow floats as floats are not deterministic, and the blockchain needs to be deterministic.

### Overflow and Underflow

Prior to solidity version 0.8 overflow and underflow were not checked by default. This means that if you add 1 to the maximum value of a uint256, the value will wrap around to 0. This can be a security risk if not properly handled.

```solidity
uint256 max = 2**256 - 1;
uint256 overflow = max + 1;
// overflow is 0
```

starting from solidity version 0.8, overflow and underflow are checked by default. This means that if you add 1 to the maximum value of a uint256, the transaction will revert.

![overflow and underflow](https://ethic41.github.io/assets/images/posts/solidity-and-blockchain/2-overflow-underflow.png)

### Arrays and Strings

The function argument for arrays and strings is `calldata` and the function argument for the return type is `memory`. The `calldata` "refer to the data in the ethereum transaction itself". `memory` is similar to `heap` in other languages like C++.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ExampleContract {
    function booleanArrayExample(bool[] calldata input) public pure returns (bool[] memory) {
        return input;
    }

    function addressArrayExample(address[] calldata input) public pure returns (address[] memory) {
        return input;
    }
}

```

- **Trying to access the value of an empty array will revert**

```solidity
uint256[] memory numbers;
// add item to the array
numbers.push(10);

uint256 number = numbers[0]; // this will revert

```

- **Passing more element than the array can hold will revert for a fixed-size array**

```solidity
uint256[3] memory numbers;
numbers[3] = 10; // this will revert
```

- **String concatenation support was added in ^0.8.12**

```solidity

pragma solidity ^0.8.12;
contract ExampleContract {
    function useArrays(string calldata user) 
        public 
        pure 
        returns(string memory) {
            return string.concat("hello ", user);
    }
}
```

- **Uninitialized values can have unintended consequences if not carefully put into consideration**

```solidity

// notice that `uint256 i` is not initialized, so it's value is 0
function filterOdd(uint256[] memory _arr) public view returns (uint256[] memory){
        uint256[] memory evenNumbers = new uint256[](_arr.length);
        uint256 count = 0;

        for (uint256 i; i < _arr.length; i++){
            if (_arr[i] % 2 == 0){
                evenNumbers[count] = _arr[i];
                count += 1;
            }
        }

        return evenNumbers;
    }

// correct implementation should be
function filterOdd(uint256[] memory _arr) public view returns (uint256[] memory){
        uint256[] memory evenNumbers = new uint256[](_arr.length);
        uint256 count = 0;

        for (uint256 i = 1; i < _arr.length; i++){
            if (_arr[i] % 2 == 0){
                evenNumbers[count] = _arr[i];
                count += 1;
            }
        }

        return evenNumbers;
    }

```

### Storage

- **A storage variable with `internal` modifier is not visible to other contracts but is still stored on the blockchain and visible to anyone who reads the blockchain.**

```solidity
contract ExampleContract {
    uint256 internal secretNumber = 42;
}

```

