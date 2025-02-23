---
description: 不是你的私钥，不是你的内容
---

# 7⃣ 用户安全

## 什么是助记词？

助记词是控制您整个账户的密码。助记词是无法更改的，将助记词告诉他人可能导致资金的全部丢失。

## DeSo应用程序能访问我的助记词吗？

不，如果您使用DeSo身份服务，应用程序无法访问您的助记词。

您的助记词存储在浏览器中一个高度安全的`iframe`中，与应用程序的其他部分完全隔离。交易在您的浏览器中的这个`iframe`里签名，**助记词永远不会离开您的浏览器**。

## 如何确保我的账户安全？

为了获得最大的安全性，开发者社区建议您使用移动浏览器或DeSo桌面应用访问DeSo应用程序。

移动浏览器和DeSo桌面应用都是安全的、沙盒化的环境，不容易受到第三方攻击者的破坏。

尽管DeSo应用程序通常可以在Chrome、Safari、Firefox等常规桌面浏览器中安全访问，但如果您允许它们访问DeSo应用程序，恶意浏览器扩展程序可能会窃取您的助记词。**这种情况应该很少见，但如果您拥有大量DeSo，建议您手动禁用扩展程序或使用隐身模式，这样可以自动禁用扩展程序。**

此外，如果您拥有大量DeSo，最好创建两个独立的账户：一个用于发布和关注，另一个用于存放您的代币。

## 如果我丢失了助记词怎么办？

由于助记词仅存储在您的浏览器中，因此任何第三方都无法恢复。**如果您丢失了助记词，目前唯一的选择是创建一个新账户，保存新的助记词，并将您的所有资产发送到这个新账户。**

您可能需要先将您的创作者代币兑换成DeSo。您可以先更改旧账户的用户名，然后迅速用新账户认领该用户名，从而实现用户名的转移。\
\
对于此处的不便，我们表示歉意。我们知道这个过程并不理想，但我们正在寻找替代方案，并希望未来能为用户提供更简便的恢复途径。

## DeSo身份服务如何运作？

DeSo身份服务位于[identity.deso.org](https://identity.deso.org)，它将您的敏感账户信息安全地存储在浏览器的本地存储中。为了保护私钥信息，身份服务依赖最小化，实施严格的内容安全策略，并经过多家安全公司的审计。

**目前，开发者社区不建议在identity.deso.org之外的任何地方输入您的助记词。**\
\
始终检查URL栏以确认您正在使用`identity.deso.org`

DeSo身份服务旨在让用户可以轻松、安全地使用各种社区项目。\
\
应用程序和节点可以与DeSo身份服务集成，使用户无需输入私钥信息即可注册。用户可以轻松地为每个账户授权不同级别的访问权限。
