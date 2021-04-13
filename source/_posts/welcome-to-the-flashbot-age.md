---
title: 欢迎来到 Flashbot 的年代
date: 2021-04-13 17:59:53
tags:
---

## Flashbot? 那是啥

Flashbot 是一种让交易者直接连接矿池并控制交易是否被打包和交易在包中的排序的机制。概括的来说：某些交易者通过故意发送不会被其他矿池包括的低 gas 交易，并和某个特定矿池进行场外交易，从而通过控制交易顺序及交易是否发生获利。

## Flashbot 的典型应用，以及为什么它打开了潘多拉的盒子

## 套利

Flashbot 目前的主要应用是进行三明治交易，也就是通过控制一些流动性不足的交易对，使得两笔交易包夹某个一般交易者的交易，两笔交易按先后分别为低价购入，高价卖出，使得被包夹的交易者遭受损失。此外包夹交易也可以用来攻击某些智能合约漏洞（尤其是涉及到随机性的）。

## 潘多拉的盒子

尽管之前可以通过提高 gas 进行夹交易，也可以通过购买算力进行类似攻击，但前者所有交易者仍在一条水平线上，而后者则成本过于高昂（购买能够有效攻击的算力成本非常大）
上面的攻击只利用了 Flashbot 控制打包交易的能力，而 Flashbot 当然可以被用来阻止别人的交易被打包，或者可以更进一步，根据接受的交易在矿池判断特定交易是否有利。若是这些情况发生，所有的链上交易者均会受到 Flashbot 交易者的威胁。因而，所有可能使 Flashbot 交易者获利的交易必须也使用 Flashbot 交易竞价，从而加速 Flashbot 交易者的得利。  
目前只有以某家破坏链上生态著称的矿池采用了 Flashbot, 但是因为 Flashbot 交易的特性，若是其他矿池不使用 Flashbot 相当于收到了损失（Flashbot 交易者拉低了平均 gas 值）。  
而且，Flashbot 交易符合共识机制，使得这种行为并不会被区块链机制限制，因此只要一个验证者在出块中所占比例够大（几个百分点就已经足够）就可以进行，而不需要进行 51% 攻击，换言之，Flashbot 是一种更弱的 51% 攻击。

## 反制

坦白来说，目前并没有有效的办法阻止 Flashbot “瘟疫”的传播，目前见到的措施都是通过利用机器人编写弱点来进行反击。

### 合约

#### 拉黑地址/扣除货币

目前已经有了利用这种方式从 Flashbot 交易者获利的野生案例，这类操作有两种实现方式：

1. 使用攻击 Flashbot 检测流动性的合约
    合约作者通过对创建者有利的代码，使得 Flashbot 交易者在交易后被锁定，从而不得不亏损以退出。
2. 拉黑出块者
    合约作者可以检测本块的出块者，并读取交易 gas 和近期平均 gas 数值，若检测到疑似 Flashbot 攻击则使交易失败，或者更进一步的使 Flashbot 用户交易被扣除。
  

但显然在 PoS 共识机制下，矿工可以通过轻易的转移出块地址来避免地址被拉黑。

#### 哈希锁定机制

简单来说，这个机制把交易市场变成了 T+n 的，从而阻止的广义的特定抢先行为，但带来的代价也十分明显。  
这个机制是从某些战略游戏里的 Tick 机制获得灵感，在这些游戏里，为了防止对方伪造延迟，所有交易者必须先交换行动的哈希值，之后再发出行动内容，从而阻止通过预知对方行动获利，若是行动和预提交哈希不一致，则证明该名玩家进行了非法操作。

### 网络

网络可以通过一定的强制交易打包或排序机制来减弱 Flashbot 负面影响，但这并不能完全解决问题。  
PoS 机制也可以提供一定程度上的抵抗力，因为投票者可以反对 FLashbot 行为，而验证者在被拉黑后几乎无法更换地址。
