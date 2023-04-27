---
description: 跨链互操作性的未来
---

# 6⃣ 智能服务

当Brian Armstrong创建Coinbase时，他做出了一个非常具有争议性的决定：

他认为，采用集中化的方法建立加密货币交易所，短期内将带来更好的用户体验，从而在竞争中胜过更去中心化的方案。

如今看来这似乎是理所当然的，因为那时候你甚至无法建立一个去中心化的交易所，但在当时这被视为异端。

建立一个中心化的加密产品的决定是如此有争议，以至于Brian为此[失去了他的首位合伙人](http://wired.com/2014/03/what-is-bitcoin/)。从长远来看，实用性往往会胜出，但在短期内，将去中心化的意识形态往往让人难以抉择。&#x20;

我们主张一个类似的具有争议性的观点：**我们认为，今天大多数用智能合约实现的计算问题将会在中心化但可组合的智能服务中完成，正如我们将要描述的那样。**

区块链仍将用于存储**资产**和**内容**，但我们认为**计算**将几乎完全转移到链下。

这是因为智能服务将使Web2开发者无需学习任何新的编程语言，允许原生跨链通信和组合，并且比智能合约具有更好的扩展性。

这些优势是以一定程度的中心化和失去一些相对于智能合约的可信度为代价的，但我们相信开发者和市场普遍会更喜欢智能服务而非智能合约。

### 智能合约很难 <a href="#smart-contracts-are-hard" id="smart-contracts-are-hard"></a>

如今，如果你想编写Web3应用程序，大多数领域里的人会让你去编写智能合约。

要编写它们，你不仅需要学习一种新的编程语言，如Solidity或Rust，还需要适应一种充满陷阱的全新“事件驱动”编程范式。

在你学会如何编写代码并处理所有陷阱之后，你还需要尽量减少存储，因为智能合约链没有配备强大的、经济高效的存储和索引功能。

即使如此，一旦你拥有了一个工作中的智能合约，它也只能限制在一个区块链上。以太坊智能合约无法直接调用Avalanche或Solana智能合约，反之亦然。

对于大多数开发者来说，智能合约非常困难，我们认为它们已经使进入Web3的门槛比实际需要的要高得多。

跨链互通性的缺失和不同链之间的可组合性进一步限制了开发者可以构建的应用。

_我们认为有更好的方法_

具体来说，一种实现智能合约所提供功能的方法，但只使用Web2 API和Javascript/Python，而数百万Web2开发者已经对这些工具非常熟悉。

想象一下，如果可以使用数百万Web2开发者广泛了解的工具来构建Web3应用，将会产生多少项目。

更重要的是，如果你的代码可以无缝地连接所有区块链，甚至是那些本身不支持智能合约的区块链，比如比特币，会怎样？

我们将这种新范式称为**智能服务**，我们相信它们将为绝大多数Web3应用提供动力，从根本上取代智能合约成为开发者构建应用的主要方式。

为什么？因为正如我们将讨论的，与智能合约相比，智能服务在提高开发者的可访问性、互操作性、可组合性和可扩展性方面做得更好。

### [​](https://deso-docs.vercel.app/docs/blockchain/smart-services#on-chain-vs-off-chain-the-epic-debate)链上与链下：史诗般的辩论 <a href="#on-chain-vs-off-chain-the-epic-debate" id="on-chain-vs-off-chain-the-epic-debate"></a>

在讨论智能服务的细节之前，有必要指出，智能服务在一个更高层次上，为了提高开发者的**可访问性、跨链互通性、可组合性和可扩展性**，而牺牲了部分去中心化和抗审查性。

这实际上触及了区块链领域一个更为根本的问题：

什么时候使用区块链，什么时候在中心化的Web2服务器上进行操作更好？



随着时间的推移，越来越多的加密货币开始从链上转移到链下。

即使是近期流行的基于区块链的NFT产品，在图片/视频内容方面也几乎总是存储在链下，拍卖活动也通常在链下进行。

但这种趋势将把我们引向何方？例如，Instagram有朝一日会以完全中心化的方式托管NFT，就像今天托管图片和视频一样吗？我们认为不会，至少不完全是这样。

总的来说，随着Web3的未来逐步展现，我们认为**资产**和**内容**将被存储在链上，而**计算**将转移到链下，这使得智能合约在未来将远不如今天有用。

例如，我们相信你的通证、你的NFT以及你的社交图谱将受益于链上存储。但是，基于计算的服务，如借贷、交易、质押等，将转移到链下的智能服务中。

鉴于此，我们认为区块链将继续具有以下几个方面的用途：

* **抗审查性**
  * 当资产和内容存储在区块链上时，没有任何一个公司或应用可以冻结某人的资产或审查他们的言论。这似乎表明，用户更愿意将重要资产（如通证）存储在区块链上，即使他们使用智能服务进行类似交换、众筹或贷款的计算。\\
* **便捷性**
  * 尽管智能服务可以通过Web2 API互通，但将资产放在区块链上几乎可以保证它们永远可以被你和第三方开发者访问。这也意味着一个智能服务中的通证或帖子将以与今天的比特币在加密货币交易所之间转移的方式相类似的方式出现在所有其他智能服务中。\\
* **克服监管限制**
  * 通过完全消除对中心化的依赖，区块链有时可以满足中心化服务无法满足的监管要求。例如，在区块链上发行通证或NFT可以免除许多法律风险。

### Web2 开发者 <a href="#web2-developers" id="web2-developers"></a>

尽管将内容存储在链上看起来有许多好处，但我们相信，对Web2开发者来说，智能服务提供的易开发性使得在大多数情况下难以证明使用区块链的必要性，除非用户需要存储的最重要的状态信息。

为什么？想想看：如果智能服务只需要开发者掌握JavaScript和Web2 API，那么我们就可以利用数百万开发者的力量，他们现在还不了解Solidity或Rust，无法为web3做出贡献。

如果有机会第一次编写智能服务，我们相信绝大多数开发者会在计算部分牺牲区块链的优势，选择快速交付产品，就像Brian Armstrong多年前在Coinbase所做的那样。

那么,你更愿意押注谁能找到Web3的杀手级应用:

_数百万Web2开发者_编写_无限制的JavaScript_，还是_数千Solidity/Rust开发者_在Gas优化限制下运作?

鉴于上述情况，有人可能合理地问：如果大量的计算转移到链下的智能服务，智能合约还有什么用?

总的来说,我们认为智能合约在定义新的资产和内容方面很有用,如ERC-20(代币)或ERC-721(NFT)。

如果您想对以太坊代币进行操作，例如，您仍然需要访问以太坊区块链上的ERC-20智能合约来转移资产，即使更复杂的计算正在您的智能服务中进行。

然而,随着时间的推移，值得注意的是，智能合约区块链将开始与在效率上超过智能合约实现的特定领域定制区块链竞争，例如DeSo。

因此，尽管智能合约可能仍将是发现新的基于区块链产品的有用工具，但我们认为，对于需要高吞吐量的应用来说，它们最终可能将其资产和内容转移到根据当前扩展需求进行定制的区块链上，这种情况将会普遍存在，给传统区块链带来显著的风险。

### [​](https://deso-docs.vercel.app/docs/blockchain/smart-services#what-is-a-smart-service)什么是智能服务？ <a href="#what-is-a-smart-service" id="what-is-a-smart-service"></a>

让我们详细了解一下智能服务。

![智能服务](https://uploads-ssl.webflow.com/6148aea00f7f907469e373ad/61d5bfa51ac5554fd45895d6\_Deso%20Mocks-05.jpeg)

智能服务听起来似乎是个高级术语，但实际上我们只是指满足一定基本约束的简中心化网络服务。

我们将讨论这些约束，它们使得各个智能服务之间可以实现可发现性和互操作性，无论它们使用的是哪种底层区块链。以下列出了这些约束。

任何满足以下条件的网络服务都可以视为智能服务，无论是使用NodeJS服务器还是流行的网络框架（如Django）编写的：

* 智能服务具有其他服务可以调用的传统Web2域名，例如mysmartservice.com
* 智能服务具有一个REST API，至少实现了以下关键接口：

#### `/get-address`[​](https://deso-docs.vercel.app/docs/blockchain/smart-services#get-address) <a href="#get-address" id="get-address"></a>

*   每个智能服务至少有一个由地址或公钥标识的链上钱包。这使得智能服务可以像链上智能合约一样接受用户的资金，并进行任意操作。\\

    所有智能服务都定义了`/get-address接口`，它只返回用户可以发送资金到与智能服务进行交互的地址。\\

    换句话说，`/get-address接口`使智能服务可以被相互发现。\\

    重要的是，智能服务不受特定区块链的约束。如果智能服务愿意，它可以返回多个地址，每个地址对应一个不同的链。\\

    用户可以向以太坊地址发送ETH或向DeSo地址发送DESO，假设智能服务在每种情况下都会做正确的事情。并非所有智能服务都会这样做，但它们有选择这样做的权利。\\

    存入智能服务地址的资金可以包含一个通用的键值元数据映射，由下面描述的 `trigger()`函数使用。

#### `/get-info`[​](https://deso-docs.vercel.app/docs/blockchain/smart-services#get-info) <a href="#get-info" id="get-info"></a>

*   返回一个键值映射，包含有关智能服务的重要信息。这也可以包括智能服务的简单描述，说明其工作原理和应该执行的操作。

    智能服务可以通过其他REST API接口实现其他功能。\\

    然后可以定义标准，以引入实现已知功能的智能服务之间的互操作性（请注意，智能服务的价值更多地在于开发的便利性，而不是API的标准化）。\\

    存入智能服务地址的资金可以包含一个通用的键值元数据映射，由下面描述的`trigger()`函数使用。

#### `trigger()`[​](https://deso-docs.vercel.app/docs/blockchain/smart-services#trigger) <a href="#trigger" id="trigger"></a>

*   智能服务定义了一个可以用任何语言编写的 `trigger(metadata)`函数，每当向`/get-address`返回的智能服务地址之一发生存款时，该函数会自动调用。\\

    `trigger()`函数获取一个与存款相关的元数据参数，包括原始交易、存款地址和存款金额。\\

    `trigger()`函数是智能服务框架的魔力所在。通常，开发者需要扫描他们感兴趣的区块链交易，这是具有挑战性、耗时且效率低下的。而使用智能服务，一切都设置得很好，所以当检测到相关交易时，`trigger()`函数会被调用，开发者只需要填写它即可。\\

    这个函数虽然不是技术上必需的，但它是大多数智能服务功能的核心。换句话说：有人存款，然后智能服务做一些事情。\\

    它还使得智能服务在功能上直接类似于智能合约，使它们对现有智能合约程序员来说更加熟悉。\\

    再次强调，`trigger()`函数也可以完全跨链操作，例如，当ETH发送到智能服务的ETH地址时，实现DESO在钱包之间的移动。\\

    随着智能服务框架的成熟，开发者可以开始用像`onETHDeposit()`或`onNFTTransaction()`这样的事件处理器来替代通用的`trigger()`函数，使代码更容易编写和可读。

### 示例[​](https://deso-docs.vercel.app/docs/blockchain/smart-services#examples) <a href="#examples" id="examples"></a>

要了解智能服务框架，最好的方法是通过一些简单的示例来解释。

#### 示例 #1: 代币兑换智能服务 <a href="#example-1-token-swap-smart-service" id="example-1-token-swap-smart-service"></a>

假设你想实现一个允许用户存入ETH并立即将ETH兑换为DESO的智能服务，反之亦然。

以下是这个智能服务的示例：

* 服务将运行在像 `desoethswapper.com` 这样的域名上。
* 实现`/get-address`，返回智能服务的ETH和DESO地址。
* `/get-info` 将通过键值映射返回关于如何使用智能服务的说明。这个映射还可以包括重要信息，如智能服务提供的DESO和ETH之间的当前汇率、收费等。
  * **description**: 调用 `/get-address` 并将DESO发送到DESO地址以换成ETH，将ETH发送到ETH地址以换成DESO。
    * 在名为`destination`的键中包含目标地址。`/get-info` 中指定的汇率表示每ETH可获得的DESO数量。"
  * **exchange\_rate**: 5.0
  * **smart\_service\_type**: "swapper"
    * 这个字段允许其他智能服务对智能服务的行为做出假设。稍后会有更多解释。
    * `trigger(metadata)` 函数可以用任何语言大致定义如下，包括JavaScript或Python：

```
if depositAddress == DESODepositAddress:
  EthAmountToSend = metadata['amount'] / exchange_rate
  EthDestinationAddress = metadata['destination']
  Send EthAmountToSend to EthDestinationAddress

else if depositAddress == ETHDepositAddress:
  DESOAmountToSend = metadata['amount'] * exchange_rate
  DESODestinationAddress = metadata['destination']
  Send DESOAmountToSend to DESODestinationAddress
```

一旦定义了这样的智能服务，任何人都可以调用它来实现ETH与DESO之间的互换。

你只需要域名`desoethswapper.com`，然后`/get-info`调用会告诉你如何使用这个服务。

一旦你知道如何使用这个服务，你只需要向智能服务的区块链地址之一发送资金，附带适当的元数据，这个地址可以从`/get-address`端点获取。

更重要的是，兑换器智能服务的行为可以标准化，以便任何在其`/get-info`调用中将自身标识为兑换器的智能服务都可以与其他智能服务组合。

例如，任何人都可以创建一个类似Uniswap的聚合智能服务，将所有的兑换器智能服务整合到一个简洁的用户界面中。

然后，如果你想将货币A兑换成货币B，类似Uniswap的智能服务可以将你的交易路由到最能高效完成你的订单的智能服务。

#### 示例 #2: 众筹智能服务 <a href="#example-2-crowdsale-smart-service" id="example-2-crowdsale-smart-service"></a>

假设你创建了一个ERC-20代币，我们称之为$MYDAO，你想通过某种巧妙的方式用DESO出售它。

也就是说，你希望人们将DESO存入你的智能服务，并从中获得$MYDAO代币。

这样一个智能服务会是什么样子呢？

* 首先，它会有一个Web2域名，我们称之为`mydaocrowdsale.com`
* `/get-address` 只需返回智能服务的DESO地址，因为这个智能服务只接受DESO。
* `/get-info` 将描述众筹的条款，并告诉用户需要包含哪些元数据
  * **description**: 调用 `/get-address` 并将DESO发送到返回的地址以购买`$MYDAO`代币。
    * 将您的`$MYDAO`地址与`destination`键一起包含在存款的元数据中。每售出100万个`$MYDAO`代币，价格翻倍。
    * `$MYDAO`的当前价格以DESO计价，并在exchange\_rate字段中返回：`exchange_rate: 10.0`
  * **smart\_service\_type**: "crowdsaler"
* `trigger(metadata)` 函数将在每次存款时调用，并完成用户的购买。

其逻辑如下：

{% code overflow="wrap" %}
```
// Track ToenAmountSold as a global variable outside of the trigger() function so
// so that it can be referenced and incremented as the sale progresses.
// ---
// The exchange rate will be computed based on the amount of the token that has been // sold.

TokenAmountToSend = computeTokenAmountToSend(metadata['amount'], TokenAmountSold)
TokenDestinationAddress = metadata['destination']
Send TokenAmountToSend to TokenDestinationAddress

// Update the amount sold, which will increase the exchange rate for subsequent
// purchases.

TokenAMountSold += TokenAmountToSend
```
{% endcode %}

显然，通过简单修改`trigger()`函数，可以实现任意复杂的众筹活动。

此外，这些众筹活动可以跨足多个区块链，这意味着即使他们出售的是DESO代币，也可以用ETH为他们的项目筹集资金，反之亦然。

#### 示例 #3: ERC-20 智能服务 <a href="#example-3-erc-20-smart-service" id="example-3-erc-20-smart-service"></a>

正如讨论过的，由于智能服务比智能合约更中心化，我们认为它们从出发点就更倾向于计算，而不是资产和内容。

这意味着，一个ERC-20代币可能还是更适合在像以太坊或DeSo（通过DeSo代币）这样的区块链上，因为这将使持有者的余额具有便携性和抗审查性。

尽管如此，为了突显智能服务的强大功能，我们认为有必要指出，甚至可以将类似ERC-20的标准完全定义为一个实现标准代币转移功能的REST API端点的智能服务（`/total-supply`，`/balance-of`，`/allowance`，`/transfer`，`/approve`和`/transfer-from`）。

如果你的智能服务实现了这组端点，你的智能服务就可以无缝地集成到一个类似Uniswap的“聚合器”智能服务中，以允许交易你的智能服务的代币。

然后，你可以将你的智能服务的Web2域名插入到Uniswap智能服务中，Uniswap智能服务将知道如何调用你的智能服务的标准化REST API端点，以启用交易。

同样，Uniswap智能服务并不受限于特定的区块链。理论上，它可以在各种链上的资产之间进行交易，完全不受限制。

此外，完全作为智能服务定义的ERC-20不会受到基于ETH的ERC-20代币所承受的高昂Gas费的影响。

### 部署智能服务 <a href="#deploying-smart-services" id="deploying-smart-services"></a>

由于智能服务可以用任何语言编写和部署，因此技术上没有必要采用特定的部署方式。你可以以任何你想要的方式部署Web服务，只要其API可以在互联网上访问，其他智能服务就能发现和与之互操作。

尽管如此，我们正在开发一种“一键部署”功能，它允许用户填写简单的Javascript函数，然后将该代码立即部署到诸如Firebase之类的平台上。

从长远来看，可以想象这样一个平台，Javascript代码本身会自动部署到托管平台上，抽象出部署过程，使代码完全描述智能服务的功能。

这样的平台可以确保部署的代码与智能服务中实际运行的代码相匹配，从而提供比今天的智能合约更强的保证。

从长期来看，智能服务托管提供商之间的竞争应该是高度竞争性的，因此在很大程度上与今天的Web2托管一样相当分散。

### [​](https://deso-docs.vercel.app/docs/blockchain/smart-services#a-note-on-layer-2-solutions)关于二层网络解决方案的说明 <a href="#a-note-on-layer-2-solutions" id="a-note-on-layer-2-solutions"></a>

以太坊的二层解决方案，如Arbitrum或ZK-Rollups，在可扩展性方面实现了与智能服务类似的改进，并在无需信任方面有所提高，但这是以增加其构建应用程序的难度为代价的。

例如，您无法仅使用JavaScript和Web2 API编写定制的ZK-Rollups应用程序。

因此，与智能服务相比，二层解决方案无法吸引数百万现有的Web2开发者，我们认为这正是链下发展的最大价值所在。

### 结论[​](https://deso-docs.vercel.app/docs/blockchain/smart-services#conclusion) <a href="#conclusion" id="conclusion"></a>

智能服务在开发者**可访问性、互操作性、组合性和可扩展性**方面优于智能合约，但代价是中心化。

我们认为，这使得智能服务对开发者更具吸引力，很有可能未来的杀手级应用将会基于智能服务构建，而非智能合约。

一旦出现这种情况，我们相信区块链将主要用于存储资产和内容，而计算将转移到链下的智能服务中。
