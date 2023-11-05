Zap ERC20 to ERC1155 Token Wrapper Contract
===============================================

Allows any ERC-20 token to be wrapped inside of an ERC-1155 contract, and thereby allows
an ERC-20 token to function as an ERC-1155 contract.

## Getting started

### Install

`yarn add @0xsequence/erc20-meta-token` or `npm install @0xsequence/erc20-meta-token`

### Usage from Solidity

```solidity
pragma solidity ^0.8.0;

import '@0xsequence/erc20-meta-token/contracts/interfaces/IERC20Wrapper.sol';

contract ContractA {
  //...
  function f(address payable wrapperAddress, address ERC20tokenAddress, uint256 amount) public {
    IERC20Wrapper(wrapperAddress).deposit(ERC20tokenAddress, msg.sender, amount);
  }
}
```

## How does it work?

When you deposit ERC-20 tokens (e.g. zap) in the wrapper contract, it will give you back ERC-1155 metaTokens (e.g. Metazap) with a 1:1 ratio. These metaToken have native meta-transaction functionalities, which allow you to transfer tokens without doing an on-chain transaction yourself, but by simply signing a message and broadcasting this message to "executors". You can also "approve" addresses to transfer tokens on your behalf with a signed message instead of calling the ERC-20 `approve()` function. 

If you want to transfer some metaTokens, you simply need to call `safeTransferFrom(sender, recipient, ERC20tokenAddress, amount, metaTransactionData)` where token address is the address of the ERC-20 token you want to transfer. Obtaining the balance is similar; `balanceOf(user, ERC20tokenAddress)`.

You can, at anytime, convert back these metaTokens back to their original tokens by calling the `withdraw()` method. 

## Gas Fee Abstraction

When transferring metaTokens, like metaZap, you can specify in which currency you want the transaction fee to be paid in. By default, ERC20 token transfers require users to pay the fee in ETH, but with metaTokens, users can pay directly in any ERC20 token they wish. Hence, at a high level, users could transfer zap by paying the transaction fee in zap as well, never needing to possess ETH. 

## Why use the ERC-1155 Interface?

There are a few reasons why the ERC-1155 standard interface was chosen for this contract. First of all, since byte arrays needs to be passed to the contract, supporting the ERC-20 interface for these metaTokens would not be possible (at least not without adding significant complexity).  Secondly, having a single contract for all ERC-20s is simpler for developers and third parties. Indeed, you don't need to deploy a contract for every ERC-20 token contract users want to augment with meta transaction functionality and third parties don't need to maintain a list of which ERC20 token address maps with which wrapper contract address. 

In addition, it becomes easier to have multiple version of wrapper contracts. Indeed, if 5 versions exists, you only need 5 contracts to support all ERC20s in the five different versions, compared for 5N contracts, where N is the number of ERC-20 contracts. 


## LICENSE

Licensed under [Apache-2.0](./LICENSE)
