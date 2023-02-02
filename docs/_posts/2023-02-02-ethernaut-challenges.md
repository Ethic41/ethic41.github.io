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

### task

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
