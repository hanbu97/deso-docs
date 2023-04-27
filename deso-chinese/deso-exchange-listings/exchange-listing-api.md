# 1⃣ 交易所上市：API

开发者社区建议使用开源的 Rosetta API 实现（目前 Coinbase 正在使用）来将 DeSo 集成到交易所中： [https://github.com/deso-protocol/rosetta-deso](https://github.com/deso-protocol/rosetta-deso).

话虽如此，我们在本文档中提供了一套备选的 API，可能更容易使用，而且 DeSo 核心团队计划无限期支持。

现在，世界上任何人都可以运行一个 DeSo 节点，我们认为通过发布一个简单的公共 API，让世界上任何加密货币交易所都能遵循来集成 DeSo，是实现民主化和去中心化的好方法。

本指南将涵盖为了交易所上市 DeSo 所需的所有 API 端点，详细描述和示例。

内容包括：

* 设置节点。
* 使用交易所 API 创建无限制的公钥/私钥对。
* 使用交易所 API 检查 DeSo 公钥的余额。
* 使用交易所 API 在公钥之间转移 DeSo。
* 使用交易所 API 通过交易 ID 查询交易。
* 使用交易所 API 通过公钥查询交易。
* 使用交易所 API 查询节点同步状态。
* 使用交易所 API 通过高度或区块哈希查询区块信息。\\

[快速上手](exchange-listing-api.md#quick-start)部分使用 “curl” 命令提供了以上所有内容的示例。\
\
[完整 API 指南](exchange-listing-api.md#full-api-guide) 部分提供了示例中显示的每个 API 接口的更多详细信息。

_**注意：此 API 仅供交易所使用**_**。**DeSo 节点使用浏览器内签名，使得你的助记词永远不会离开你的浏览器（[_learn more_](../deso-blockchain/privacy-and-security.md)）。相比之下，交易所通常是托管的，因此其中的一些接口代表用户操纵了他们的助记词。

## 快速上手

### 生成助记词种子

首先，您需要生成一个标准的 [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 助记词种子，用于生成公钥/私钥对。

如果您不需要在完全断网的计算机上生成密钥，那么可以使用 [Diamond](https://diamondapp.com) 注册流程来生成助记词。

请注意，在diamondapp上生成助记词时，您的助记词不会离开浏览器。有关此过程的详细信息，请参阅[隐私和安全性](../deso-blockchain/privacy-and-security.md)。

如果您需要离线生成助记词种子，我们建议您使用[这个工具](https://iancoleman.io/bip39/)。使用12个或24个单词的助记词都可以，标准比特币助记词也适用。

在我们的示例中，我们将使用以下内容：

* 助记词：_arrive mixture refuse loud people robot dolphin scissors lift curve better demand_
* 密码 (也被称作"ExtraText"): _password_

### 运行节点

本指南中的所有命令和示例都假设您在本地计算机上运行了一个DeSo节点。

要设置一个节点，只需按照开源的 /run 仓库中的说明操作。如果遇到任何问题，请在[官方 DeSo Discord](broken-reference/):的#nodes频道寻求帮助：

* [https://github.com/deso-protocol/run](https://github.com/deso-protocol/run)\\

请注意，节点软件是跨平台的，应该可以在Linux、Mac和Windows上运行。然而，似乎大多数成功的案例都会选择在至少拥有32GB RAM和至少100GB可用磁盘空间的Linux和Mac机器上运行。\\

注意：您必须在[_dev.env_](https://github.com/deso-protocol/run/blob/190a2380b278689a4db844bb52a31d0450db7d46/dev.env#L265)中将_`READ_ONLY_MODE`_设置为false，才能使某些API调用正常工作。然而，在撰写本文时，还不建议将_`READ_ONLY_MODE`_设置为false来部署生产节点。不过，这种情况应该很快就会改变。请关注[_README_](https://github.com/deso-protocol/run/tree/190a2380b278689a4db844bb52a31d0450db7d46)以获取更新。\\

### 检查节点同步状态

此查询将返回关于节点同步状态等信息。有关更多信息，请参阅[完整的API 指南](exchange-listing-api.md#full-api-guide)部分。

```
curl --header "Content-Type: application/json" --data-raw '{}' \
    http://localhost:17001/api/v1/node-info | python -m json.tool
```

**注意事项:**

* 我们用命令管道符，传递结果到“python -m json.tool”，以便进行“美观打印”，如果您没有安装Python，可以删除此部分命令。\\
* 我们假设节点在与进行此查询相同的计算机上运行。如果节点在另一台计算机上运行，那么应将该计算机的IP替换掉“localhost”。\\

### 生成公钥/私钥对

这将生成一个对应于此账户索引“0”的公钥/私钥对。每个密钥对将映射到特定种子的索引。

要生成更多密钥对，请直接循环迭代“Index”参数：

```
curl --header "Content-Type: application/json" --request POST --data '{
    "Mnemonic":"arrive mixture refuse loud people robot dolphin scissors lift curve better demand",
    "ExtraText":"password",
    "Index": 0
}' http://localhost:17001/api/v1/key-pair  | python -m json.tool
```

**注意事项:**

* 在后台，每个公钥/私钥对都对应到派生路径m/44'/0'/0'/0/{index}。\
  \
  **因此，它们将与使用相同助记词、密码短语和派生路径生成的比特币钱包地址完全相同。**\\
* 此功能返回的公钥和私钥将使用base58校验编码进行编码，在此接口的[完整API指南](exchange-listing-api.md#full-api-guide)部分中进行了更为详细描述。现在，您需要知道的是，您可以将公钥/私钥字符串传递给其他API接口，以检查余额、花费DeSo等。\\
* 使用base58编码的DeSo公钥始终以前缀“BC”开头。使用base58编码的DeSo私钥始终以前缀“bc”（小写）开头。\\
* 此函数返回的DeSo公钥/私钥对示例。注意，Error值为空字符串意味着接口运行成功。\\
* ```
  {
      "Error": "",
      "PrivateKeyBase58Check": "bc6EmekhAbzn2V9BchgRLMRMZW1m8mo7kmvdwjZRB5nnKpgQhWSf4",
      "PrivateKeyHex": "423e1f1fe03469e4173f5a0056f468255358f9200fd5acfa7be8185d2fcb98b4",
      "PublicKeyBase58Check": "BC1YLgAJ2kZ7Q4fZp7KzK2Mzr9zyuYPaQ1evEWG4s968sChRBPKbSV1",
      "PublicKeyHex": "024089f4297576513ce07de8190583154c15b8279a586f7d0663ff3c5391351a1e"
  }
  ```
* 我们用命令管道符，传递结果到“python -m json.tool”，以便进行“美观打印”，如果您没有安装Python，可以删除此部分命令。

_**注意：此API仅供交易所使用。**_DeSo节点使用一个从未接收过您的助记词的不同API，您的助记词永远不会离开您的浏览器。相比之下，交易所通常是托管的，因此其中一些端点代表用户操纵助记词。

_**注意：此 API 仅供交易所使用**_**。**DeSo 节点使用浏览器内签名，使得你的助记词永远不会离开你的浏览器（[_learn more_](../deso-blockchain/privacy-and-security.md)）。相比之下，交易所通常是托管的，因此其中的一些接口代表用户操纵了他们的助记词。\\

### 检查DeSo公钥的余额

```
curl --header "Content-Type: application/json" --request POST --data '{
    "PublicKeyBase58Check":"BC1YLgAJ2kZ7Q4fZp7KzK2Mzr9zyuYPaQ1evEWG4s968sChRBPKbSV1"
}' http://localhost:17001/api/v1/balance | python -m json.tool
```

**注意事项:**

* 这将以“nanos”为单位返回余额，其中1 DeSo = 1,000,000,000 “nanos”。例如，如果此公钥的余额为“1 DeSo”，则此接口将返回1,000,000,000（或1e9 nanos）。\\
* 此接口还会返回UTXO，但对于大多数节点用户来说，这可能用不到。

### 使用公钥/私钥进行DeSo转账

```
curl --header "Content-Type: application/json" --request POST --data '{
    "SenderPublicKeyBase58Check":"BC1YLgAJ2kZ7Q4fZp7KzK2Mzr9zyuYPaQ1evEWG4s968sChRBPKbSV1", 
    "SenderPrivateKeyBase58Check":"bc6EmekhAbzn2V9BchgRLMRMZW1m8mo7kmvdwjZRB5nnKpgQhWSf4", 
    "RecipientPublicKeyBase58Check":"BC1YLgU67opDhT9bTPsqvue9QmyJLDHRZrSj77cF3P4yYDndmad9Wmx", 
    "AmountNanos": 1000000000
}' http://localhost:17001/api/v1/transfer-deso | python -m json.tool
```

**注意事项:**

* 您需要向`SenderPublicKeyBase58Check`发送一些DeSo，否则将会运行失败。\\
  * 您可以在diamondapp.com上购买DeSo，然后使用“Send(发送) DeSo”发送到测试账户，用于进行测试。\\
* 金额必须以“nanos”为单位指定，其中1 DeSo = 1e9 nanos。此示例将1 DeSo从公钥`BC1YLgAJ2kZ7Q4fZp7KzK2Mzr9zyuYPaQ1evEWG4s968sChRBPKbSV1`转移到公钥`BC1YLgU67opDhT9bTPsqvue9QmyJLDHRZrSj77cF3P4yYDndmad9Wmx`。\\
  * 要在不广播到区块链的情况下对交易进行“空运行”，只需将`DryRun: true`添加到参数中。\\
* 将“AmountNanos”设置为负值，如-1，将发送尽可能多的金额。\\
  * 要实现带有“Max(最大可用)”按钮的UI，我们建议使用负的AmountNanos并将DryRun设置为true来访问此接口，获取结果的“花费金额”（净费用），并将其显示给用户。\\
* 此接口将返回已创建交易的信息。请参阅有关此接口的[完整API指南](exchange-listing-api.md#full-api-guide)部分以获取有关返回内容的更多信息。\\
* 还可以自定义交易“费率”。有关更多详细信息，请参阅此接口的[完整API指南](exchange-listing-api.md#full-api-guide)部分。

_**注意：此 API 仅供交易所使用**_**。**DeSo 节点使用浏览器内签名，使得你的助记词永远不会离开你的浏览器（[_learn more_](../deso-blockchain/privacy-and-security.md)）。相比之下，交易所通常是托管的，因此其中的一些接口代表用户操纵了他们的助记词。\\

### 查询公钥的交易记录

```
curl --header "Content-Type: application/json" --request POST --data '{
    "PublicKeyBase58Check":"BC1YLgAJ2kZ7Q4fZp7KzK2Mzr9zyuYPaQ1evEWG4s968sChRBPKbSV1",
    "IDsOnly": true
}' http://localhost:17001/api/v1/transaction-info | python -m json.tool
```

**注意事项:**

* 交易ID是使用base58校验编码的交易的sha256哈希，是一笔交易的唯一标识。\\
* 此操作可获取特定公钥对应的的所有交易ID按从旧到新的顺序排序。
* 若要获取完整交易而非仅ID，只需将`IDsOnly`设置为`false`而非`true`，或完全不包含在请求体中。\\
* 此接口仅在节点以[TXINDEX标志](https://github.com/deso-protocol/run/blob/190a2380b278689a4db844bb52a31d0450db7d46/dev.env#L123)设置为true时启动才可用，这是默认设置。\\
  * 您还必须等待`TXINDEX`生成，这可能需要几个小时。使用UpdateTxIndex可在日志中监控进度。\\
* 请查看此接口的[完整API指南](exchange-listing-api.md#api-v1-transaction-info)部分，了解此接口返回的信息。

### 使用交易ID查找交易

使用交易ID获取特定交易的信息。您可以从先前描述的[使用公钥/私钥进行DeSo转账](exchange-listing-api.md#shi-yong-gong-yao-si-yao-jin-hang-deso-zhuan-zhang)等其他接口获取交易ID。

```
curl --header "Content-Type: application/json" --request POST --data '{
    "TransactionIDBase58Check": "3JuEUE5QSkjyuLwY8WUjS3MRjMbaNEd4nE63VugpU17HMzJW7vbrJP"
}' http://localhost:17001/api/v1/transaction-info | python -m json.tool
```

**注意事项:**

* 这与用于查找公钥交易的接口相同。当设置了`PublicKeyBase58Check`参数时，`TransactionIDBase58Check`参数应为空，且会被忽略。\\
* 此接口仅在节点以[TXINDEX标志](https://github.com/deso-protocol/run/blob/190a2380b278689a4db844bb52a31d0450db7d46/dev.env#L123)设置为true时启动才可用，这是默认设置。\\
* 请查看此接口的[完整API指南](exchange-listing-api.md#api-v1-transaction-info)部分，了解此接口返回的信息。

### 根据区块哈希或高度获取区块信息

下面的操作将返回与高度为10715的区块关联的所有信息。如果链未同步到此点，则返回错误。

```
curl --header "Content-Type: application/json" --request POST --data '{
    "Height":10715
}' http://localhost:17001/api/v1/block | python -m json.tool
```

\
与上一个示例相同，但是通过哈希而非高度查询区块。

```
curl --header "Content-Type: application/json" --request POST --data '{
    "HashHex":"0000000000306a10b85a0bfd801479f1f2227ebaa8bdd5c61da4736dff319362"
}' http://localhost:17001/api/v1/block | python -m json.tool
```

\
有关更多信息，请查看这些接口的[完整API指南](exchange-listing-api.md#api-v1-block)部分。\\

## 完整API指南

_**注意：此 API 仅供交易所使用**_**。**DeSo/Diamond 节点使用浏览器内签名，使得你的助记词永远不会离开你的浏览器（[_learn more_](../deso-blockchain/privacy-and-security.md)）。相比之下，交易所通常是托管的，因此其中的一些接口代表用户操纵了他们的助记词。\\

_**注意：开发者社区还在努力完成与**_ [_**Rosetta**_](https://www.rosetta-api.org) _**的集成，以进一步完善此API。**_

### /api/v1/key-pair

您可以使用标准的BIP39助记词生成公钥/私钥对。每个公钥/私钥对对应于一组助记词的特定推导索引。

这意味着对于特定助记词，例如索引“5”，将始终生成相同的公钥/私钥对。因此，通过在特定助记词上迭代索引，可以生成无限数量的公钥/私钥对。

所有公钥/私钥都可以与比特币公钥/私钥互操作。

这意味着它们表示secp256k1曲线上的一个点（与比特币所使用的相同）。\
\
在底层，DeSo使用BIP39助记词并使用BIP32派生路径m/44'/0'/0'/0/{index}生成公钥/私钥对，其中“index”是要生成的公钥/私钥的索引。

这意味着节点生成的DeSo公钥/私钥对将始终与[Ian Coleman](https://iancoleman.io/bip39/)生成的公钥/私钥相吻合。

因此，工程师可以通过使用diamondapp.com或[Ian Coleman](https://iancoleman.io/bip39/)生成助记词，使用该助记词创建密钥对，然后验证生成的公钥/私钥对与diamondapp.com或[Ian Coleman](https://iancoleman.io/bip39/)上显示的内容相符来进行“合理性检查”。

```
路径: /api/v1/key-pair
方法: POST
POST参数:
    // BIP39助记词和额外文本。助记词可以是12个单词或 24个单词。ExtraText 是可选的。
    Mnemonic  string
    ExtraText string
    // 要生成的公钥/私钥对的索引
    Index uint32
返回值:
  // 如果成功，则为空。否则，包含发生的错误的描述。
  Error string
  // 使用base58检查编码的DeSo公钥，
  // 前缀为[3]byte{0x11, 0xc2, 0x0}
  // 该公钥可以在后续的API调用中传递以查询余额等。
  // 所有编码的DeSo公钥都以“BC”开头。
  PublicKeyBase58Check string
  // 以普通十六进制字符串编码的DeSo公钥。这应与
  // 不应将此内容传递给后续的API调用，它仅作为参考提供，
  // 主要用于合理性检查。
  PublicKeyHex string
  // 使用base58检查编码的DeSo私钥，
  // 前缀为[3]byte{0x4f, 0x6, 0x1b}
  // 该私钥可以在后续的API调用中传递以花费DeSo等。
  // 所有DeSo私钥以“bc”字符开头。
  PrivateKeyBase58Check string
  // 以普通十六进制字符串编码的DeSo私钥。请注意，
  // 这与Ian Coleman工具生成的内容不会直接匹配，
  // 因为该工具显示的私钥使用的是比特币的WIF格式，
  // 而不是原始十六进制。要将此原始十六进制转换为比特币的WIF格式，
  // 您可以使用这个简单的Python脚本：
  // https://github.com/geniusprodigy/bitcoin-convertpvk
  // 不应将此内容传递给后续的API调用。它仅作为参考提供，
  // 主要用于合理性检查。
  PrivateKeyHex string
```

### /api/v1/balance

通过将公钥传递给以下接口，可以检查特定公钥的余额。 已花费的交易输出不会由此接口返回。

要对已花费的交易输出执行操作，必须使用“transaction-info”接口。

```
路径: /api/v1/balance
方法: POST
POST 参数:
  // 使用base58检查编码的DeSo公钥（以“BC”开头）。
  // 提供此字段时，其他参数将被忽略。
  PublicKeyBase58Check string
  // 只考虑具有大于或等于指定确认数的UTXO。
  // 默认值为零，表示考虑所有UTXO，包括内存池中的UTXO。
  Confirmations uint32
返回值:
  // 如果成功，则为空。否则，包含发生的错误的描述。
  Error string
  // 以“nanos”为单位查询的公钥余额。注意
  // 每个DeSo对应1e9个“nanos”，因此，如果余额是“1 DeSo”，那么
  // 这个值将为1e9。
  ConfirmedBalanceNanos int64
  // 以“nanos”为单位查询的公钥未确认余额。如果确认数设置为
  // 大于零的值，则此字段设置为零。
  UnconfirmedBalanceNanos int64
  // DeSo使用类似于比特币的UTXO模型。因此，查询
  // 余额将返回特定公钥的所有UTXO以方便操作。请注意，UTXO只是对
  // 之前交易中特定输出索引的引用。
  UTXOs [{
    // 一个字符串，唯一标识以前的交易。这是
    // 使用base58检查编码对交易信息进行sha256哈希。
    // 如果此UTXO是区块奖励，则为空字符串。
    TransactionIDBase58Check string
    // 此交易中与传入公钥可花费的输出相对应的索引。
    // 以“nanos”为单位，此UTXO可花费的金额= 1e9 DeSo。
    Index int64
    // 以“nanos”为单位，此UTXO可花费的金额1 DeSo = 1e9 nanos。
    AmountNanos uint64
    // 有权使用此UTXO中存储的金额的公钥。
    PublicKeyBase58Check string
    // 此UTXO拥有的确认数。如果UTXO未确认，则设置为零。
    Confirmations int64
    // 此UTXO是否为区块奖励。
    IsBlockReward bool
  }, ... ]
```

### /api/v1/transfer-deso

使用这个简单的API调用，可以将DeSo从一个公钥转移到另一个公钥。要转移DeSo，必须提供一个公钥/私钥对。

DeSo使用类似于比特币的UTXO模型，但DeSo交易通常比比特币交易简单，因为DeSo始终将“来自公钥”用作“找零”公钥（这意味着它默认不“旋转”密钥）。

例如，如果一个交易从PubA向PubB发送10个DeSo，其中5个DeSo用于“找零”且1个DeSo作为“矿工费”，那么交易将如下所示：

```
输入: 16 DeSo (要发送的10 DeSo，5 DeSo作为找零，1 DeSo作为交易费)
PubB: 10 DeSo (从A发送到B的金额)
PubA: 5 DeSo (退回给A的找零)

隐式地，1个DeSo作为矿工费支付。矿工费隐式地计算为（总输入-总输出），就像在比特币中一样。
```

通过在调用接口时指定负金额，可以发送最大数量的DeSo。

我们建议首先将`DryRun`设置为`true`运行端点，检查输出，然后将`DryRun`设置为`false`运行它，这将实际广播交易。

<pre><code><strong>路径: /api/v1/transfer-deso
</strong>方法: POST
POST 参数:
    // 使用 base58 check 编码的 DeSo 私钥（以 "bc" 开头）。
    SenderPrivateKeyBase58Check string
    // 使用 base58 check 编码的 DeSo 公钥（以 “BC” 开头），将接收发送的 DeSo。
    RecipientPublicKeyBase58Check string
    // 以 “nanos” 为单位发送的 DeSo 数量。请注意，“1 DeSo” 等于1e9 nanos
    // 因此要发送 1 DeSo，此值需要设置为 1e9。
    AmountNanos int64
    // 本次交易使用的费率。如果未设置，将使用默认费率。
    // 可以使用下面的 “DryRun” 参数进行检查。
    MinFeeRateNanosPerKB int64
    // 当设置为 true 时，事务会在响应中返回，但不会
    // 实际广播到网络。对于测试非常有用。
    DryRun bool
返回值:
  //  如果成功，则为空。否则，包含发生的错误信息。
  Error string
  // 执行转账的交易。如果 DryRun 设置为 true，则不会广播。
  Transaction {
    // 一个字符串，唯一标识此交易。这是一个 sha256 哈希
    // 使用 base58 检查编码对交易数据进行编码。
    TransactionIDBase58Check string
    // 交易数据的原始十六进制。这可以从本对象的人类可读部分完全构造。
    RawTransactionHex string
    // 本次交易的输入。
    Inputs [{
        // 交易输入包括交易 ID 和来自该交易的输出索引。
        TransactionIDBase58Check string
        Index int64
      }, ... ]
    Outputs [
      // 交易输出仅为公钥和分配给该公钥的金额以 “nanos” 为单位，其中 1 DeSo = 1e9 nanos。
      {
        PublicKeyBase58Check string
        AmountNanos int64
      }, ... ]
    // 以十六进制格式的交易签名。
    SignatureHex string
    // 对于基本转账，始终为 “0”
    TransactionType int64
    // 对于基本转账，始终为空
    TransactionMeta {}
    // 交易被挖掘的区块的哈希。如果该交易未确认，此字段将为空。
    // 要查找交易的确认数，只需将此值传入 "block" 接口。
    BlockHashHex string
  }
  TransactionInfo {
    // 输入总和
    TotalInputNanos uint64
    // 发送至 “RecipientPublicKeyBase58Check”的金额
    SpendAmountNanos uint64
    // 返回至 “SenderPublicKeyBase58Check”的金额
    ChangeAmountNanos uint64
    // 本次交易的总费用和费率（以nanos每KB计）。
    FeeNanos uint64
    FeeRateNanosPerKB uint64
    // 将匹配作为参数传递的公钥。注意， SenderPublicKeyBase58Check 从此交易中接收找零。
    SenderPublicKeyBase58Check string
    RecipientPublicKeyBase58Check string
  }


</code></pre>

### /api/v1/transaction-info

如果有一个 `TransactionIDBase58Check 的值`，例如调用“transfer-deso”接口获得的结果，可以通过将此交易 ID 传递给节点来获取对应可读的“Transaction 对象”。请注意，如果 `TXINDEX` 设置为 false，则此接口将出错。

如果将 `TXINDEX` 传递给节点，但它尚未完成同步区块链，则此接口可能返回不完整的结果。

/node-info 接口可用于检查节点的同步进程（通常，同步仅需要一两分钟）。

如果有人有一个 `PublicKeyBase58Check`（以 “BC” 开头）的公钥，可以通过这个接口获取与该公钥关联的所有 TransactionIDs，按最早到最新的顺序进行排列（这将包括地址作为发送者和接收者的交易）。\
\
还可以选择在同一调用中获取所有交易的完整 Transaction 对象。

```
路径: /api/v1/transaction-info
方法: POST
POST 参数:
  // 一个字符串，唯一标识此交易。例如，像之前调用 “transfer-deso” 获取。
  // 当设置了 PublicKeyBase58Check 时忽略。
  // 当直接使用其 ID 查找交易时，我们还会扫描内存池。
  // 这使得 “区块浏览器” 可以轻松显示与特定 ID 关联的交易。
  TransactionIDBase58Check string
  // 使用 base58 check 编码的 DeSo 公钥（以“BC” 开头），用于获取交易 ID。
  // 当设置时，TransactionIDBase58Check 被忽略。
  PublicKeyBase58Check string
  // 是否仅返回每个交易的完整交易信息或仅返回 TransactionIDHex。
  // 当此选项未设置时，返回完整交易。
  IDsOnly bool
返回值:
  // 如果成功，则为空。否则，包含发生的错误信息。
  Error string
  // 从最早到最新的所有与此公钥关联的交易信息。
  // 如果 “IDsOnly” 设置为 true，则每个 Transaction对象将仅包含 TransactionIDBase58Check。
  // 否则，还将设置所有其他字段。
  Transactions [
    Transaction {
      // 始终需要设置。
      TransactionIDBase58Check string
      // 其余字段如前所定义，但仅在IDsOnly未设置或者值为false的时候需要
      ...
  }, ... ]
```

### /api/v1/node-info

可以使用此接口查询节点的区块链和同步状态的通用信息。

区块链采用“先下载区块头”的同步方式，这意味着它首先下载所有 DeSo区块头，然后再下载所有区块。\
\
这意味着，当节点首次同步时，最佳“头部链”的末端可能会领先于其最近下载的区块。\
\
除了同步 DeSo 区块头头和 DeSo 区块外，DeSo 节点还将同步所有最新的比特币区块头，以支持其内置的去中心化比特币<>DeSo 互换机制

因此，此接口还返回有关节点最佳比特币区块头链的信息，这与其 DeSo 链区块头的内容是不同的。

```
路径: /api/v1/node-info
方法: POST
返回值:
  DeSoStatus {
    // 节点当前运行情况的概述。
    State string

    // 通常，我们分别跟踪最新的头部和最新的区块，因为头部优先的同步方式可能导致最新的头部
    // 与最新的区块略有偏差。
    LatestHeaderHeight     uint32
    LatestHeaderHash       string
    LatestHeaderTstampSecs uint32

    LatestBlockHeight     uint32
    LatestBlockHash       string
    LatestBlockTstampSecs uint32

    // 除非主链完全更新，否则这个值不为零。在某些情况下，我们不知道当前主链的时间戳，
    // 可能会出现估算值。
    HeadersRemaining uint32
    // 除非主链完全更新且已下载所有相应的区块，否则这个值不为零。
    BlocksRemaining uint32
  }
  BitcoinStatus {
    // 我们下载比特币区块头，以支持应用程序内置的去中心化
    // 比特币 <> DeSo 互换，这使用户可以将比特币转换为 DeSo，而无需信任第三方。
    //
    // 此响应部分的结构与 DeSoStatus 相同，只是
    // 区块信息不会被填充，因为我们仅下载
    // 比特币头部而不是完整的比特币区块。
  }
  DeSoOutboundPeers []PeerResponse {
    IP           string
    ProtocolPort uint16
    JSONPort     uint16
    IsSyncPeer   bool
  }
  DeSoInboundPeers []PeerResponse{
    // 与上面的结构相同
  }
  DeSoUnconnectedPeers []PeerResponse{
    // 与上面的结构相同
  }
  BitcoinSyncPeer []PeerResponse{
    // 与上面的结构相同
  }
  BitcoinUnconnectedPeers []PeerResponse{
    // 与上面的结构相同
  }
  // 节点当前向其发送区块奖励的公钥。
  // 如果没有指定公钥，那么节点将不会进行挖矿。
  MinerPublicKeys []string
```

### /api/v1/block

可以通过区块哈希或高度来查询一个区块的信息。要获取链中的所有区块，只需通过从零开始循环查询此接口不同高度直到区块末端。

区块长度和末端哈希可以通过 /node-info 接口获得。

```
路径: /api/v1/block
方法: POST
POST 参数:
  // 区块高度。0 对应创世区块。如果高度超过末端，将返回错误。
  // 如果设置了 HashHex，则忽略此字段。
  Height int64
  // 要返回的区块的哈希。如果设置了此项，将忽略高度。
  HashHex string
  // 当设置为 false 时，只返回所请求区块的头部信息，而不是完整的区块。否则，返回完整的区块。
  FullBlock bool
返回值:
  // 如果成功，则为空。否则，包含发生的错误的描述。
  Error string
  // 区块头部中包含的信息。
  Header {
    // 查询到的区块的哈希。
    BlockHashHex string
    // 通常设置为零
    Version uint32
    // 链中上一个区块的哈希。
    PrevBlockHashHex string
    // 区块中包含的所有交易的 Merkle 根。
    TransactionMerkleRootHex string
    // 区块挖掘时间的 Unix 时间戳（以秒为单位）。
    TstampSecs uint32
    // 与此头部相对应的区块高度。
    Height uint32
    // Nonce 以little-endian(小端) 32 位整数编码。如果挖掘一个区块需要超过 2^32
    // 个哈希，则可以调整区块奖励的 ExtraData 字段以改变 Merkle 根，从而为矿工提供一组新
    // 的 2^32 个头部 Nonce 以尝试。请注意，我们没有使用 64 位（或更多），因为保持头部小对
    // 于轻客户端的效率很重要，而且每 2^32 个值只需轻松调整 ExtraData 即可。
    Nonce uint32
  }
  // 一个 Transaction 对象列表，其中 Transaction 对象如前所述
  Transactions [
    Transaction {
    }, ... 
  ]
```
