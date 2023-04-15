---
title: 不要取整到整百(Hundred)
date: 2023-04-16 02:14:51
tags: solidity
---

## 发生了什么

Hundred 一个未被使用的借贷代币，被人进行了只读重入+取整误差攻击

## 攻击分析

[攻击交易在此](https://openchain.xyz/trace/optimism/0x15096dc6a59cff26e0bd22eaf7e3a60125dcec687580383488b7b5dd2aceea93)

在链接所示交易中，作者进行了两次子攻击，子攻击的过程如下：

1. 向合约中存入一定数量代币，如 400000000 satoshi, 铸造出 200000000 个 Hundred WBTC
2. 取出 199999998 个 Hundred WBTC 代币，此时合约返回给用户 399999999 satoshi(因为合约之前有人存过，所以兑换率比2稍微大一点点), 此时合约中有 1 satoshi 和 2 个 hWBTC
3. 此时强行向合约中转帐一定数量 WBTC, 从而操纵汇率为 2:401000000
4. 利用存入的金额进行借贷, 由于价值为 4.01 btc, 可以贷出约为 1btc 的资产
6. 通过 `redeemUnderlying` 取出 400999999 satoshi, 此时取出代币数量计算为 1, 产生舍入误差, 合约读取的汇率为之前缓存的 2:401000000, 由于舍入误差, 用户还剩 1 个 hWBTC, 价值为根据之前汇率计算的 2.005 BTC
7. 攻击完成, 获利

## 教训

小心取整问题, 特别是你没多少存款的时候。