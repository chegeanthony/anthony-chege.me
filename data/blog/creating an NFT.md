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

In Solidity, you write a smart contract to create your NFT. Here's a simple example:

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
