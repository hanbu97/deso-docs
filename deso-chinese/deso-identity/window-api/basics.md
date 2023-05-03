---
description: Guide on integrating with the DeSo Identity Window API
---

# 概述

在本指南中，我们将解释如何将 DeSo Identity 窗口 API 集成到您的应用中。&#x20;

在开发处理用户身份验证的应用程序时，总是有一些需要用户交互的操作。&#x20;

在传统的 Web2 应用程序中，这通常涉及用户输入用户名和密码等信息，或用户点击第三方登录，如 Google 或 Apple。&#x20;

另一方面，去中心化的 Web3 应用程序由区块链驱动，因此它们依赖于公钥和私钥。

区块链的底层数学原理通常对用户和开发者来说都很复杂。&#x20;

为解决这些问题，我们设计了 DeSo 身份服务，它将用户身份验证的加密复杂性隐藏在一个简单的窗口 API 下。&#x20;

使用 DeSo 身份服务，您无需担心实现任何 HTML 表单或输入验证，您只需要正确调用窗口 API。&#x20;

窗口 API 负责将身份作为弹出窗口或新标签页打开。在窗口上下文中打开身份就像启动 `window.open` 一样简单。&#x20;

以下是一些可以调用身份服务的示例：

```javascript
const login   = window.open('https://identity.deso.org/log-in');
const signUp  = window.open('https://identity.deso.org/sign-up');
const logout  = window.open('https://identity.deso.org/logout?publicKey=BC123...');
const approve = window.open('https://identity.deso.org/approve?tx=0abf35a...');

// 可以添加到任何路径以获取测试网络deso 和比特币地址
const testnet = window.open('https://identity.deso.org/log-in?testnet=true');
```

以下是在使用几个 URL 参数启动`log-in`登录接口后的示例用户界面，本例中`accessLevelRequest=4`

您可能还记得我们在入门指南中的[#access-levels](../identity/concepts.md#access-levels "mention") 部分提到过 `accessLevelRequest` URL 参数。

本文档将解释您可以使用的其他 URL 参数。

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Window API 示例</p></figcaption></figure>

在启动窗口上下文后，用户需要执行一个操作，例如登录或注册。

在用户完成流程后，您的应用将通过`postMessage`或`callback`回调接收包含安全用户凭证和所需接口返回体的信息。

然后，您可以使用用户凭据与 [iframe-api](../iframe-api/ "mention")通信。我们建议使用以下格式打开Identity窗口上下文：

```javascript
// 将窗口居中。
const h = 1000;
const w = 800;
const y = window.outerHeight / 2 + window.screenY - h / 2;
const x = window.outerWidth / 2 + window.screenX - w / 2;
const win = window.open("https://identity.deso.org/log-in", null, `toolbar=no, width=${w}, height=${h}, top=${y}, left=${x}`);
```

当我们将公钥传递给Identity窗口API时，它应始终采用**base58check**格式：

```javascript
BC1YLgwkd7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ
```

**注意**：<mark style="color:red;">一次只能打开一个Identity窗口。</mark>

## Messages消息

通常，DeSo Identity服务将通过 [`postMessage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)与您的应用程序通信。

要监听这些消息，您需要在父窗口上下文中声明事件处理程序，如下所示。

Identity发送的第一个消息是`method:`"`initialize`"，您的应用需要发送响应。

我们在 [#events](../identity/concepts.md#events "mention") 和 [#initialize](../identity/concepts.md#initialize "mention") 指南中详细解释了这个机制。

```javascript
window.addEventListener("message", (event) => this.handleMessage(event));
```

当用户在窗口上下文中完成操作时，Identity 将向您的应用发送包含与启动接口相对应的请求体的 `postMessage`。

这些消息通常包含一个方法："`login`" 字段，以便更容易处理关闭身份窗口，如 [#termination](./#termination "mention") 章节中所述。&#x20;

然而，有些接口将包含不同的方法，例如 [#derive](./#derive "mention") 接口。

由窗口 API 发送的消息还将包含一个`id: null`字段，表明它们不需要您的应用程序响应。

以下是来自`log-in`登录接口的 `event.data`返回体示例。

```javascript
{
  id: null,
  service: "identity",
  method: "login",
  payload: {
    users: {
      BC1YLj8iwsicimv8ttrPg6rBtizvM7X3KCsiVQwYZKqj6Wj3rT8TD3D: {
        hasExtraText: false,
        btcDepositAddress: "16YqLEpYzeV1aCcp7YwHKh9m5Kg4Sd8eak",
        ethDepositAddress: "0xf8212f8d8E881090653bFF0AC99f499063bCFE77",
        version: 1,
        encryptedSeedHex: "0ac1d9e14c640a26ea3f70095f9b27a50ef06652aea7a2f19f086d750e5f4ecf2d752568b9e3fd3400ad8581d9cf8da5dab3ea29078c1a528f81a51b55514ed5",
        network: "mainnet",
        accessLevel: 4,
        accessLevelHmac: "d66741dcf7a828e7b2b3c44708643de335de28b7a4941eeb687a6d5b1da66e77"
      }
    },
    publicKeyAdded: "BC1YLj8iwsicimv8ttrPg6rBtizvM7X3KCsiVQwYZKqj6Wj3rT8TD3D",
    signedUp: true
  }
}
```

大多数窗口上下文的响应都具有与上述类似的结构

典型的响应将包含`users`用户对象，该对象反映了所有先前登录到您的应用的用户。

用户对象是一个 `publicKey => userCredentials` 的映射，其中凭据主要用于与 `iframe` 上下文通信。

在此类请求中，我们将传递 `encryptedSeedHex`、`accessLevel`、`accessLevelHmac`，这些由 DeSo Identity 用于确保用户通过窗口上下文安全地进行身份验证。

用户凭据应保存在本地存储中。

在收到方法："`login`" 响应时，您应始终覆盖现有凭据。

这是因为某些请求可能导致 Identity 重新授权所有用户，他们的凭据（如 `encryptedSeedHex`）将发生变化。

在 DeSo 协议实现中，我们通过 `/src/app/global-vars.service.ts` 中第[ #857](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/global-vars.service.ts#L857) 行的 `this.backend.setIdentityServiceUsers()` 使用 [`localStorage` API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 存储数据。

如果您不熟悉 `localStorage`，以下是您需要的三个主要 API 调用。

在参考实现中，我们简单地将整个 `payload.users` 字段存储在名为`IdentityUsersKey = "identityUsersV2"`的单个键下。

```javascript
// Store dat使用（键, 值）=（IdentityUsersKey，users）将数据存储在 localStorage 中
// 我们假设用户值是一个 JSON 对象，所以：

localStorage.setItem(IdentityUsersKey, JSON.stringify(userCredentials));

// 从 localStorage 中读取 publicKey 处的数据
const users = JSON.parse(localStorage.getItem(IdentityUsersKey));

// 从 localStorage 中删除 publicKey 处的数据
localStorage.removeItem(IdentityUsersKey);
```

除了 `users` 字段外，响应还将包含特定于请求的 API 接口的字段。

当我们调用`log-in`登录接口时，响应还包含了已登录用户的 `publicKeyAdded` 和表示用户已注册的 `signedUp`。

有关这些字段的更多信息可以在 [#endpoints](./#endpoints "mention") 章节中找到。

## Callbacks回调

如果您正在构建一个移动应用程序，您可能更愿意使用回调而不是消息。

使用`callback`回调 URL 启动窗口上下文会导致 Identity 窗口通过 URL 参数而不是 `postMessage` 发送请求体。

回调机制类似于移动应用中通常如何实现 OAuth。

回调旨在与派生密钥一起使用，因此它们目前适用于[#derive](./#derive "mention") 和[#get-shared-secrets](./#get-shared-secrets "mention")接口。

由于这些端点的响应包含敏感用户信息，所提供的**`callback`回调 URL 应始终是本地地址**。

以下是带有 `auth://derive` 回调 URL 的 `derive` 接口示例调用：

```javascript
const derive = window.open('https://identity.deso.org/derive?callback=auth://derive');
```

在用户完成 Identity 流程后，请求体将通过 URL 参数以 GET 请求的形式发送到提供的回调，如下所示：

```javascript
GET auth://derive?param1=value1&param2=value2&...
```

其中 `param1=value1` , `param2=value2` 字段将与`/derive`接口响应体的结构相匹配，如[#derive](./#derive "mention") 章节中所阐述的那样。

## Termination终止

当用户在 Identity 窗口中完成任何操作时，会发送`method: "login"`消息或者调用`callback`回调。

在从所需的 API 接口收到请求体后，您应该通过在窗口对象上调用 `window.close()` 来关闭 Identity 窗口。

当 Identity 窗口打开时，您还可以像与 iframe API 通信一样与它通信。这有时候在您想要签署交易或生成 JWT 时非常有用。
