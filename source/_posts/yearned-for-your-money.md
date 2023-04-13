---
title: 现在你可以怀念（Yearn）你的USDT了
date: 2023-04-13 16:31:14
tags: smartcontract
---

## 发生了什么
[配的很好，下次不要再配了。 - samczsun ](https://twitter.com/samczsun/status/1646404331967778820?t=SSUs5yaW5sx52jCuKXAs9w&s=19)
在今天下午的 UTC 时间早上6点，Yearn 的 USDT 因为闪电贷收到了一次 [沉重打击](https://explorer.phalcon.xyz/tx/eth/0x8db0ef33024c47200d47d8e97b0fcfc4b51de1820dfb4e911f0e3fb0a4053138)。

## 交易分析

攻击者首先从 Balancer 闪电贷了 五百万 USDT，五百万 DAI 和两百万 USDC，之后将 USDC, DAI 换成了 USDT，之后存入了少量，然后将一部分 USDC 转给了[配置错误](https://twitter.com/samczsun/status/1646404331967778820?t=SSUs5yaW5sx52jCuKXAs9w&s=19)的 iearn USDT(将 fulcrum USDC 当成了 USDT)，从而操纵合约读到的 fulcrum 存款余额。之后通过一系列操作，进行swap和取回余额，从而通过操纵后的利率和价格获利

## 问题

Yearn 的 USDT 合约存在的主要问题就是错误配置的 fulcrum, 这一错误配置让攻击者可以通过操纵 fulcrum 存款余额，从而操纵合约的利率和价格，从而获利。（读出的余额是黑客转入的 USDC，真正存入的 USDT余额合约无法读取）。  
错误配置不会致命，但 1000 天都没发现的错误配置，只能说你配的很好，下次别配了。
