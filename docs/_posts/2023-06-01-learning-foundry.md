---
layout: post
title:  Learning Foundry
date:   2023-06-01 03:49:10 +0100
categories: foundry
post_id: 3
---

This is an unusual type of post, usually one creates a post after planning it out or perhaps after completing a task you intend to write about. This post (and probably more) will be different, I will write as I learn. The goal is to include my thought process and also avoid the procrastination that comes with trying to write a post after completing a task. If it works out, I will create more posts like this.

It's 4:00 AM and I've decided it's about time I learn [Foundry](https://github.com/foundry-rs/foundry). I have heard a lot of smart contract auditors and security researchers talk about it and how they use it to find bugs in smart contracts, so I thought it might be a good idea to check it out.

## [Foundry](https://book.getfoundry.sh/)

Foundry is a smart contract development toolchain built with solidity. It manages dependencies, project compilation, testing, and deployment. It provides a solidity REPL and allows interaction with the blockchain from the command line.

### Installation

Foundry installation is straightforward, there are several options for installing Foundry, but the easiest is the following:

```bash
curl -L https://foundry.paradigm.xyz | bash
```

### First steps with Foundry

To initialize a new project with foundry called `learning_foundry`:

```bash
forge init learning_foundry
```

This initializes a git repository named `learning_foundry` in your working directory with a few folders, the most useful ones being `lib`, `script`, `src`, and `test`. The `src` folder is where you will write all your contracts and codes, by default, it starts with a `Counter.sol` contract. The `test` folder is where all your tests will go and it contains the `Counter.t.sol` test contract. The `lib` folder is where all your libraries will be housed and by default, the `forge-std`` library is installed during initialization. A good thing about Foundry is that almost everything is customizable, so the above directory structure is not carved in stone, if you need to customize you can check the [Foundry documentation](https://book.getfoundry.sh/).

After initializing a project and probably adding some new code to the `src` folder, you can use Forge to compile and build your contracts as follows:

```bash
forge build
```

The output of the build can be found in the `out` folder.

To test your code, you write all your tests (more on how to write tests later) in the `test` folder and run:

```bash
forge test
```

So far we've learned to use `forge` to initialize, build and test our contract (at least the default one). Next, we will dive deeper into Foundry to see what it has to offer.

### VSCode Setup

Since we will be writing a lot of code in later sections it makes sense to set up VSCode. Add the following configuration to the `.vscode/setting.json` file:

```json
{
    "solidity.packageDefaultDependenciesContractsDirectory": "src",
    "solidity.packageDefaultDependenciesDirectory": "lib",

    "editor.formatOnSave": true,
    "[solidity]": {
        "editor.defaultFormatter": "NomicFoundation.hardhat-solidity"
    },
    "solidity.formatter": "forge",
}
```

## Managing Dependencies with Foundry

Foundry provides a flexible way to manage dependencies with `forge`. Forge utilizes the robust dependency management of `git submodules` to manage dependencies, this means that every dependency is a git submodule, and also any Github repository can be used as a dependency. Below is an example of adding a new dependency:

```bash
forge install transmissions11/solmate
```

The above command pulls the latest master branch of the repository `solmate` by the GitHub user `transmissions11` and adds it as a dependency to your project in the `lib` folder. You can also pull a different branch as follows:

```bash
forge install transmissions11/solmate@v7
```

To update a dependency:

```bash
forge update lib/solmate
```

To remove a dependency:

```bash
forge remove solmate
```

In my opinion, this approach to dependency management is really clean and easy.

## Testing with Foundry

In foundry, all tests are written in solidity, this avoids the need for context switching between multiple languages as in the case of hardhat. To write a test, you create a new file in the `test` folder with the extension `.t.sol`. To run all tests in the `test` folder:

```bash
forge test
```

Here's an example of running the `forge test` command in a freshly created project, that only has the default test:

```bash
$ forge test
No files changed, compilation skipped

Running 2 tests for test/Counter.t.sol:CounterTest
[PASS] testIncrement() (gas: 28312)
[PASS] testSetNumber(uint256) (runs: 256, μ: 27376, ~: 28387)
Test result: ok. 2 passed; 0 failed; finished in 24.43ms
```

Specific tests can be run with:

```bash
forge test --match-contract SomeContract --match-test testSomeFunction
```

glob pattern can be used to run tests that match a filename pattern:

```bash
forge test --match-path test/SomeContract.t.sol
```

By default, the `forge test` command will only display a summary of passing and failing tests. To increase the verbosity of the output, you can use the -v flag:

```bash
forge test -vvvvv
```
