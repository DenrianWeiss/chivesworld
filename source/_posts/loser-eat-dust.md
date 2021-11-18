---
title: 输的吃土 - 盲盒的不正确开盒方式
date: 2021-11-17 01:14:51
tags: solidity
---

有些人总喜欢玩触发时生成随机数的玩法，那就别怪别人玩输的吃土了。  
本文是 RACA 盲盒被人用智能合约输的吃土玩成明盒的事故复盘。

## 事故现场

[BSCScan](https://bscscan.com/address/0xec06e634415ab8d903e1ee6b4f314be99fd56a3b)

## 发生了啥

由于 RACA 的智能合约在[开盲盒](https://bscscan.com/address/0x4eb7ddd1b0103349fd289c5a4e4b95a0cc68d19e)的时候才决定盲盒内容(自己用 Panoramix 看)，
因此我们的攻击者搞了个非常简单的办法弄死了这个盲盒：只要不断的开盒，大奖就会在眼前延伸，不要停下来啊！

## 具体代码

```python
def unknown3829d282(uint256 _param1): # not payable
  require calldata.size - >=′ 32
  if not stor1[caller]:
      revert with 0, 'white only' # 限制只有白名单里的人能用这合约
  require ext_code.size(0x4eb7ddd1b0103349fd289c5a4e4b95a0cc68d19e)
  call 0x4eb7ddd1b0103349fd289c5a4e4b95a0cc68d19e.open(uint256 param1) with: # 开盒合约
       gas gas_remaining wei
      args _param1
  if not ext_call.success:
      revert with ext_call.return_data[0 len return_data.size]
  require ext_code.size(0x61c6eeca7b14cf4ec1b190dd879008dd7d7e470) 
  static call 0x61c6eeca7b14cf4ec1b190dd879008dd7d7e470.balanceOf(address tokenOwner) with:
          gas gas_remaining wei
         args this.address
  if not ext_call.success:
      revert with ext_call.return_data[0 len return_data.size] # 查询大奖的NFT余额
  require return_data.size >=′ 32
  if ext_call.return_data[0] != 1:
      revert with 0, 'no valid'
```

## 攻击过程

从上面合约代码可以看出，攻击者操作十分简单，不断重复开盒，如果不是大奖则输的吃土，撤销交易。又因为原合约可以重复开盒改变结果，所以盲盒变明盒

## 结论

在区块链这种能撤销交易的环境下，不要在交易的那一刻决定随机数结果，请集中决定盲盒结果（这样反复开盒就成了浪费Gas），或是多少个周期集中派发一次。  
永远记住，EVM上单个交易是可撤销的。
