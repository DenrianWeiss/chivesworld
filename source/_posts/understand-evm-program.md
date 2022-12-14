---
title: 理解EVM程序 - 从 Solidity 到 EVM的结构
date: 2022-12-14 18:01:14
tags: contract
---

在这篇文章中，我们将会带你理解 Solidity 程序在编译成 EVM 程序后是如何工作的。我们下面以 WETH 这一简单的智能合约为例讲解

## 从反汇编理解 EVM 程序

### 初始化

大部分智能合约都会以 `0x6060 6040 52...` 开头，这一初始化过程为: 
```assembly
PUSH1 0x60 # Part 1
PUSH1 0x40
MSTORE

PUSH1 0x04 # Part 2
CALLDATASIZE
LT
PUSH2 0x00af
JUMPI

PUSH1 0x00 # Part 3
CALLDATALOAD 
PUSH29 0x0100000000000000000000000000000000000000000000000000000000
SWAP1
DIV
PUSH4 0xffffffff
AND
DUP1
``` 
1. 在 0x40-0x60 的内存空间中存储空白地址指针 - [0x40 以下的地址被 EVM 保留用于内部Hash](https://docs.soliditylang.org/en/v0.8.17/internals/layout_in_memory.html#layout-in-memory) 
2. 检查 `CALLDATASIZE` 是否小于 4\(没有函数签名\)，如果是则跳转到 0x00af，否则继续执行  
3. 从 `CALLDATALOAD` 中读取函数签名，这一步是通过除以 `0x0100000000000000000000000000000000000000000000000000000000` 实现   
在提取完函数签名后，接下来是函数跳转表部分。

### 跳转表

反汇编结果的17-70行是函数跳转表，这一部分的作用是根据函数签名跳转到对应的函数。这一部分的代码格式如下：
```assembly
PUSH4 [signature]
EQ
PUSH2 [function]
JUMPI
DUP1
```
其中 signature 是函数签名，function 是函数的地址。这一部分的代码是由编译器自动生成的，我们可以通过 `solc --hashes` 命令查看函数签名和函数地址的对应关系。在 WETH 中，我们可以得到如下结果：
```
0x06fdde03 name()
0x095ea7b3 approve(address,uint256)
0x18160ddd deposit()
0x23b872dd transfer(address,uint256)
0x2e1a7d4d withdraw(uint256)
0x313ce567 totalSupply()
0x70a08231 balanceOf(address)
0x95d89b41 allowance(address,address)
0xa9059cbb transferFrom(address,address,uint256)
0xd0e30db0 deposit()
0xdd62ed3e allowance(address,address)
```

EVM 会用上一步获得的函数签名与跳转表按顺序匹配，最后会有一项来兜底失配的函数，这一部分的内容为跳转到 fallback 函数。

### 函数体和 fallback

在 匹配表匹配到对应函数签名后，EVM 会跳转到对应的函数体。函数体内部可以分为以下两个部分：参数解析和执行部分，函数进入后会检查参数长度是否正确，如果不正确则 `revert`。检查过参数长度后会进入执行和返回部分。


## 附录：工具列表

- [samczsun's signature db](https://sig.eth.samczsun.com/)
- [EVM 字节码列表](https://ethervm.io/)
