---
title: 记一次被钓鱼网站盗走eth的经历
date: 2021-07-01 09:51:52
category:
- 钓鱼网站

tags:
- eth
---

#### 记一次被钓鱼网站盗走ETH的经历

我本来是要研究`NFT`(非同质化代币)的, 在研究这个过程中 有几个在线的交易平台`Nifty Gateway`、`OpenSea`、`MakersPlace`等网站可以查看一些商品属性啥的，有助于了解到底啥是`NFT`

我这边也是选择了 `OpenSea` 

之后就被盗走 0.05 个 `ETH` 700多块钱吧


#### 钓鱼网站是怎样的

于是我去 `Chrome` 浏览器 搜索 `OpenSea`

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701100243.png)

也是头晕眼花 没注意瞅 就选择了第一个搜索结果

打开网站: 

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701100549.png)

注意看这里的域名是 `www-opensea.io` 这里看不出来有啥名堂 咱们继续往下走

点击右上角小头像进行登录

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701100814.png)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701100904.png)

到这里 感觉有点不一样了 我的小狐狸明明是已经登录的状态 这里然我输入密码解锁 我也没多想解锁就解锁呗,输入密码进行解锁

解锁完之后 又让输入私钥 这网站咱也没登录过啊 可能就是需要私钥吧 我就继续输入了私钥

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701101043.png)

输入完私钥后 账户里的`eth`就没了 这个账户最好不要在使用了 因为账号和私钥已经泄露 

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701101449.png)

钱已经被全部转走了 有多少划走多少 转到了`0xAf9dE309b54198E2e37C8DB3d367967Fc20EAa9D`这个账户 我们去查一下这个账户

[骗子账户](https://etherscan.io/address/0xaf9de309b54198e2e37c8db3d367967fc20eaa9d)

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701101815.png)

这个账户在我写博客的时候 陆陆续续已经进账 1.9 个 `eth` 了  钱也找不回来了 放弃吧 !!!

#### 正常网站应该是怎样的

我们打开第二个 网站

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701102219.png)

这里的域名是 `opensea.io`

之后我们进行登录

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701102434.png)

可以看到 正常网站 如果我们钱包是登录的状态 直接是授权连接 不需要我们输入密码的 甚至密钥  

连接完之后 我们就登录进来了 

![](https://cdn.jsdelivr.net/gh/Naruto-1996/picture/images/20210701102624.png)


#### 总结

正常网站在进行登录的时候 是不需要用户提供私钥的 如果让你输入解锁密码和私钥 那你可要小心了。

如果让你提供密码和私钥才能进行登录的话 极有可能是 **钓鱼网站** 千万小心。

一旦私钥泄漏 这个账号最好不要再使用 因为这个账号已经不属于你了 及时换账号吧！




