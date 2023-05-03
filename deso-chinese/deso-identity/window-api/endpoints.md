---
description: List of DeSo Identity Window API endpoints
---

# 接口

本节描述了Window API的所有接口。

接口通过URL参数进行调用，其中一些参数是可选的。在用户在窗口内完成操作后，Identity将发送一个`postMessage`响应。

## log-in登录

此接口用于处理用户登录或帐户创建。

#### 请求

```javascript
const login = window.open('https://identity.deso.org/log-in');
```

#### URL 参数

| 名称                            | 类型     | 描述                                                                                         |
| ----------------------------- | ------ | ------------------------------------------------------------------------------------------ |
| accessLevelRequest (optional) | int    | 请求的访问级别，如 [#access-levels](../identity/concepts.md#access-levels "mention") 中描述的，默认是访问级别为2 |
| testnet (optional)            | bool   | 是否使用测试网还是主网。默认为`false`                                                                     |
| webview (optional)            | bool   | 是否使用webview。默认为 `false`                                                                    |
| jumio (optional)              | bool   | 是否在注册时显示 "获得免费deso"。默认为`false`                                                             |
| referralCode (optional)       | string | 用户访问网站所用的推荐代码。推荐代码允许用户获得更多的钱作为注册奖励。[#get-free-deso](./#get-free-deso "mention")有更多描述。      |
| hideGoogle (optional)         | bool   | 从个人信息界面中隐藏谷歌登录。默认为`false`                                                                  |

`accessLevelRequest` 可以取值 {0, 2, 3, 4}。您应该只请求满足应用程序需求的最低权限。

以下是每个级别的含义：

```javascript
enum AccessLevel {
  // 用户撤销权限
  None = 0,

  // 所有交易都需要批准。
  // 这意味着没有帐户操作被授权。
  ApproveAll = 2, /* 默认值  */

  // 买入、发送和卖出需要批准
  // 这授权所有非消费操作。
  ApproveLarge = 3,

  // 节点可以在无需批准的情况下签署所有交易
  // 这授权所有非消费和消费操作。
  Full = 4,
}
```

#### 响应

```javascript
{
  id: null,
  service: "identity",
  method: "login",
  payload: {
    users: {
      BC1YLfsWMfv8UdytwrWqWvqSP6M6eQJg7W5TWL1WNDYd7zxi6wEShQX: {
        accessLevel: 4,
        accessLevelHmac: "0d22e283751c904ab36dc3910afe1a981...",
        btcDepositAddress: "1PXhm3D6sgZtfGNe2mtP27NVBHEcNJX2AW",
        encryptedSeedHex: "bdad93a19eb3be8b4c2f63b5cefb82823...",
        hasExtraText: false,
        network: "mainnet",
      },
      BC1...
    },
    publicKeyAdded: 'BC1YLfsWMfv8UdytwrWqWvqSP6M6eQJg7W5TWL1WNDYd7zxi6wEShQX',
    signedUp: false,
  }
}
```

对于登录或注册操作，所选公钥将包含在 `publicKeyAdded` 中。

当用户注册时，`signedUp` 字段将设置为 `true`，否则对于登录将为 `false`。

应用程序应将当前的 `publicKeyAdded` 和 `users` 对象存储在本地存储中。

您应该始终覆盖现有的保存的用户。

查看[#messages](./#messages "mention") 部分以获取更多信息。

当应用程序要签署交易或解密消息时，需要 `accessLevel`、`accessLevelHmac` 和 `encryptedSeedHex` 值。

## logout登出

登出用于重置单个用户的 `accessLevel`。您应该通过调用此接口来处理用户登出。

当您登出用户时，您应该从本地存储/数据库中删除相应的 `userCredentials` 条目。

有关更多信息，请参阅[#messages](./#messages "mention") 部分。

#### 请求

```javascript
const logout = window.open('https://identity.deso.org/logout');
```

#### URL 参数

| 名称                 | 类型     | 描述                      |
| ------------------ | ------ | ----------------------- |
| publicKey          | string | 正在登出的用户的公钥              |
| testnet (optional) | bool   | 是否使用测试网还是主网。默认为`false`  |
| webview (optional) | bool   | 是否使用webview。默认为 `false` |

#### 响应

```javascript
{
  id: null,
  service: "identity",
  method: "login",
  payload: {
    users: {
      BC1YLfsWMfv8UdytwrWqWvqSP6M6eQJg7W5TWL1WNDYd7zxi6wEShQX: {
        accessLevel: 4,
        accessLevelHmac: "0d22e283751c904ab36dc3910afe1a981...",
        btcDepositAddress: "1PXhm3D6sgZtfGNe2mtP27NVBHEcNJX2AW",
        encryptedSeedHex: "bdad93a19eb3be8b4c2f63b5cefb82823...",
        hasExtraText: false,
        network: "mainnet",
      },
      BC1...
    }
  }
}
```

## 批准

批准接口用于事务签名。

如果您不确定这意味着什么，请确保查看 [#transactions](../identity/concepts.md#transactions "mention")以了解 DeSo 区块链中事务是如何工作的。

通常，在您想要签署一个超出您在[#log-in](./#log-in "mention")登录期间请求的[`accessLevel`](broken-reference/) 范围的事务时，会调用批准接口。

例如，如果您请求`accessLevel=3` 并想要签署一个 `BasicTransfer` 事务（通过 `api/v0/send-deso` API 构造），您需要使用批准接口，因为 `api/v0/send-deso` 需要 `accessLevel=4`。

如果您要签署的事务在您拥有的 `accessLevel` 范围内，您应该通过[iframe-api](../iframe-api/ "mention")接口签署。

#### 请求

```javascript
const approve = window.open('https://identity.deso.org/approve');
```

#### URL 参数

| 名称                 | 类型     | 描述                      |
| ------------------ | ------ | ----------------------- |
| tx                 | string | 要签署的交易的十六进制哈希           |
| testnet (optional) | bool   | 是否使用测试网还是主网。默认为`false`  |
| webview (optional) | bool   | 是否使用webview。默认为 `false` |

#### 响应

```javascript
{
  id: null,
  service: "identity",
  method: "login",
  payload: {
    users: {
      BC1YLfsWMfv8UdytwrWqWvqSP6M6eQJg7W5TWL1WNDYd7zxi6wEShQX: {
        accessLevel: 4,
        accessLevelHmac: "0d22e283751c904ab36dc3910afe1a981...",
        btcDepositAddress: "1PXhm3D6sgZtfGNe2mtP27NVBHEcNJX2AW",
        encryptedSeedHex: "bdad93a19eb3be8b4c2f63b5cefb82823...",
        hasExtraText: false,
        network: "mainnet",
      },
      BC1...
    },
    signedTransactionHex: "0196be9786f1634b2734196bad0798..."
  }
}
```

## derive派生

派生接口用于为用户生成派生密钥。

当您持有派生密钥时，您可以在不与 DeSo 身份服务交互的情况下为用户签署事务。&#x20;

派生密钥主要用于移动应用程序和使用[#callbacks](./#callbacks "mention")回调。&#x20;

如果没有指定回调，您将通过[#messages](./#messages "mention")接收派生密钥。&#x20;

在这种情况下，您将收到带有 `method: "derive"`"（与 "`login`" 相对）的请求体。有关派生密钥的更多信息可以在 [#derived-keys](../identity/mobile-integration.md#derived-keys "mention")部分找到。

在事务支出限制分叉高度达到后，派生密钥将需要对事务支出限制进行授权，这将为事务类型提供粒度权限，甚至为 NFT、创作者币和 DAO 币提供更细粒度的权限。&#x20;

要了解更多信息，请阅读[#derived-keys](../identity/mobile-integration.md#derived-keys "mention")部分。

#### 请求

```javascript
const derive = window.open('https://identity.deso.org/derive');
```

#### URL 参数

| 名称                       | 类型     | 描述                                                                                                                                                                                                   |
| ------------------------ | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| callback (optional)      | string | <p><a data-mention href="./#callbacks">#callbacks</a>中阐述的有效载荷的回调URL。 回调</p><p></p><p>Callback URL for the payload as explained in <a data-mention href="./#callbacks">#callbacks</a></p>             |
| testnet (optional)       | bool   | 是否使用测试网还是主网。默认为`false`                                                                                                                                                                               |
| webview (optional)       | bool   | 是否使用webview。默认为 `false`                                                                                                                                                                              |
| TransactionSpendingLimit | string | [#transactionspendinglimitresponse](../../deso-backend/api/#transactionspendinglimitresponse "mention") as a JSON string. Use `encodeURIComponent(JSON.stringify(transactionSpendingLimitResponse))` |
| PublicKey                | String | Public key (in base 58) for which you want to generate a derived key (Optional)                                                                                                                      |
| DerivedPublicKey         | String | Derived key (in base 58) that you want to use instead of allowing identity to generate a new one (Optional)                                                                                          |

#### Response

```javascript
{
  id: null,
  service: "identity",
  method: "derive",
  payload: {
    derivedSeedHex: "40538066039f21f42f0247f49fe4e7d63a9d80528486b02fea37ce3c57886546",
    derivedPublicKeyBase58Check: "BC1YLjGYUcpF7HMqmUYNoDaV7Wxc8TYoGhGwmigyEVSCKNAxU9GmikD",
    publicKeyBase58Check: "BC1YLj8iwsicimv8ttrPg6rBtizvM7X3KCsiVQwYZKqj6Wj3rT8TD3D",
    btcDepositAddress: "Not implemented yet",
    ethDepositAddress: "Not implemented yet",
    expirationBlock: 91587,
    network: "mainnet",
    accessSignature: "304502206a612483e970e3494191e5242683b2be8d22ca6e20473e990f50098021099d8b022100f5b011ea683499dee50079ed2497bfbd16461e802e53f956d4c9c6cdaa423f47",
    jwt: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE2Mzc5OTQ3NDMsImV4cCI6MTY0MDU4Njc0M30.o74505dYS0qZ5EqSxeYiuSzMCTFRLEToLXmudTtbdk3oYhu80M95qbHX5BjHQw4Bvuh7yz8Hv1u2-leReAaf9Q",
    derivedJwt: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE2Mzc5OTQ3NDMsImV4cCI6MTY0MDU4Njc0M30.Nb1IuWgOxrG21NG6zyHMA6M-Mlq2kVrIPVNWAhj49jlaDCqlGQAAEBOl4jlVBC3vNU0S9yyq_8QI5Gmz4Qk1lA"
  }
}
```

The meaning of each of these fields is explained in [#derived-keys](../identity/mobile-integration.md#derived-keys "mention"). In the case of the callback, all these fields will be passed as URL parameters.

Assuming, we've passed `callback=auth://derive`, the payload will be sent as a GET request like this:

```javascript
GET auth://derive?derivedSeedHex=...&derivedPublicKey=...&publicKey=...&
btcDepositAddress=...&ethDepositAddress=...&expirationBlock=...&network=...&
accessSignature=...&jwt=...&derivedJwt=...
```

## get-shared-secrets

This endpoint is intended to be used in combination with derived keys.

It allows you to get message encryption/decryption keys, or shared secrets, so that you can read messages without querying Identity (as is traditionally done via [iframe-api](../iframe-api/ "mention")).

The `get-shared-secrets` endpoint requires a callback as well as other URL parameters that are used to verify ownership of the derived key.

Another required URL parameter is `messagePublicKeys` which are the public keys of messaging parties in [CSV format](https://en.wikipedia.org/wiki/Comma-separated\_values#Basic\_rules) for which we want to fetch shared secrets.

The `get-shared-secrets` endpoint allows to fetch multiple shared secrets at once.

For example, if the owner of the derived key was public key `A`, and we wanted to read conversations between parties `B`,`C`,`D` (or more) we would set `ownerPublicKey=A` and `messagePublicKeys=B,C,D` .

#### Request

```javascript
const secrets = window.open('https://identity.deso.org/get-shared-secrets');
```

#### URL Parameters

| Name               | Type                                                                                      | Description                                                                       |
| ------------------ | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| callback           | string                                                                                    | Callback URL for the payload as explained in [#callbacks](./#callbacks "mention") |
| ownerPublicKey     | string                                                                                    | Master public key that was used to get the derived key                            |
| derivedPublicKey   | string                                                                                    | Derived public key                                                                |
| JWT                | string                                                                                    | JWT signed by the derived key, it's passed as `derivedJwt` in `/derive`           |
| messagePublicKeys  | strings, [CSV format](https://en.wikipedia.org/wiki/Comma-separated\_values#Basic\_rules) | Public keys of users for which we want to fetch shared secrets.                   |
| testnet (optional) | bool                                                                                      | 是否使用测试网还是主网。默认为`false`                                                            |

#### Response

The response is a list of shared secrets in CSV format corresponding to `messagePublicKeys` . The payload will always be sent as a [#callbacks](./#callbacks "mention").

```javascript
GET callback?sharedSecrets=ccf9474d7578658e0c77adb7aea5cc9b61d3bccad5bddddd11e1850dfa4db059,
    db7aea5cc9b61d3b..., a11e1850dfa4db059...
```

## get-free-deso

The `get-free-deso` endpoint allows for launching the Jumio KYC flow, and if completed successfully, it will end up sending your users free starter DeSo or their referral bonus.

#### Request

```javascript
const free = window.open('https://identity.deso.org/get-free-deso');
```

#### URL Parameters

| Name                    | Type   | Description                               |
| ----------------------- | ------ | ----------------------------------------- |
| public\_key             | string | Public key of the user to be KYC verified |
| referralCode (optional) | string | Referral code to be used in Jumio         |
| testnet (optional)      | bool   | 是否使用测试网还是主网。默认为`false`                    |

#### Response

```javascript
{
  id: null,
  service: "identity",
  method: "login",
  payload: {
    users: {
      BC1YLfsWMfv8UdytwrWqWvqSP6M6eQJg7W5TWL1WNDYd7zxi6wEShQX: {
        accessLevel: 4,
        accessLevelHmac: "0d22e283751c904ab36dc3910afe1a981...",
        btcDepositAddress: "1PXhm3D6sgZtfGNe2mtP27NVBHEcNJX2AW",
        encryptedSeedHex: "bdad93a19eb3be8b4c2f63b5cefb82823...",
        hasExtraText: false,
        network: "mainnet",
      },
      BC1...
    },
    publicKeyAdded: "BC1...",
    signedUp: true,
    jumioSuccess: true
  }
}
```

The `signedUp` boolean variable determines if the user has logged in or signed up, and `jumioSuccess` indicates if the Jumio KYC was successful.

## verify-phone-number

The `verify-phone-number` endpoint will give the user some starter DeSo after a successful phone verification.

#### Request

```javascript
const phone = window.open('https://identity.deso.org/verify-phone-number');
```

#### URL Parameters

| Name               | Type   | Description                                 |
| ------------------ | ------ | ------------------------------------------- |
| public\_key        | string | Public key of the user to be phone verified |
| testnet (optional) | bool   | 是否使用测试网还是主网。默认为`false`                      |

#### Response

```javascript
{
  id: null,
  service: "identity",
  method: "login",
  payload: {
    users: {
      BC1YLfsWMfv8UdytwrWqWvqSP6M6eQJg7W5TWL1WNDYd7zxi6wEShQX: {
        accessLevel: 4,
        accessLevelHmac: "0d22e283751c904ab36dc3910afe1a981...",
        btcDepositAddress: "1PXhm3D6sgZtfGNe2mtP27NVBHEcNJX2AW",
        encryptedSeedHex: "bdad93a19eb3be8b4c2f63b5cefb82823...",
        hasExtraText: false,
        network: "mainnet",
      },
      BC1...
    },
    publicKeyAdded: "BC1...",
    signedUp: true,
    phoneNumberSuccess: true
  }
}
```

The `signedUp` boolean variable determines if the user has logged in or signed up, and `phoneNumberSuccess` indicates if the phone verification was successful.
