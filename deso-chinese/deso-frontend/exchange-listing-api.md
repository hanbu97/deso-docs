---
description: Get started building on DeSo with our Javascript SDK
---

# 1⃣ 前端：开始

### 设置

如果您已经熟悉某个特定的框架，可以根据您喜欢的工具的文档创建一个项目（Create React App，Vite，Nextjs，Remix，Angular，Vue 等）。

如果您不确定，我们的[示例](https://github.com/deso-protocol/deso-examples-react)将使用[Create React App](https://create-react-app.dev/),，这是一种合理的选择，用于为快速原型设计搭建开发环境。

您可以在 Create React App  [入门页面](https://create-react-app.dev/docs/getting-started)上找到系统要求和安装步骤。\
\
请注意，CRA 的默认脚手架使用的是原生 JavaScript。

如果您熟悉 TypeScript 并且更喜欢使用它，请查看[这个章节](https://create-react-app.dev/docs/getting-started#creating-a-typescript-app)以开始使用。不然的话，使用原生 JavaScript 是一个合理的选择。\
\
当您使用默认脚手架使您的应用运行起来后，我们将安装 DeSo 身份包并查看一些示例。

### 安装

[DeSo 协议 SDK](https://www.npmjs.com/package/deso-protocol) 用于构建 DeSo 区块链应用程序的核心功能：登录、登出、签名和提交交易等。\
\
NPM: [https://www.npmjs.com/package/deso-protocol](https://www.npmjs.com/package/deso-protocol)\
Github: [https://github.com/deso-protocol/deso-workspace/tree/main/libs/deso-protocol](https://github.com/deso-protocol/deso-workspace/tree/main/libs/deso-protocol)

\
在应用程序的根目录下运行：

```
npm i deso-protocol
```

现在，您应该准备好开始构建了！

## 配置

根据您的应用需求，更改身份库进行的操作

### 交易支出限制选项

设置交易支出限制选项将决定用户在登录您的应用时会看到什么权限，以及您的应用可以代表他们花费多少 DeSo。

在 DeSo 上，每个用户在首次创建帐户时都会为他们生成一个主要或_**owner**_**所有者**密钥对。

所有者密钥可以代表用户签署_**任何**_交易，包括花费他们_**所有**_的钱，以及转移他们_**所有**_的 NFT 或代币！

让用户与之交互的每个应用都可以直接访问这个密钥显然不是一个好主意。但我们也不想让用户不得不手动批准应用要求他们执行的每一笔交易。

想象一下，如果每个喜欢和评论都需要一个烦人的批准弹出窗口，您会怎么使用 Twitter？&#x20;

解决方案是允许应用生成具有有限权限集的**子密钥**或**派生密钥**，这些权限由所有者_**owner**_**密钥**批准。\
\
这看起来是这样的：

* 用户首次创建帐户，生成一个存储在 DeSo 身份中的_**owner**_**所有者**公钥/私钥对（存储在浏览器中的本地位置，但位于与应用无法访问的不同域上）。\\
* 用户通过输入手机号码或购买一些 $DESO 代币来获得一些启动 $DESO 代币以支付燃料费。\\
* 应用程序生成一个_**派生密钥**_，这可以是任意随机密钥对，并生成一个赋予此派生密钥特定权限的交易，例如代表用户发表 3 次帖子。
* 一旦生成了派生密钥批准交易，DeSo Identity 钱包可以提示用户_**批准**_它，从而使用用户的_**所有者**_公钥对交易进行签名。\\
* 一旦批准交易由用户的所有者公钥签署，它可以_**广播**_ 到 DeSo 区块链，然后赋予派生密钥所需的权限。\\
* 应用程序可以代表用户愉快地签署交易，而用户不必担心应用程序窃取他们的资金（也称为被 **rug-pulled** 或 **rugged**）。

在此示例中，我们将要求用户允许创建无限数量的帖子，并进行无限次转账，直到他们达到 1 $DESO 的全局限制。

```
import { configure } from 'deso-protocol';

configure({
  spendingLimitOptions: {
    // 注意：此值以 DeSo 纳诺为单位，因此 1 DeSo * 1e9
    GlobalDESOLimit: 1 * 1e9 // == 1 Deso
    // 交易类型到派生密钥允许代表所有者公钥执行此操作的次数的映射
    // allowed to perform this operation on behalf of the owner public key
    TransactionCountLimitMap: {
      BASIC_TRANSFER: 'UNLIMITED', // 发送/接收 DESO 是 "basic transfer"基本交易
      SUBMIT_POST: 'UNLIMITED',
    },
  }
});
```

**重要提示**：在调用任何其他身份方法之前，您需要确保只调用一次 configure。

请注意，尽管我们在上面的 configure() 调用中批准了无限次**基本转账**交易，但我们在再次弹出批准之前无法从用户钱包中获取超过 1 $DESO。

这对用户来说是一件好事！

虽然您通常希望在生产应用程序中请求特定权限，但可以请求无限制访问以进行原型设计或快速测试。

此示例请求无限制访问权限：

```
import { configure } from 'deso-protocol';

configure({
  spendingLimitOptions: {
    IsUnlimited: true
  }
});
```

### 应用名称

appName 用于标识授权派生密钥的应用程序。

这可以用于对由特定应用程序发出的派生密钥进行分组和识别。如果您不设置 appName，默认情况下将使用您的应用程序运行的域名。

```
import { configure } from 'deso-protocol';

configure({
  appName: 'My Cool App',
  spendingLimitOptions: {
    IsUnlimited: true
  }
});
```

就这样，用户将能够轻松查看他们使用过的所有应用程序以及授权给它们的权限（这就是为什么设置一个好的应用名称很有帮助！）。

用户还将能够从一个统一的仪表板中禁用权限。

### 下一步

接下来，我们将查看一些[常见场景的基本示例](https://github.com/deso-protocol/deso-examples-react)。
