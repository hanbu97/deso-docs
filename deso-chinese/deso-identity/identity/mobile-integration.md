---
description: Integrating with the DeSo Identity on Mobile.
---

# 移动集成

## 派生密钥

在区块链世界中，用户的私钥是非常敏感的信息。

这是因为，与Web2密码不同，私钥无法修改，这意味着如果有人获得了您的私钥，您可能**永远**面临攻击的风险，除非您将整个帐户转移到另一个私钥上，否则您无法做其他的事情来保证账户的安全。

我们坚信用户的主密钥**永远**不应与第三方应用程序共享，无论它们的安全措施如何。因此，我们创建了派生密钥，这大大降低了与未经授权访问用户凭据相关的攻击途径。

派生密钥是临时的，通常在发行后约30天内自动过期。

派生密钥还可以在任何时候取消授权，我们相信这将有助于在未来创建先进的安全系统，以减轻因密钥泄漏产生的风险。

在达到`Transaction Spending Limit`(交易支出限制区块高度)之后，派生密钥将需要获得授权才能执行特定类型的交易以及更具体的创建者代币、DAO代币和NFT交易类型操作。查看 以了解如何构建交易支出限制对象。

查看 [#transactionspendinglimitresponse](../../deso-backend/api/#transactionspendinglimitresponse "mention") 以了解如何构建交易支出限制对象。

派生密钥是一对公钥和私钥，它们被授权代表另一对密钥签署交易。

也就是说，如果您持有用户的有效派生密钥，您可以提交由该派生密钥签署的交易，它将被视为有效交易，就像是由该用户发起的。

这对于移动应用程序尤其有用，因为这意味着您只需与DeSo身份服务交互一次，以获取用户的派生密钥。

这也意味着派生密钥是极为敏感的信息，因此应在安全存储中谨慎处理，最好由经验丰富的软件工程师来处理。\\

**使用派生密钥的流程如下：**

1. 通过调用 [#derive](../window-api/#derive "mention")  window API接口生成派生密钥\\
2. 通过后端API在`/api/v0/authorize-derived-key`构建`AuthorizeDerivedKey`交易\\
3. 用派生密钥签署`AuthorizeDerivedKey`交易\\
4. 通过`/api/v0/submit-transaction`提交已签名的`AuthorizeDerivedKey`交易\\
5. （可选）通过后端API在`/api/v0/get-user-derived-keys`确认派生密钥已成功授权\\

### 生成派生密钥

如果您的应用程序需要离线签名，例如当您是移动客户端时，身份验证可以为您提供派生密钥需要的材料。

要为用户获取派生密钥，请使用回调启动 [#derive](../window-api/#derive "mention")  window API接口：

```javascript
const derive = window.open('https://identity.deso.org/derive?callback=...');
```

当用户完成身份验证流程后，您将收到包含派生密钥对的返回结果。

为了简化起见，我们将返回体设为JSON对象；但是，您将在回调中以URL参数的形式接收它。

#### 返回结果

```javascript
{
    accessSignature: "30440220314ccf7a747922ddb6f8c26821c6f0dc65f0227e15014fb5e728f96abed841e2022033aace1f75eb35479d07273ff8bf1a959af75209743ced23939210f824d5321f",
    derivedPublicKeyBase58Check: "tBCKUx3cYyUcPnXyqLYuAfpChQHzcbzvhLTF6Xujuu5CA8hKaHPwTo",
    derivedSeedHex: "e4c515c30479d116757c56b4224022a5558af243946c075cff6ae2ec67fd3748",
    expirationBlock: 12024,
    derivedJwt: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE2MzM5Mjg0MjgsImV4cCI6MTYzNjUyMDQyOH0.dvbNwcOUz2bzEMC79nyxzIJGI_3NoMUw59VAI6qdLGNy6x5YC9u0MsFcrDhuL-i8Y66gIQXq0VzgeIzNThxisg",
    jwt: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE2MzM5Mjg0MjgsImV4cCI6MTYzNjUyMDQyOH0.4zyR0kgXIeO6P94TuGWbxxr3fHUoIyJWv4GGMAxP6gfz6UMwSSej85ZJe_N5JYYcvk_vHWhnj0CcXfGQtNMQ8Q",
    network: "testnet",
    messagingKeyName: "default-key",
    messagingKeySignature: "fakemessagingkeysignature",
    messagingPublicKeyBase58Check: "tBC1YLh9Rjy3fLcW1bRDcQ4PXhGocuGnsNqVJx3CESCJknkZ7LJ6mV"
    messagingPrivateKey: "fakemessagingprivatekey"
    publicKeyBase58Check: "tBCKWiTPdkGAiSd2jTx58hRh1TAGVnpeDE78eYqsghEeVFpjkGYNLk",
    transactionSpendingLimitHex: "80d0dbc3f4020205f4b00709ff8705042100000000000000000000000000000000000000000000000000000000000000000000871121032357f6e57297839516ae3fd71e76ce47e43f881e8be86877f00ec456594b10c9017b21032357f6e57297839516ae3fd71e76ce47e43f881e8be86877f00ec456594b10c9029b2021032357f6e57297839516ae3fd71e76ce47e43f881e8be86877f00ec456594b10c903ee4700022001855d9ca9c54d797e53df0954204ae7d744c98fe853bc846f5663459ac9cb7b00010a2001855d9ca9c54d797e53df0954204ae7d744c98fe853bc846f5663459ac9cb7b0003f50300"
}
```

让我们来看看这些值：

* `accessSignature` 是访问证明，等于所有者签名的`sha256(derivedPublicKey + expirationBlock + transactionSpendingLimitBytes)`（更多详细信息，请查看[实现](https://github.com/deso-protocol/identity/blob/ffcb09a3ba6070d14a43c31a09f7ed0478fb2acf/src/app/account.service.ts#L109)）\\
* `derivedPublicKeyBase58Check` 和 `derivedSeedHex` 是派生的密钥对\\
*   `expirationBlock` 是一个未来的区块高度，表示派生密钥的过期“日期”（区块）。派生密钥在大约 30 天后过期。



    在此期间，您将不得不生成并授权另一个派生密钥。\
    \
    要检查派生密钥是否有效，您应该将当前区块高度（例如，从`/api/v0/get-app-state` 后端 API 接口获取的区块高度）与通过查询`/api/v0/get-user-derived-keys`后端 API 接口找到的 `expirationBlock` 进行比较。\\
* `derivedJwt` 是由 `derivedPublicKeyBase58Check` 签署的有效期为一个月的 JWT 令牌\\
* `jwt` 是由所有者 `publicKeyBase58Check` 签署的一个月超时的 JWT 令牌\\
* `network` 是生成此派生密钥的网络\\
* `messagingKeyName` 是用于 v3 消息传递的密钥名称\\
* `messagingKeySignature` 是用于 v3 消息传递的密钥签名\\
* `messagingPublicKeyBase58Check` 是用于 v3 消息传递的公钥\\
* `messagingPrivateKey` 是用于 v3 消息传递的私钥\\
* `publicKeyBase58Check` 是此派生密钥的父公钥。\\
* `transactionSpendingLimitHex` 是表示此派生密钥的 TransactionSpendingLimit 的十六进制字符串。\\

### 授权派生密钥

在进行任何签名之前，必须首先通过提交包含 `accessSignature`、`derivedPublicKeyBase58Check`、`expirationBlock`、`transactionSpendingLimitHex` 和 `publicKeyBase58Check` 的 [`authorizeDerivedKey交易`](https://docs.deso.org/devs/backend-api#authorize-derived-key)来激活派生密钥。

为了进行交易，请向`/api/v0/authorize-derived-key` 后端 API 接口发起请求。

如果在进行后端 API 请求时设置`DerivedKeySignature: true`，则可以立即使用派生密钥对授权交易进行签名。

为了帮助您开始使用 authorizeDerivedKey 交易，我们在示例仓库中提供了一个 [node.js代码片段](https://github.com/deso-protocol/examples/tree/main/identity/authorize-derived-key)，展示了此流程。

如果一切正常，您应该在 `/api/v0/get-user-derived-keys` [接口](https://github.com/deso-protocol/backend/blob/f70d89a/routes/user.go#L2559) 的响应中看到派生密钥，返回体中 `PublicKeyBase58Check` 的值为所有者用户公钥。

此外，请参阅 DeSo 开发者中心中 AuthorizeDerivedKey 的[实现](https://hub.deso.org/#/user/authorize-derived-key)。

虽然功能强大，但此模型有一个局限性。

意味着它要求用户拥有一定的余额才能执行 `authorizeDerivedKey` 交易。

这会造成一个局限，因为您将无法为可能在其帐户中没有余额的新用户授权派生密钥。

&#x20;然而，在实践中，除非用户在其帐户中拥有一定的非零余额，否则派生密钥将没有用处。否则，他们根本无法提交任何交易。

解决这个限制的一个可能方法是在用户要执行操作并拥有足够余额时才发送 `authorizeDerivedKey` 交易（例如点赞、关注等）。

另一种方法是编写一个钩子，在用户收到足够余额时在后台发送 `authorizeDerivedKey` 交易。&#x20;

这种简单的后台机制应该可以缓解大多数用户体验问题。

### 签名交易

从身份验证的`/derive` 返回的响应中嵌入的私钥 `derivedSeedHex` 可以代表 `publicKeyBase58Check` 拥有者签署交易。

为实现这一目标，您应该在后端 API 中构建交易，就像是由拥有者的公钥发起的。

然后，您需要将值设置为派生公钥（以压缩字节格式（33 字节数组）编码为十六进制字符串）的字段附加到交易的 `ExtraData["DerivedPublicKey"]`。

查看 examples 代码仓库中的此node.js  [代码片段](https://github.com/deso-protocol/examples/tree/main/identity/compress-public-key)，以帮助压缩派生公钥。

如果您在对交易进行序列化/反序列化以将“`DerivedPublicKey`”添加到 ExtraData 时遇到问题，可以使用后端接口`/api/v0/append-extra-data`，将交易的十六进制和派生公钥传递给它，如下所示：

**请求**

```javascript
{
    "TransactionHex": "01049434a060acca8c05af65207c019e1052f3e29dc677125ce8a1833ac72e2b2d010102a7af43768408e8b8f5bacc8d0658f36bb27c7ecb81b88e210d7be4e54861a40bcf980c0a21669d2ac6caefa5af9c6bb60d28b30f78d918d5b5b9ee3b5ae986818dc07eee84012102a7af43768408e8b8f5bacc8d0658f36bb27c7ecb81b88e210d7be4e54861a40b0000",
    "ExtraData": { 
        "DerivedPublicKey": "03f6f5470d8df61160ccf364851c77b8b803131d3f1e8092301178e2fdcec15206"
    }
}
```

由于交易费用和 ExtraData 的复杂性，当构建交易时，您应该将 `minFeeRateNanosPerKb` 从 `10000` 提高到约 `12500`。

否则，在提交交易时，您可能会收到提示交易费用过低的错误。

一旦您在 ExtraData 中获得了带有派生密钥的交易十六进制，您可以使用从身份验证获得的 `derivedSeedHex` 对交易进行签名。

您可以使用 `authorize-derived-key` 示例的 [node.js代码片段](https://github.com/deso-protocol/examples/tree/main/identity/authorize-derived-key)中的签名代码。

您还可以在此处找到 Go 语言的 [示例实现](https://github.com/deso-protocol/backend/blob/f70d89a196cfc42ca3e32a1b80ed9935380a91be/routes/admin\_transaction.go#L349) ，该示例实现对应于管理员后端接口`/api/v0/admin/test-sign-transaction-with-derived-key。`

签名完成后，可以像往常一样通过 `/api/v0/submit-transacton` 提交交易。

请注意，在此过程中，我们不需要与 DeSo Identity 进行任何通信。

### 消息

您可以使用派生密钥在 DeSo 区块链上加密/解密消息。有关我们的消息传递协议的更多信息，请查看相关协议部分。

为了加密/解密消息，您需要为用户的每个消息传递合作伙伴获取共享密钥。

要获取共享密钥，您可以向窗口[#get-shared-secrets](../window-api/#get-shared-secrets "mention")  API 中的接口提交请求。

获得共享密钥后，您可以使用它们来加密/解密消息。

为了帮助您编写这个流程，我们在 examples 代码仓库中提供了一个  [node.js代码片段](https://github.com/deso-protocol/examples/tree/main/identity/messages-shared-secret)，展示了我们的消息传递协议。

## Webview 支持

身份验证也支持 Webview，但为了完全集成，需要一些改变。

主要差异如下：

* 无需运行 iframe 上下文。您将向在 Webview 中运行的一个上下文发送所有消息。
* 您的 Webview 上下文需要有一个额外的参数 ?webview=true。
* 根据您的移动开发框架，您需要确保来自 Webview 的消息被正确注册。目前支持 iOS、Android 和 React Native Webview。
