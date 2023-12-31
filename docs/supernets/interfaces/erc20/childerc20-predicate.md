---
id: childerc20-predicate
title: ChildERC20 Predicate
sidebar_label: ChildERC20 Predicate
description: "Learn about the access control module and how to allowlist validators."
keywords:
  - docs
  - polygon
  - edge
  - consensus
  - interface
---

The `IChildERC20Predicate` interface is part of the cross-chain bridge system, which manages the child tokens on the L2 side. This guide will explain the methods provided by the IChildERC20Predicate interface and how to use them.

## Overview

The `IChildERC20Predicate` interface is responsible for managing child tokens on L2. It provides:

- Methods for deploying new child tokens.
- Processing state updates received from L1.
- Withdrawing tokens back to L1.

## Initialize

Before using the IChildERC20Predicate contract, it must be initialized with the required parameters:

```solidity
function initialize(
    address newL2StateSender,
    address newStateReceiver,
    address newRootERC20Predicate,
    address newChildTokenTemplate,
    address newNativeTokenRootAddress,
    string calldata newNativeTokenName,
    string calldata newNativeTokenSymbol,
    uint8 newNativeTokenDecimals
) external;
```

The parameters for initialization include the addresses of the L2 state sender, the state receiver, the root ERC20 predicate, and the child token template. The initialization also requires the native token's root address, name, symbol, and decimals.

## Deploy a new child token

To create a new child token on the L2 side, call the deployChildToken method:

```solidity
function deployChildToken(
    address rootToken,
    bytes32 salt,
    string calldata name,
    string calldata symbol,
    uint8 decimals
) external;
```

The parameters for deploying a new child token include the address of the corresponding root token on L1, a unique salt value, and the token's name, symbol, and decimals.

## Process state updates

The onStateReceive method receives state updates from the L1 side and processes the data accordingly:

```solidity
function onStateReceive(uint256 /* id */, address sender, bytes calldata data) external;
```

When a state update is received, the method processes the data based on the sender's address and provided data. The system typically calls this method automatically when state updates are received from L1.

## Withdraw tokens

To withdraw tokens from L2 back to L1, call the withdraw method:

```solidity
function withdraw(IChildERC20 childToken, uint256 amount) external;
```

This method takes the child token contract address and the amount to be withdrawn. The tokens will be withdrawn to the message sender's address on L1.

## Withdraw tokens to a specific address

If you want to withdraw tokens from L2 to L1 for a specific address, use the `withdrawTo` method:

```solidity
function withdrawTo(IChildERC20 childToken, address receiver, uint256 amount) external;
```

This method takes the child token contract address, the receiver's address, and the amount to be withdrawn. The tokens will be withdrawn to the specified receiver's address on L1.
