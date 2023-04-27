---
description: Fundamentals of working with the DeSo Identity Service
---

# 核心概念

## 基础知识

在本节中，我们将探讨 DeSo 身份服务在基于 Web 的集成中的应用。如果您是 iOS 或 Android 开发人员，本指南仍然有用。

I此外，我们建议在之后阅读我们的 [mobile-integration.md](mobile-integration.md "mention")指南。

基于 Web 的应用程序与身份服务有两种交互方式：

* 在[`iframe`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) 中嵌入身份服务
* 将身份服务作为[`window`](https://developer.mozilla.org/en-US/docs/Web/API/Window/open)窗口打开

在大多数情况下，Web 应用程序将同时使用这两种上下文。

关于身份服务 API 的一个一般性经验法则是，iframe 用于所有后台请求，如交易签名和消息解密 —— 而窗口上下文用于需要用户交互的请求，如登录、注册和帐户管理。

### 事件

`iframe` 和窗口上下文都通过 [`Window.postMessage()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)向您的应用程序发出消息事件以进行通信。

要开始监听这些消息，我们需要向父窗口添加一个监听器，例如实现中的[#38](https://github.com/deso-protocol/frontend/blob/main/src/app/identity.service.ts#L38) 行。

```javascript
window.addEventListener("message", (event) => this.handleMessage(event));
```

这里，`this.handleMessage(event)`是由开发人员定义的用于处理传入消息逻辑的函数。

这个处理器函数可能是您在与身份服务交互时必须编写的最重要的代码片段。

当第一次使用身份服务时，我们建议使用简单的`console.log(event)`来了解它的工作原理。

`event.data` 字段将包含 DeSo 身份服务发送的消息的有效载荷。&#x20;

### 初始化

当打开身份服务时，它发送的第一条消息是`initialize 初始化`。这条消息会在 `iframe` 和`window`窗口上下文中发送，并且需要响应。

在本节中，我们假设我们已经打开了 iframe 或窗口上下文中的一个，但现在不用担心。

每个上下文都有一个专门的指南，详细解释其内部运作，稍后我们将讨论这个问题。以下是您的事件处理器在`initialize`初始化时接收到的`event.data` 示例。

以及在实现中第 [#226](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L226) 行的相应处理逻辑。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {},
  method: 'initialize',
}
```

以上四个字段在大多数身份服务消息中都存在。让我们更仔细地看一下它们各自的作用：

* `id` 采用 [UUID v4](https://en.wikipedia.org/wiki/Universally\_unique\_identifier#Version\_4\_\(random\)) 格式，用于标识身份服务的请求/响应。\\
* service 字段在每条消息中都设置为 '`identity`'，并应在事件处理器中检查（像[这样](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L214)），以确保消息来自 DeSo 身份服务。\\
* payload 字段将包含身份服务发送的数据，如用户信息、已签名的交易等。在`initialize`初始化消息中，它被留空。\\
* `method` 字段描述发送的消息，在我们的示例中为 `'initialize'`。\\

DeSo 身份服务**要求**基于 Web 的应用程序对`initialize`初始化消息进行响应。

无论我们使用 `iframe` 还是`window`，响应都可以通过直接响应消息事件来发送，例如在 [#187](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L187) 行和 [#282](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L282) 行所示：

```javascript
event.source.postMessage({ 
    id: '21e02080-0ef4-4056-a319-a66403f33768',
    service: 'identity',
    payload: {},
}, "https://identity.deso.org");
```

id`id`字段应设置为与`initialize`初始化消息中存在的 `id` 值相匹配。

在上面的代码片段中，我们将 `id` 字段设置为字符串，但您应在代码中设置 `id: event.data.id`。

您可能已经注意到，在上面的示例中，我们在`postMessage()`调用的末尾添加了字符串 `"https://identity.deso.org".`

这指定了目标源，或 `postMessage` 的目标 URL。

我们也可以传递通配符 "\*" 来接受任何 URL，这样做安全性较低，但为简单起见，我们将在整个文档中使用它。

然而，我们建议设置`"https://identity.deso.org"`以获得更好的安全性。

关于消息格式的一些快速说明：

* 具有 `id` 和 `method` 的消息是期望响应的请求（如`initialize`初始化）。
* 具有 `id` 而没有 `method` 的消息是对请求的响应。
* 没有 `id` 的消息不期望响应。

在阅读[window-api](../window-api/ "mention")和[iframe-api](../iframe-api/ "mention")时，请牢记这一点。

## 账户

DeSo 身份服务实现了诸如登录、注册和注销等账户管理功能。身份服务处理与区块链账户相关的所有加密相关的复杂处理。

此外，我们在使用 DeSo 身份服务时，无需处理密码或任何敏感数据。

所有与账户相关的操作都是通过`window`窗口上下文 API 执行的。

现在，我们将仅专注于账户管理的一般逻辑，每个 API 接口的详细信息在[window-api](../window-api/ "mention")的指南中给出。

账户工作流程涉及以下步骤：

1. 应用程序使用 `/log-in`  [API接口](../window-api/#log-in)在`window窗口`上下文中[打开](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L99)身份服务。
2. 当用户完成登录流程时，身份服务发送包含 [`PublicUserInfo`](https://github.com/deso-protocol/identity/blob/f211503ea2420cc6e75e48683670d278cc152d8c/src/types/identity.ts#L20) 的响应，该响应将在与 `iframe` 上下文交换消息时使用。
3. 应用程序[关闭](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L196)身份服务窗口，并将步骤 2 中的 `PublicUserInfo` 存储到[本地存储](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/global-vars.service.ts#L857)/数据库中。
4. 应用程序在将消息发送到 `iframe` 上下文以处理交易[签名](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L125)、消息[解密](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L144)和其他方法时使用 `PublicUserInfo`。
5. 应用程序可能会启动更多窗口上下文以处理其他需要用户交互的操作。

此通信模式涵盖了与 DeSo 身份服务的几乎所有交互。

在步骤#2中，我们提到了神秘的 `PublicUserInfo`。其中包含了三个重要字段，包含了用户安全凭据：

* `encryptedSeedHex` 用于在`window`窗口和 `iframe` 上下文之间验证用户帐户
* `accessLevel` 和 `accessLevelHmac` 信息用于验证用户授权给您的应用程序的权限

在处理用户帐户创建或登录时，您总是需要显式设定访问权限，因此我们接下来专门为它们分配了一个完整的章节来进行说明。

### 访问等级

在处理 DeSo 身份窗口上下文中的用户帐户时，您应该总是在请求中添加 `accessLevelRequest` URL 参数，如下所示：

```javascript
window.open("https://identity.deso.org/log-in?accessLevelRequest=4", null);
```

DeSo 协议的实现在处理 URL 参数时使用 `params` 变量，`accessLevelRequest` 逻辑可以在[#85](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L85) 行找到。

访问级别请求的范围从 `0` 到 `4`，决定了用户授权您的应用程序进行的操作。

更高的权限级别意味着更多的授权操作。

可用的访问级别是：

```javascript
enum AccessLevel {
  // 用户撤销权限
  None = 0,

  // 所有交易都需要批准。
  // 这意味着没有授权任何帐户操作。
  ApproveAll = 2, /* 默认 */

  // 需要批准购买、发送和出售
  // 这将授权所有非支出操作。
  ApproveLarge = 3,

  // 节点可以在不经批准的情况下签署所有交易
  // 这将授权所有非支出和支出操作。
  Full = 4,
}
```

或者，这里是等级 2,3,4（向下递增）在 DeSo 身份 UI 中的样子：

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption><p>DeSo身份等级UI</p></figcaption></figure>

访问级别决定了哪些操作需要用户的批准。

Approval批准在这里意味着我们需要在`window`窗口上下文中启动身份服务，以便用户可以查看并手动确认交易。

批准机制在[#approve](../window-api/#approve "mention")中有更详细的解释。只有在您的应用程序真正需要它时，您才应该要求访问级别 4。

通常，您应该刻意请求符合应用程序要求的最低权限级别。

要查看每个操作的 accessLevel 要求的详尽列表，请查阅我们的[iframe-api](../iframe-api/ "mention") 文档。

## 交易

交易是每个区块链的重要组成部分。

当你想到交易时，你可能会从金融角度来看待它们，也就是说，交易是用户之间进行货币交换的过程。

虽然在传统系统中这是正确的，但在区块链世界里，交易实际上更加通用。区块链交易意味着对底层数据库（即区块链状态）的任何更改。

在DeSo上，这包括金融交易，但还包括社交交易，如提交帖子、关注用户、铸造NFT等。

由于区块链通信是点对点的，我们无法仅通过查看网络信息（如IP地址）来验证交易的发起者。

相反，我们使用密码学方法确保发送交易的人确实是他们声称的那个人。

为此，我们使用数字签名，这是由用户私钥颁发的特殊证书。

在与DeSo身份服务集成时，我们无需担心与用户密钥相关的所有密码学细节。

相反，我们可以简单地请求身份接口处理诸如颁发交易签名之类的繁琐工作。

### 生命周期

DeSo区块链上的交易生命周期包含三个步骤：

**构建:** 开发者首先通过诸如`/api/v0/buy-or-sell-creator-coin` 等接口与DeSo后端API进行交互，以获取未签名的用户交易。

**签名:** 开发者然后从构建步骤的响应中获取输出`TransactionHex`，该输出编码了用户交易，并使用DeSo身份服务对其进行签名。

**广播:** 签名后的交易将通过`/api/v0/submit-transaction`由开发者发送，以便将其添加到区块链账本中。

在本节中，我们主要对签名步骤感兴趣。

DeSo身份服务通过`iframe`和`window`窗口上下文处理交易签名的颁发。

您在`/log-in`中请求的`AccessLevel`（如[#access-levels](concepts.md#access-levels "mention")中所述）将决定您可以使用`iframe`上下文签名的交易。

每个`iframe` API所需的AccessLevel在 [iframe-api](../iframe-api/ "mention") 指南中有详细说明。

如果您要签名的交易与此AccessLevel匹配，您可以简单地通过[`iframe上下文` ](../iframe-api/#sign)要求DeSo身份服务在后台颁发签名。

这将通过向iframe发送`postMessage`来完成，如[#274](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L274)行所示。

```javascript
this.iframe.contentWindow.postMessage(req, "*");
```

请注意，我们在iframe `contentWindow`上执行`postMessage`。

尽管我们将在`iframe`上下文指南中详细解释`req`变量中应包含的内容，但我们实际上需要传递`method: "sign"`字段、encryptedSeedHex、`accessLevel`和and`accessLevelHmac`字段，并传递我们要签名的交易的`transactionHex`（例如[#125](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L125)行）。

响应将包含 `signedTransactionHex`，表示请求成功，或者一个`approvalRequired: true`字段，表示我们需要启动身份窗口来进行审批流程。

为此，我们将使用`/approve` 接口启动一个`window`窗口上下文，并将所需的交易作为URL参数传递：

```javascript
window.open("https://identity.deso.org/approve?tx={transactionHex}", null);
```

收到 `signedTransactionHex`后，我们可以将其广播到网络。

我们将在相应的API文档中进一步描述 `method: "sign"`iframe API 消息和 `/approve` 窗口 API接口。

## 消息

任何社交网络的关键组成部分都是消息。

A crucial component of any social network is messaging.

That's why the DeSo blockchain was designed to handle messages as transactions that can be securely stored on-chain.

Messages are private and can only be read by the sender and the recipient thanks to our public key cryptographic protocols.

Consequently, messages are handled by Identity.

As an application developer, it isn't crucial to understand the messaging scheme in-depth, but we want to share a few remarks for those crypto-savvy readers in the [Protocol](broken-reference/) subsection.

### Protocol

If you're working closely with DeSo messages or are a mobile app developer, you will most likely have to digest this subsection.

There are two implemented messaging protocols on the DeSo blockchain: legacy `V1`, and current `V2`; and we're expecting to move to a `V3` version soon.

Older blocks will still contain messages following the legacy `V1` messages so they ought to be handled differently than the `V2` messages.

If you've read our core documentation, you should remember the transaction `ExtraData` .

New message transactions will have a `V: []byte` field in `ExtraData` which should determine the used version (`1` or `2`) — if a message transaction doesn't have the `V` field, it means it's following `V1` scheme.

Here's how each version works:

* **`V1`**: (LEGACY) Messages encrypted to the recipient public key using AES-128-CTR scheme.\
  \
  Messages can be decrypted with the private key of the user specified in transaction metadata field `RecipientPublicKey`.\\
* **`V2`**: (CURRENT) Messages encrypted to both the recipient and sender using [AES-128-CTR](https://github.com/deso-protocol/identity/blob/f211503ea2420cc6e75e48683670d278cc152d8c/src/lib/ecies/index.js#L175) scheme with shared secrets derived via [ECDH](https://github.com/deso-protocol/identity/blob/f211503ea2420cc6e75e48683670d278cc152d8c/src/lib/ecies/index.js#L100) and run through a simple [SHA256 ConcatKDF](https://github.com/deso-protocol/identity/blob/f211503ea2420cc6e75e48683670d278cc152d8c/src/lib/ecies/index.js#L23).\
  \
  Under `V1`, once a message was broadcasted to the blockchain, the sender of the message was unable to decrypt it on other devices.\
  \
  On the other hand, shared secrets are available both to the recipient and sender, so both can decrypt messages.\\
* **`V3`**: (PLANNED) We intend to expand the `V2` scheme so it uses rotating messaging keys. Keys will be computed via HD wallet scheme with hardened derivation.\
  \
  Messaging keys can then be shared with third-party apps, specifically mobile clients, to simplify message handling.

If you're a mobile app developer using derived keys you will most likely need to implement the message encryption and decryption yourself, or rely on an existing implementation.

The best way would be to trace through the node.js example [encryption/decryption code snippet](https://github.com/deso-protocol/examples/tree/main/identity/messages-shared-secret) or look at [encryption](https://github.com/deso-protocol/identity/blob/f211503ea2420cc6e75e48683670d278cc152d8c/src/app/identity.service.ts#L203) and [decryption](https://github.com/deso-protocol/identity/blob/f211503ea2420cc6e75e48683670d278cc152d8c/src/app/identity.service.ts#L216) Identity code and recreate it in the programming language of your choosing.

If you bundle your code in a library for others to use, please do share it and we'll include it in this documentation!

Our cryptographic implementations are based on JavaScript [ECIES-Parity library](https://github.com/sigp/ecies-parity) from Sigma Prime.

### Implementation

Messages are primarily handled through the [`iframe` context](../iframe-api/#decrypt), with the exception of mobile clients relying on derived keys.

These clients will handle messages through the `window` context. The goal of this subsection is to give you a general intuition about message handling.

We leave the communication details to the section on `iframe` API.

#### Encryption

Encryption is a process of concealing information so that only some authorized parties can read it.

In order to successfully encrypt and send a message transaction, the following steps should be taken:

1. Message text is first encrypted through the Identity by sending a request with `method: "encrypt"` to the [`iframe` context](../iframe-api/#encrypt), as in [line #141](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L141).
2. The encrypted message is used in the Backend API to construct a message transaction via `/api/v0/send-message-stateless` endpoint.
3. After message transaction is prepared, the `transactionHex` will finally be signed by the DeSo Identity and broadcast to the network as described in the Transactions section.

#### Decryption

We previously mentioned concealing information, but what about revealing an encrypted message, or in other words, what about message decryption?

In order to read a message, we will pass the encrypted message to the DeSo Identity Service which will handle the message decryption for us.

This is done in a single step:

1. Send the encrypted message in a request to the `iframe` context with `method: "decrypt"` , as in [line #150](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L150). Check out the [#decrypt](../iframe-api/#decrypt "mention") API for details.

Another use-case for the `decrypt` API is decrypting unlockable text in NFTs.

To see how this can be done, we recommend tracing through the [`DecryptUnlockableTexts()`](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/backend-api.service.ts#L945) in the DeSo Protocol repository.

## Conclusion

In this document, we've outlined all the major components of integrating with the DeSo Identity.

If you've gone this far, you should have a good understanding of how the Identity works and what is required to efficiently incorporate it into your application.

Throughout this guide, we've intentionally left out many implementation details for the sake of simplicity.

If things made sense to you, it means it's a good time to dive into the other guides on the DeSo Identity. Specifically, if you're making a web-based application, you should take a look at our [Window API](../window-api/) and [iframe API](../iframe-api/) docs next.

If you're building a mobile application, we recommend taking a look at the [Mobile integration](mobile-integration.md) guide next.
