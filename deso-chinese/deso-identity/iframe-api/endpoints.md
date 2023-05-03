---
description: List of DeSo Identity iframe API endpoints
---

# 终端

#### 注意：

iframe API 支持公钥和派生密钥的功能。您可以通过检查登录响应中的 `derivedPublicKeyBase58Check` 来确定密钥类型。

## sign

[**访问级别**](broken-reference/)**: 3, 4 (取决于**[**交易**](https://github.com/deso-protocol/identity/blob/9dad527dc46498b9aaa0344abd70dc8895acf246/src/app/identity.service.ts#L288)**)**

sign 消息负责对交易十六进制进行签名。如果需要批准，应用程序必须调用窗口 API 中的[#approve](../window-api/#approve "mention")接口来对交易进行签名。

#### Public keys公钥返回体

| 名称             | 类型     | 描述            |
| -------------- | ------ | ------------- |
| transactionHex | string | 您想签名的交易十六进制哈希 |

#### Derived keys派生秘钥的返回体

| 名称                          | 类型     | 描述                            |
| --------------------------- | ------ | ----------------------------- |
| transactionHex              | string | 您想签名的交易十六进制哈希                 |
| derivedPublicKeyBase58Check | string | 仅在已登录用户使用派生密钥代表所有者公钥进行签名时才需要。 |

#### 请求

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  method: 'sign',
  payload: {
    accessLevel: 4,
    accessLevelHmac: "0fab13f4...",
    encryptedSeedHex: "0fab13f4...",
    transactionHex: "0fab13f4...",
    derivedPublicKeyBase58Check: "0fab13f4...",

  },
}
```

#### 响应（成功）

如果交易成功签名，您将得到此响应。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    signedTransactionHex: "0fab13f4...",
  },
}
```

#### 响应（需要批准）

如果您的用户授权的访问级别与签署交易所需的`accessLevel`访问级别不匹配，您将得到此响应。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    approvalRequired: true,
  },
}
```

## encrypt

[**访问等级**](broken-reference/)**: 2**

encrypt API 负责加密消息。有关更多详细信息，请查看 [#messages](../identity/concepts.md#messages "mention")

#### Public keys公钥返回体

| 名称                 | 类别     | 描述                       |
| ------------------ | ------ | ------------------------ |
| recipientPublicKey | string | 接收者的公钥，以base58check格式表示。 |
| message            | string | 您想加密的消息文本。               |

#### Derived keys派生秘钥的返回体

仅在登录用户使用派生密钥代表所有者公钥签名时需要。

| 名称                              | 类别     | 描述                                |
| ------------------------------- | ------ | --------------------------------- |
| recipientPublicKey              | string | 接收者的公钥，以base58check格式表示。          |
| message                         | string | 您想加密的消息文本。                        |
| encryptedMessagingKeyRandomness | string | 在加密消息时，此值将用于替代`encryptedSeedHex`。 |
| derivedPublicKeyBase58Check     | string | 以base58check格式请求加密的公钥。            |
| ownerPublicKeyBase58Check       | string | 公钥仅用于验证。                          |

#### 请求

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  method: 'encrypt',
  payload: {
    accessLevel: 3,
    accessLevelHmac: "0fab13f4...",
    encryptedSeedHex: "0fab13f4...",
    recipientPublicKey: "BC1YLgwkd7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ"
    message: "This is a message",
    derivedPublicKeyBase58Check: "BC1YLsond7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ",
    ownerPublicKeyBase58Check: "BC1YLdadd7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ",
    encryptedMessagingKeyRandomness: "837fab39...",
    
  },
}
```

**派生密钥的响应（需要加密的消息随机性密钥）**

如果请求包含 `derivedPublicKeyBase58Check` 并且不包括 `ownerPublicKeyBase58Check` 和 `encryptedMessagingKeyRandomness`，您将获得此响应。

```javascript
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    encryptedMessage: "",
    requiresEncryptedMessagingKeyRandomness: true,
  },
}
```

您可以通过调用窗口 API 中的 messaging-group 请求加密的 MessagingKeyRandomness。

#### 响应（需要批准）

如果您的用户授权的访问级别与签署交易所需的`accessLevel`访问级别不匹配，您将获得此响应。

要解决此问题，用户需要允许至少访问级别 2。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    approvalRequired: true,
  },
}
```

#### 响应

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    encryptedMessage: "0fab13f4...",
  },
}
```

## decrypt

[**访问等级**](broken-reference/)**: 2**

解密 API 负责解密消息。

正如我们在前文[#messages](../identity/concepts.md#messages "mention") 提到的，当前的消息协议是 `V2`；然而，仍然可以解密来自 `V1` 方案的消息。&#x20;

解密 API 允许您通过传递一个 `encryptedMessage` 对象数组一次解密多个消息。

解密 API 旨在在调用`/api/v0/get-messages-stateless` 后端 API 接口之后构建，因此 `encryptedMessage` 的结构与后端响应的结构相匹配。

我们建议跟踪 DeSo 协议前端的 `src/app/backend-api.service.ts` 中的  [`GetMessages()` ](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/backend-api.service.ts#L1293)函数。&#x20;

假设消息是从后端 API 响应的 `OrderedContactsWithMessages`.`Messages` 中获取的，可以按以下方式构建 `encryptedMessage`：

```javascript
encryptedMessage : {
    EncryptedHex: message.EncryptedText,
    PublicKey: message.IsSender ? message.RecipientPublicKeyBase58Check : message.SenderPublicKeyBase58Check,
    IsSender: message.IsSender,
    Legacy: !message.V2,
}
```

解密 API 的另一个用例是解密 NFT 中的可解锁文本。&#x20;

要查看如何实现此功能，我们建议跟踪 DeSo 协议存储库中的 [`DecryptUnlockableTexts()`](https://github.com/deso-protocol/frontend/blob/6d6225a8425f2fe7ad84a222027159333b2c754f/src/app/backend-api.service.ts#L945)。

#### Public keys公钥返回体

| 名称                | 类型                  | 描述        |
| ----------------- | ------------------- | --------- |
| encryptedMessages | \[]encryptedMessage | 加密消息对象列表。 |

#### Derived keys派生秘钥的返回体

| 名称                              | 类型                  | 描述                     |
| ------------------------------- | ------------------- | ---------------------- |
| derivedPublicKeyBase58Check     | string              | 以base58check格式请求解密的公钥。 |
| ownerPublicKeyBase58Check       | string              | 用于识别哪个消息组成员条目用于解密群组消息。 |
| encryptedMessagingKeyRandomness | string              | 需要解密请求。                |
| encryptedMessages               | \[]encryptedMessage | 加密消息对象列表。              |

#### 请求

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  method: 'decrypt',
  payload: {
    accessLevel: 3,
    accessLevelHmac: "0fab13f4...",
    encryptedSeedHex: "0fab13f4...",
    derivedPublicKeyBase58Check: "BC1YLsond7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ",
    ownerPublicKeyBase58Check: "BC1YLdadd7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ",
    encryptedMessagingKeyRandomness: "837fab39...",
    encryptedMessage: [
      {
        EncryptedHex: "0f154bcad...",
        PublicKey: "BC1a4gbK1...",
        IsSender: true,
        Legacy: false
      },
      {
        EncryptedHex: "0afa44bcd...",
        PublicKey: "BC1asKl5j1...",
        IsSender: false,
        Legacy: true
      }, ...
    ]
  },
}
```

#### 响应（需要加密的消息密钥随机性）

如果请求包含 `derivedPublicKeyBase58Check` 并且不包括 `ownerPublicKeyBase58Check` 和 `encryptedMessagingKeyRandomness`，您将获得此响应。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    decryptedHexes: {}
    requiresEncryptedMessagingKeyRandomness: true,
  },
}
```

#### 响应（需要批准）

如果您的用户授权的`accessLevel`访问级别与签署交易所需的访问级别不匹配，您将获得此响应。

要解决此问题，用户需要至少允许访问级别 2。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    approvalRequired: true,
  },
}
```

#### 响应

响应包含一个 `decryptedHexes` 字段，它是一个解密消息的映射，索引为请求中的 `EncryptedHex`。

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    decryptedHexes: {
      "0f154bcad...": "hello world"
      "0afa44bcd...": "in retrospect it was inevitable",
    }
  },
}
```

## jwt

[**访问等级**](broken-reference/)**: 2**

`jwt` 消息创建已签名的 JWT 令牌，可用于验证用户对特定公钥的所有权。

JWT 仅在 10 分钟内有效。

JWT 用于某些后端 API 接口，如`/api/v0/upload-image`。最佳做法是在调用这些接口之前请求 JWT。

#### Public keys公钥返回体

| 名称  | 类型  | 描述           |
| --- | --- | ------------ |
| N/A | N/A | <p><br>无</p> |

#### Derived keys派生秘钥的返回体

| 名称                          | 类型     | 描述                |
| --------------------------- | ------ | ----------------- |
| derivedPublicKeyBase58Check | string | 告知Identity如何签署交易。 |

#### 请求

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  method: 'jwt',
  payload: {
    accessLevel: 3,
    accessLevelHmac: "0fab13f4...",
    encryptedSeedHex: "0fab13f4...",
    derivedPublicKeyBase58Check: "BC1YLsond7iADbrSgryTfXhMEcsF76cXDaWog4aDzoTunDb2DcZ3myZ",
  },
}
```

#### 响应

```javascript
{
  id: '21e02080-0ef4-4056-a319-a66403f33768',
  service: 'identity',
  payload: {
    jwt: "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE2MTk2NDk4MDcsImV4cCI6MTYxOTY0OTg2N30.FKZF8DSwwlnUaW_eRa7Wr1v2QcG7_iDN-NjdqXUcgrSAPg1EdSfpWsLL4GeUiD9zdLUgrNoKU7EsKkE-ZKMaVQ",
  },
}
```

#### 在 Go 中进行验证

如果您想在 Go 中验证 JWT 令牌，可以使用以下代码：

```javascript
func (fes *APIServer) ValidateJWT(publicKey string, jwtToken string) (bool, error) {
	pubKeyBytes, _, err := lib.Base58CheckDecode(publicKey)
	if err != nil {
		return false, errors.Wrapf(err, "Problem decoding public key")
	}

	pubKey, err := btcec.ParsePubKey(pubKeyBytes, btcec.S256())
	if err != nil {
		return false, errors.Wrapf(err, "Problem parsing public key")
	}

	token, err := jwt.Parse(jwtToken, func(token *jwt.Token) (interface{}, error) {
		// Do not check token issued at time. We still check expiration time.
		mapClaims := token.Claims.(jwt.MapClaims)
		delete(mapClaims, "iat")

		// We accept JWT signed by derived keys. To accommodate this, the JWT claims payload should contain the key
		// "derivedPublicKeyBase58Check" with the derived public key in base58 as value.
		if derivedPublicKeyBase58Check, isDerived := mapClaims[JwtDerivedPublicKeyClaim]; isDerived {
			// Parse the derived public key.
			derivedPublicKeyBytes, _, err := lib.Base58CheckDecode(derivedPublicKeyBase58Check.(string))
			if err != nil {
				return nil, errors.Wrapf(err, "Problem decoding derived public key")
			}
			derivedPublicKey, err := btcec.ParsePubKey(derivedPublicKeyBytes, btcec.S256())
			if err != nil {
				return nil, errors.Wrapf(err, "Problem parsing derived public key bytes")
			}
			// Validate the derived public key.
			utxoView, err := fes.mempool.GetAugmentedUniversalView()
			if err != nil {
				return nil, errors.Wrapf(err, "Problem getting utxoView")
			}
			blockHeight := uint64(fes.blockchain.BlockTip().Height)
			if err := utxoView.ValidateDerivedKey(pubKeyBytes, derivedPublicKeyBytes, blockHeight); err != nil {
				return nil, errors.Wrapf(err, "Derived key is not authorize")
			}

			return derivedPublicKey.ToECDSA(), nil
		}

		return pubKey.ToECDSA(), nil
	})

	if err != nil {
		return false, errors.Wrapf(err, "Problem verifying JWT token")
	}

	return token.Valid, nil
}
```
