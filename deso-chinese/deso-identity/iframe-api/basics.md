---
description: Guide on integrating with the DeSo Identity iframe API
---

# 概述

iframe 可以让您在不需要用户交互的情况下执行诸如签署交易或加密/解密消息等操作，就像在[window-api](../window-api/ "mention")中一样。

iframe 通常对用户完全不可见。

然而，在某些浏览器（如 Safari）上，iframe 需要渲染，因为用户需要点击 iframe 授权其存储访问权限。

我们将在指南后面部分解释如何实现这一点。

要与 iframe API 通信，您需要在应用中加入 iframe 窗口 HTML 组件，并将其指向 `https://identity.deso.org/`URL。

下面是一个示例组件。

我们还为您提供了一个带有 CSS 样式的示例。

```markup
<iframe
  id="identity"
  frameborder="0"
  src="https://identity.deso.org/embed"
  style="height: 100vh; width: 100vw; display: none; position: fixed; 
    z-index: 1000; left: 0; top: 0;"
></iframe>
```

请注意 iframe 组件的 CSS 样式。如前所述，iframe 通常对用户不可见。这就是为什么我们在 CSS 样式中设置 `display: none`。

我们还希望 iframe 窗口位于您的应用之上，并占据整个显示区域，因此添加了其他 CSS 属性。

您应根据您的应用程序修改 z-index 属性。在我们需要向用户显示 iframe 时，您将设置属性 `display: block`。

例如，`document.getElementById("identity.style.display = "block");`

## 消息

与 iframe 上下文的通信是通过发送 `postMessage()`请求来完成的。类似地，iframe 上下文会通过发送消息事件向您发送响应。

当您第一次打开 iframe 上下文时，Identity 将向您发送一个`initialize`初始化消息，需要您回应。

这个概念在 [#events](../identity/concepts.md#events "mention") 部分中有更详细的解释。

向 Identity iframe 发送请求时，您需要包含一个`id`字段。id 应采用 [UUID v4](https://en.wikipedia.org/wiki/Universally\_unique\_identifier#Version\_4\_\(random\)) 格式。我们建议使用  [uuid npm](https://www.npmjs.com/package/uuid) 包，或者您也可以使用以下 Vanilla JavaScript 实现：

```javascript
function uuid() {
    return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
        var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
        return v.toString(16);
    });
}
```

以下是您可以发送给 Identity iframe 的示例消息格式：

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  method: 'info',
}
```

让我们看看这些字段的含义：

* `id` 应采用 UUID v4 格式生成。
* `service` 应始终设置为 "`identity`"，以便 DeSo 身份验证服务知道它应该读取此消息。
* `method` 这是您将要请求的 API 类型，在这种情况下是 "`info`"，但它可以是 "`sign`"、""`encrypt`" 等，如 [#api](./#api "mention") 部分所述。

您应该根据 `id` 对请求进行索引，以便您可以将它们与响应进行匹配。

我们建议查看 [DeSo 协议源代码](https://github.com/deso-protocol/frontend)，以了解如何实现这一点。

尤其是，您可以跟踪 [#257 in `src/app/identity.service.ts`](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L257)的 `send()` 方法。

## 存储访问

除了`initialize`初始化外，您还需要向 `iframe` 上下文发送一个`info`消息。

DeSo Identity 使用本地存储和 cookies 在窗口和 iframe 上下文之间共享有关用户的信息。

然而，某些浏览器（如 Safari 和 iOS 上的 Chrome）在未经用户明确许可的情况下禁止以这种方式存储数据。

例如，苹果的智能跟踪防护（ITP）对跨域数据存储和访问施加了严格的限制。

这意味着每次页面重新加载时，Identity iframe 必须请求存储访问权限。

这可以通过向用户显示 iframe（即设置 `display: "block"`）并让用户点击 iframe 来实现。

当用户在 Safari 中访问 DeSo 应用程序时，他们会看到一个 "点击任意位置以解锁您的钱包" 的提示，这是 iframe 中的一个巨大按钮。当用户点击按钮时，他将为 Identity 提供所需的访问权限。

以下是用户看到的操作界面：

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption><p>Iframe界面以确保获得存储权限</p></figcaption></figure>

为简化此过程，您应执行一个名为 `info` 的特殊 API 调用，该调用将指示您是否应向用户显示 iframe 窗口。

最佳实践是在向 iframe API 发送第一个non-`initialize`非初始化请求（如签名、加密等）时发送 `info` 消息。

我们建议查看[示例仓库](https://github.com/deso-protocol/examples/tree/main/identity/iframe-info-storage-access/)中的 `identity/iframe-info-storage-access`代码片段，该代码片段使用 Vanilla JavaScript 和 promises 实现此通信模式。

如果您熟悉 [RxJS](https://rxjs.dev)，还可以查看  [Deso协议代码](https://github.com/deso-protocol/frontend)，从[第32行](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/identity.service.ts#L32)的 `storageGranted` 变量开始。以下是 `info` 请求的示例：

#### 请求

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  method: 'info',
}
```

以下是 iframe 将发送给您的响应：

#### 响应

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    browserSupported: true,
    hasCookieAccess: true,
    ​​hasLocalStorageAccess: true,
    ​​hasStorageAccess: true
  },
}
```

让我们看一下上述返回体中的字段：

* `browserSupported` 告诉您 DeSo Identity 服务是否与用户的浏览器兼容。如果设置为 false，则应通知用户 Identity 对他们不起作用。大多数现代浏览器的输出为 true。
* `hasCookieAccess` 表示用户浏览器是否允许使用 cookies。如果本地存储不可用，Identity 可能会在 cookies 中存储一些信息。
* `hasLocalStorageAccess` 如果用户浏览器上的本地存储可用，则为 `true`。
* `hasStorageAccess` 表示浏览器是否有存储访问权限。如果设置为 `false`，您将需要向用户显示 `iframe` 窗口，以便他可以授予访问权限。我们接下来将介绍这个部分。

如果 `info` 消息返回`hasStorageAccess: false`，您的应用程序应使 iframe 占据整个页面。

您可以通过设置以下内容来实现此目的：&#x20;

```javascript
document.getElementById("identity").style.display = "block"
```

`info` 消息还检测用户是否禁用了第三方 cookie。

第三方 cookie 对于 Identity 在安全地签署交易中是必需的。

如果无法使用 localStorage 或 cookie，`info` 返回`browserSupported: false`，您的应用程序应告知用户他们将无法使用 Identity 进行签名或解密操作。

当用户点击 "点击任意位置以解锁您的钱包" 时，iframe 会通过发送 `storageGranted` 消息来表示。

这个请求不需要回应。当您的应用程序收到 `storageGranted` 消息时，它可以向用户隐藏 `iframe` 窗口，此时 iframe 已准备好接收签名、解密等消息。

要隐藏 `iframe`，只需调用：

```javascript
document.getElementById("identity").style.display = "none"
```

#### `storageGranted` 消息

```javascript
{
  service: 'identity',
  method: 'storageGranted',
}
```

## 用户凭证

要使用 iframe API，您首先需要从[window-api](../window-api/ "mention")通过[#log-in](../window-api/#log-in "mention") 接口获取安全的用户凭证。

用户凭证包括三个字段，即 `encryptedSeedHex`、`accessLevel` 和 `accessLevelHmac`。

您必须将这些信息包含在 iframe API 的所有请求中。我们还在[#accounts](../identity/concepts.md#accounts "mention")部分解释了这个概念。
