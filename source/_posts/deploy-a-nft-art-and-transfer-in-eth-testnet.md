---
title: deploy a nft art and transfer in eth testnet
date: 2021-07-30 16:57:44
category:
- NFT
tags:
- eth
- NFT
---

### 如何在以太坊测试网络上发布一个NFT艺术品并转给他人

#### 1、首先什么是NFT

> 非同质化代币NFT是一种数字资产，具备不可分割和独一无二这两大特性，可用于代表有形和无形的物品。NFT应用场景多样，包括数字收藏品、音乐、艺术品以及游戏内代币。

#### 2 、什么是ERC-721

ERC 代表 Ethereum Request for Comment，721 是提案标识符号。 ERC 是以太坊生态系统中的应用级标准，可以是 ERC-20 等代币的智能合约标准，ERC 的作者负责与以太坊社区建立共识，一旦提案得到以太坊社区的审查和批准社区它成为一个标准。您可以在此处跟踪最近的 ERC 提案。创建 ERC-721 是为了提出在智能合约中跟踪和传输 NFT 的功能。

ERC-721 是一个开放标准，描述了如何在 EVM（以太坊虚拟机）兼容的区块链上构建不可替代的代币；它是不可替代代币的标准接口；它有一套规则，可以很容易地使用 NFT。 NFT 不仅是 ERC-721 类型；它们也可以是 ERC-1155 代币。


#### 3、我们还需要知道一个东西 `IPFS`

> 星际文件系统（InterPlanetary File System，缩写为IPFS）是一个旨在创建持久且分布式存储和共享文件的网络传输协议。它是一种内容可寻址的对等超媒体分发协议。在IPFS网络中的节点将构成一个分布式文件系统。它是一个开放源代码项目，自2014年开始由协议实验室在开源社区的帮助下发展。其最初由Juan Benet设计。

如果要发布一个NFT艺术品,我们需要定义这个艺术品的属性，通常我们用`json`文件来定义，我们还需要一个链接可以查看这个艺术品

随便创建一个xxx.json

```json

{
"name": "NFT Art",
"description": "This image shows the true nature of NFT.",
"image": "https://ipfs.io/ipfs/QmQLzfy3Zs9S6nV4HG9to9wntbdaoJgC41wgEQMJwm6gBK",
}

```

* name 艺术品的名称

* description 艺术品的描述

* image 图片的链接

这里我们就定义了一个简单的json文件来表示这个艺术品 
`IPFS` 是一个去中心化的平台 我们将json文件 图片上传到这个平台 可以拿到这样的链接

**下面我们来说一下如何使用`IPFS`**

[IPFS官网地址](https://ipfs.io/)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730175202.png)

我这里选择的是 桌面应用程序 可视化界面 好操作一些 

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730175424.png)

打开之后 选择导入 我们选择创建的xxx.json 和 一张图片也好 gif也行

然后我们点击三个点 选择 分享链接 复制一下链接就行了

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730175647.png)

当然 你也可以不选择`IPFS` 只要提供的链接能访问到就行。用IPFS的原因是去中心化 如果是你自己提供的链接那就可能不是去中心化了


这里我给出我的两个链接
```solidity

json字符串 
https://ipfs.io/ipfs/QmZJEQLGYSMoVMmzVrkujsPiydZiWbHHGFZQLvf8J79s8h?filename=apple.json

图片链接
https://ipfs.io/ipfs/QmQLzfy3Zs9S6nV4HG9to9wntbdaoJgC41wgEQMJwm6gBK?filename=spin.gif

```

后面的`?filename=apple.json`可以删掉也能访问到


#### 4、编写智能合约

编写NFT合约代码的话 我们可以选择一些非常成熟的库在他们的基础上进行编写自己的NFT代码 比较好的库就是`OpenZeppelin`

[github地址](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/master/contracts/token/ERC721/extensions)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730172037.png)

他这里是提供了很多个不同功能的NFT代码给我们， 我们只需要选择红色框那个来继承就行了，这个是比较简单的， 没有别的复杂功能。

#### 5、开始编写代码

这里我们选择在线的`Remix`来编写我们的合约代码 

[Remix中文版地址](http://remix.app.hubwiz.com/#optimize=false&runs=200&evmVersion=null&version=soljson-v0.7.4+commit.3f05b770.js)

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721URIStorage.sol";

contract MobilePhoneNft is ERC721URIStorage{

  uint public counter;

  constructor() ERC721("MobilePhoneNft", "MPN"){
    counter = 0;
  }

  function createNFTs (string memory tokenURI) public returns(uint) {
    uint tokenId = counter;

    _safeMint(msg.sender, tokenId);
    _setTokenURI(tokenId, tokenURI);

    counter ++;
    return tokenId;
  }


  function burn(uint256 tokenId) public virtual {
    require(_isApprovedOrOwner(msg.sender, tokenId), "You can not burn it, because you are not the owner!");
    super._burn(tokenId);
  }
  
  
}

```

我们讲一下这个代码 

`createNFTs` 创建一个NFT 我们调用的时候需要传入一个参数 这个参数是一个地址代表你这个`NFT`的一些属性 这个地址是一串`json`字符串

[https://ipfs.io/ipfs/QmZJEQLGYSMoVMmzVrkujsPiydZiWbHHGFZQLvf8J79s8h](https://ipfs.io/ipfs/QmZJEQLGYSMoVMmzVrkujsPiydZiWbHHGFZQLvf8J79s8h)

`json` 字符串内容如下:

```json

{
"name": "NFT Art",
"description": "This image shows the true nature of NFT.",
"image": "https://ipfs.io/ipfs/QmQLzfy3Zs9S6nV4HG9to9wntbdaoJgC41wgEQMJwm6gBK",
}

```

tokenId 我们使用`counter ++` 自增 初始值为0

有一个`burn`的方法 当你调用完`createNFTs`创建了一个NFT但是你并不满意时 可以调用这个方法来烧毁这个`NFT`。

#### 6、合约部署

编译一下我们的合约

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730180438.png)

如果没有问题 就可以部署了

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730180645.png)

我这里是部署到 Rinkeby 测试网络 部署完成之后 我们就调用一下 合约的方法 `createNFTs`

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730180909.png)

填入上面那个 `json` 的链接 正常付gas费就行了 

之后我们在`https://rinkeby.etherscan.io/`测试网络上查看 我们的账户就可以看到那个nft合约了 不过我们看不到图片

如果想看到图片的话 需要借助`openSea` 它也是支持了`Rinkby`测试网络的

[Open Sea Rinkby 测试网络](https://testnets.opensea.io/account)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730181750.png)

#### 7、将这个NFT转给他人

我们只需要调用 这个方法就行了 这个方法是 ERC-721 提供的方法

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730182229.png)

tokenId 在rinkeby测试网可以查看 我这里是 0

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210730182401.png)



