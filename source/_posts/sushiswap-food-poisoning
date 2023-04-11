---
title: 寿司大厨，食物中毒
date: 2023-04-11 15:01:14
tags: 
---

显然，不太卫生的日料并不是中国特产，现在轮到 SushiSwap 的用户食物中毒了。  

## 发生甚么事了

TLDR: SushiSwap 的路由合约没有对用户输入作合适的检查，导致黑客可以从用户账户中转出任意已授权代笔。

## 超标沙门氏菌

在[这笔](https://etherscan.io/tx/0xea3480f1f1d1f0b32283f8f282ce16403fe22ede35c0b71a732193e56c5c45e8)[攻击交易中](https://etherscan.io/address/0x000000C0524F353223D94fb76efab586a2Ff8664),
攻击者调用了 SushiSwap 的 `processRoute` 函数，该函数是一个普通的swap函数，允许用户自行传入兑换路径（并不一定要求是特定swap），之后我们一路前进到 `swap` 调用，在其中黑客提供的路由选择了 `swapUniV3` 函数来处理swap。  
处理路由的代码如下：
```solidity
function swapUniV3(uint256 stream, address from, address tokenIn, uint256 amountIn) private {
    address pool = stream.readAddress();
    bool zeroForOne = stream.readUint8() > 0;
    address recipient = stream.readAddress();

    lastCalledPool = pool;
    IUniswapV3Pool(pool).swap(
      recipient,
      zeroForOne,
      int256(amountIn),
      zeroForOne ? MIN_SQRT_RATIO + 1 : MAX_SQRT_RATIO - 1,
      abi.encode(tokenIn, from)
    );
    require(lastCalledPool == IMPOSSIBLE_POOL_ADDRESS, 'RouteProcessor.swapUniV3: unexpected'); // Just to be sure
  }
```
  
注意到函数体的第一行和第五行，这两行将 `lastCalledPool` 设为了一个用户输入的值，允许一个用户传入的合约作为任意回调函数的调用者，让我们来看看在 SushiSwap 中 `lastCalledPool` 是如何被使用的：
```solidity
function uniswapV3SwapCallback(
    int256 amount0Delta,
    int256 amount1Delta,
    bytes calldata data
  ) external {
    require(msg.sender == lastCalledPool, 'RouteProcessor.uniswapV3SwapCallback: call from unknown source');
    lastCalledPool = IMPOSSIBLE_POOL_ADDRESS;
    (address tokenIn, address from) = abi.decode(data, (address, address));
    int256 amount = amount0Delta > 0 ? amount0Delta : amount1Delta;
    require(amount > 0, 'RouteProcessor.uniswapV3SwapCallback: not positive amount');

    if (from == address(this)) IERC20(tokenIn).safeTransfer(msg.sender, uint256(amount));
     else IERC20(tokenIn).safeTransferFrom(from, msg.sender, uint256(amount));
  }
```

看起来没什么问题是吗？注意函数体的第三行，它从回调合约提供的数据中读出来付费者地址，然后将代币转移到该地址。

## 嘬酱油瓶儿

现在我们已经凑齐了发生灾难的所有条件，接下来就是引发它了。我们要怎样做呢？
1. 路由合约中的 `lastCalledPool` 变量可以被用户控制，这个被传入的合约可以调用回调函数。
2. 这个回调函数并没有检查被转款的人是提交swap交易的用户。
3. 这时我们作一笔route是我们写好合约的swap，转出地址设为随机一个倒霉蛋，bingo。

## 教训

永远检查输入，无论是进到你店里的顾客，还是你吃下去的食物，或者你合约的输入。
毕竟没人知道来的东西是不是在回转寿司嘬酱油瓶发抖音的贵物还是黑客。
