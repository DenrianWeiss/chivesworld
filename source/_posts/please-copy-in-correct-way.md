---
title: 当你发 Altcoin 时，不该怎么做
date: 2021-04-06 15:16:26
tags: general
---

我是不太想写挂什么东西的文章，但是有些过于大脑升级的发新链行为实在是令我不敢讲话。显然，有时候有些人作出的改动还不如不改。  
另外，为了人身安全，作者表示暂时变身谜语人。

## 当你定地址时不该怎么做

每一个新链都需要一个地址规则，而有些人总是想在这件事上搞点不合适的创新。  

**如果你的地址校验规则和别的链一样，那你最好确保私钥转换到地址的规则和别的链一样。**  

我觉得这件事原因很明确，避免转丢，但是总有傻逼想要搞点创新，比如下面这位不想给出姓名的选手：

```javascript
function publicKeyToAddress(publicKey) {
  const buffer = keccak256(publicKey).slice(-20);
  buffer[0] = (buffer[0] & 0x0f) | 0x10; // eslint-disable-line no-bitwise
  return buffer;
}
```

我能理解你想模仿 Bitcoin 的 P2SH/P2PKH 标志，甚至你还做了合约标记（地址以 8 开头）。但是你的老版客户端并不进行校验，而且你的地址规则用了和 Ethereum 一样的大小写区分，导致双向转错了的币有 15/16 的概率实际上烧掉了（eth 规则生成的地址假如第一位是 1 则可以找回）。

## 当你发明机制时不该怎么做

我承认在你往智能合约里加常数或者给它改名的时候能让它看起来和某家深圳公司对软件的改造一样能掩盖复制痕迹，可你有没有想过你的 RPC 和工具链几万年没人更新了，特别是你的脚本机制和别人一样的时候。  

这一点我想我不用举出实例，因为太多了。

## 当你吹牛逼的时候不该怎么做

显然，某些人的名字或者某些饼出现在白皮书里对于我这种老韭菜本身就构成一种警告，但是好好想一想，重新发明某种机制真的显得你很高大上么？就算你故意在技术预览里忽略了之前别人发明过这东西的事实，还是有人能看出来的。  

而这种事的重灾区总是在“匿名交易”和跨链机制方面，我不认为重新发名权威证明机制的预言机是什么伟大科学发现，特别是发现在你非常 fancy，非常摩登的名字下面那个机制连吊销都没有。而重新发明依赖单向函数和曲线的零知识证明也不是什么高级东西。另外，我这里还要顺带提一嘴：如果你说你交易费用低廉，我希望这个不是对你对币值没啥希望的另一种说法。

## 结语

发行一种新空气币未必是坏事，但是重新发明故意不兼容的新“特性”就是另一回事了。有时候，你的发明的效果`可能还不如直接复制一份，我认真的。