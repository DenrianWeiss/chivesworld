---
title: 手把手教你发行空气币 - ERC20 篇
date: 2021-04-01 21:55:08
tags: erc20
---

自从以太坊这类提供合约能力的项目出现后，发行空气币的成本再度降低了。现在想要发行一个空气币已经不再需要去矿池购买算力了。我们只需要一个钱包，一个浏览器和一点编造白皮书和搭建简易官方网站的能力，下面，我将手把手教你发行一个 ERC20 空气币，而这只需要你学会一点英语。

## 第一步：选择你的英雄

首先，要想发行空气币，我们要选择一个充满了容易骗的韭菜的“智能”链。  
我们可用的选择有老朋友以太坊、孙割的 Tron（不推荐，待会告诉你为什么。[^1]）、杜先生的 Heco 等。  
这里我们以以太坊为例。  
在选定你的区块链之后，接下来我们需要的就是一个用来支付合约费用的钱包，我在这里用的是 Metamask，ImToken 之类大概也行罢。

## 第二步：购买发币用的币

部署智能合约是要消耗区块链资源的，因此我们需要给矿工准备点币来把合约发到网上。在这方面，你可以随便找个交易所买点然后提出来，我觉得一个经验丰富的韭菜应该很懂这步怎么做，因此不再赘述。

## 第三步：下载 OpenZepplin 的模板

让我们拿出浏览器，打开这个[页面](https://github.com/OpenZeppelin/openzeppelin-contracts)，点击右上角那个大大的**下载**，或者如果你稍微聪明一点，也可以用 git 把它拉下来，之后，拿你的文件管理器打开那个文件夹。

## 第四步：打开你的发币工具

对于大部分人来说，最好用的发币工具就是以太坊官方的 [Remix IDE](https://http://remix.ethereum.org/)。  
打开那个网页之后，我们还需要导入 OpenZepplin 的空气币模板。  
这里推荐用 `remixd` 打开你本地的 OpenZepplin 合约模板文件夹：用 `npm` 安装 `remixd` 后(`npm install -g @remix-project/remixd`)[^2]，在你的 cmd/powershell/bash 里输入这么个命令启动它：`remixd -s <你下载的 OpenZepplin 模板路径>`[^3]。  
然后设置 Remix 连接 remixd：
![](/images/erc20-remix-import.png)
点击上面那张图界面里的那个下拉框，选择 `connect to localhost`，再点 connect 。
之后我们就来到了编辑界面，打开 `contracts/token/ERC20/ERC20.sol`,这里我们需要做一点改动。  
为了给我们自己发空气币，我们需要允许自己给自己生成空气币，因此需要改一下创建函数：  
1. 在第 35 行加上这么一句： 

```solidity
  address private _owner;
```

2. 在 `constructor` 下面那两行后加上这么一句：

```solidity
  _owner = msg.sender;
```

3. 拖到文件底部，在最后一行那个花括号前面加上这么个东西：

```solidity
    function Mint(uint256 amount) public {
        require(msg.sender == _owner);
        _mint(msg.sender, amount);
    }
```
  
这样，我们的空气合约就改好了，接下来就是把它发到网上！

## 第五步：把空气币发上网

我们点击界面左边的这个图标：  
![](/images/remix-compile.png)  

然后在弹出的界面里点击内个 `Compile ERC20.sol` 按钮，之后等一小会儿。  
然后点击右边的第三个图标，上面有一个 `ENVIRONMENT` 复选框，解锁你的钱包之后选择 Injected Web3，等待弹出提示连接。  

之后点 `DEPLOY` 旁边下箭头，在上下两个框里写上你的空气币的名字和符号，填完之后点 `transact`。 你的高级空气币就上网啦！

## 第六步：给你自己发币，并且开始宣传！

这一步我是没什么想法，您可自行发挥智慧！

## 脚注

[^1] 孙割的 Tron 自主可控版 solidity 编译器属实有亿点点古老，导致我们用来发币的 openZepplin 合约编译不了。
[^2] 安装 npm 的方法自己网上找。
[^3] 如果你不懂这些是啥，去搜索啊！