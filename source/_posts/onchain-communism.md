---
title: 重新定义低危漏洞 - Nomad 爆炸
date: 2022-08-02 10:45:14
tags: solidity
---

有时候，数学不及格不只是能让你拿不到学位，甚至有可能让你成为蛆快链共产主义先锋。

## 发生了啥

由于 `Nomad` 桥的开发者使用了他们大概没有完全理解的 `Sparse Merkle Tree` , 这一哈希树结构的特点是未使用的叶子将以空叶表现。我们认为 `Nomad` 采用这一结构是为了有效率的构建欺诈证明，从而高效率验证交易。
`Nomad` 的审计机构， `QuantumStamp`, 在他们的 [审计报告](https://github.com/nomad-xyz/docs/blob/1ff0c55dba2a842c811468c57793ff9a6542ef0f/docs/public/Nomad-Audit.pdf) 中指出了这一点，并且标记为了 Low Risk，甚至还认为项目方确认了。
不幸的是，在这个错误的下面(QSP-19), Quantumstamp 还用小字标出了一句话：

> We believe the Nomad team has misunderstood the issue.
>> Quantumstamp

显然， `Nomad` 项目方认为，攻击者无法找到空叶的原像，但问题在于，空叶**不是**哈希为空，而是内容为空。所以任何以空内容调用他们桥合约的 `proof` 都可以确认并更改状态，这使得他们的欺诈证明完全失效。   
但是这一问题本身不足以造成 Hack 发生。[合约](https://etherscan.io/address/0xb92336759618f55bd0f8313bd843604592e27bd8#code) 中的 `Replica.sol::44` 将 `messageHash` 映射到了 `message` 对应的状态树根，问题在于，以太坊中未初始化的map取出的值为0，而 `nomad` 项目方不幸的，把 `0x00` 设为了可接受的roothash。

## 简直像狂欢一样

不幸的，在升级之后的 UTC 时间 8月1日晚上10点，有人发现了这个问题。  
之后的大约两个小时内，以太坊上的科学家们简直像狂欢一样，他们用空叶作为证明，快乐的将 `Nomad` 跨链桥中的一亿六千五百万美元的资产进行了一个款的提，对于跟风者来说这个活动简直像共产主义一样 —— 
只要修改交易的接收者，币种和数额，就会有免费的钱进入他们的账户，也许这就是真正的共产主义吧。

## 宁睡醒辣？

在大家提完款后之后的大概两小时，`Nomad` 团队终于睡醒辣，并且终于想起来把他们的合约赶紧下线。不幸的是，钱已经被各类来建设共产主义领取 Free Money 的人提取干净了。  
我们不能说 `Nomad` 反应迟钝，毕竟我们看到 [Ronin](https://rekt.news/zh/ronin-rekt/) 的朋友花了一周的时间才发现有问题，而[Harmony](https://rekt.news/zh/harmony-rekt/)的朋友也花了14个小时，只能说他们不算反应迟钝了。
