---
title: 'How to Deploy Tokens (NFTs) on the Ethereum Network'
description: 'A step-by-step guide to deploying Non-Fungible Tokens (NFTs) on the Ethereum Network.'
date: '2023-07-03'
tags: ['Blockchain', 'Ethereum', 'NFTs']
---

# Introduction

Non-Fungible Tokens (NFTs) have become a popular topic in the blockchain community. They offer a way to prove ownership of a piece of digital content on the blockchain. In this guide, we'll go through the steps to deploy an NFT on the Ethereum network.

# Prerequisites

Before we start, you'll need the following:

- Basic knowledge of Solidity, Ethereum's programming language.
- [Metamask](https://metamask.io/) or similar Ethereum wallet installed in your browser.
- Some Ether (ETH) in your wallet to pay for gas fees.

# Step 1: Write your Smart Contract

In Solidity, you write a smart contract to create your NFT.

```solidity
pragma solidity ^0.5.0;

import "@openzeppelin/contracts/token/ERC721/ERC721Full.sol";
import "@openzeppelin/contracts/drafts/Counters.sol";

contract MyNFT is ERC721Full {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    constructor() ERC721Full("MyNFT", "MNFT") public {
    }

    function mintNFT(address recipient, string memory tokenURI)
        public returns (uint256)
    {
        _tokenIds.increment();

        uint256 newItemId = _tokenIds.current();
        _mint(recipient, newItemId);
        _setTokenURI(newItemId, tokenURI);

        return newItemId;
    }
}
```

In the above code, we're importing two OpenZeppelin contracts: ERC721Full and Counters. ERC721Full is a complete ERC721 Non-Fungible Token implementation, while Counters provides a counter that can be used to create unique identifiers.

The mintNFT function allows you as a user to create a new token by specifying the recipient's address and the token's URI (Uniform Resource Identifier), which points to the metadata (like name, description, image, etc.) of the token. After the token is minted, its URI is set, and the function returns the new token's ID.

# Step 2: Compile your Smart Contract

To compile your smart contract, you'll need a Solidity compiler. You can use the Truffle Suite, which is a development environment, testing framework, and asset pipeline for Ethereum. You can install it using npm (Node.js package manager) as follows:

```
npm install -g truffle
```

Then, you can compile your smart contract using the truffle compile command. Make sure your contract file is located in the contracts folder in your Truffle project.

```
truffle compile

```

# Step 3: Deploy your Smart Contract to the Ethereum Network

To deploy your contract, you first need to configure your Ethereum wallet with Truffle. In the truffle-config.js file, you can add configurations for different networks. For deploying to the Rinkeby test network, for example, you could use this configuration:

```
const HDWalletProvider = require('@truffle/hdwallet-provider');
const mnemonic = 'your mnemonic here';

module.exports = {
  networks: {
    rinkeby: {
      provider: function() {
        return new HDWalletProvider(mnemonic, "https://rinkeby.infura.io/v3/YOUR-PROJECT-ID")
      },
      network_id: 4
    },
  },
  compilers: {
    solc: {
      version: "^0.8.0",    // Fetch exact version from solc-bin
    }
  },
  mocha: {
    enableTimeouts: false,
    before_timeout: 120000 // Here is 2min but can be whatever timeout is suitable for you.
  },
  plugins: [
    'truffle-plugin-verify'
  ],
  api_keys: {
    etherscan: 'YOUR_ETHERSCAN_API_KEY'
  }
};

```

The HDWalletProvider is a convenient way to make transactions with your Ethereum wallet. The mnemonic is a secret phrase that is used to generate your account's private and public keys. You should keep it secure and never reveal it to anyone. YOUR-PROJECT-ID is the ID of your project on Infura, a service that provides Ethereum nodes as a service.

Next, you can create a migration script for deploying your smart contract. In the migrations folder in your Truffle project, create a file like 2_deploy_contracts.js and add the following:

```
const MyNFT = artifacts.require("MyNFT");

module.exports = function (deployer) {
  deployer.deploy(MyNFT);
};

```

Then you can deploy your contract using the truffle migrate command, specifying the network:

```
truffle migrate --network rinkeby

```

# Step 4: Interact with your NFT Smart Contract

After deploying your smart contract, you can interact with it. You can use Truffle's console to do this. First, run the console specifying the network:

```
truffle console --network rinkeby

```

Then, you can instantiate your contract in the console and call its methods:

```
const contract = await MyNFT.deployed();
const tokenId = await contract.mintNFT("0xYourAddress", "https://my-nft.com/token/1");

```

This will mint a new NFT and return its ID.

# Conclusion

Deploying an NFT on the Ethereum network can seem like a daunting task, but with the right tools, it's a straightforward process. With Solidity, OpenZeppelin, and Truffle, you can write, compile, and deploy a smart contract for creating unique tokens. The possibilities for what you can do with NFTs are vast, from digital art and music to virtual real estate and more.

Below is an example of a digital art (Olycoin)

![Olycoin](https://github.com/chegeanthony/anthony-chege.me/assets/91752484/a1691da5-ca37-4e04-84ff-fd4f748ea1ba)

OH! a quick tip :( Remember to always be mindful of the gas fees associated with deploying contracts and minting NFTs on the Ethereum network. These costs can fluctuate and become quite high when the network is busy.

In this guide, we only scratched the surface of what's possible with NFTs and Ethereum. I encourage you to continue exploring this exciting area of technology.

Happy coding!

```
~ Tony 😄

```

# References

- [OpenZeppelin Documentation](https://docs.openzeppelin.com/contracts/)
- [Truffle Suite](https://www.trufflesuite.com/)
- [Infura](https://infura.io/)
- [Metamask](https://metamask.io/)
- [Solidity Documentation](https://docs.soliditylang.org/)
- [ERC721 Standard](https://eips.ethereum.org/EIPS/eip-721)

# Tags

Blockchain, Ethereum, NFTs
