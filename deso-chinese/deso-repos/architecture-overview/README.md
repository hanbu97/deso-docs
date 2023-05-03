# 1⃣ 架构概述

## 简介: DeSo 仓库

运行在 DeSo 区块链上的应用程序（如  [Diamond](https://diamondapp.com) ）通常包含四个组件：前端、后端、核心和身份。

这个软件架构包含了运行一个像 Diamond 这样的 Web3 分布式社交平台所需的全部内容。

这种架构使您能够运行自己的 DeSo 节点并访问所有区块链数据。

DeSo 基金会准备了一些示例代码供您查看，您可以运行它们快速**创建自己的社交网络**！

* **Core Protocol核心协议:** [github.com/deso-protocol/core](https://github.com/deso-protocol/core)
  * 这是一个包含所有 DeSo 背后的“共识”代码的 Golang 仓库。它旨在作为一个嵌入式库，供想要在 DeSo 上构建项目的项目使用。\\
* **Backend后端:** [github.com/deso-protocol/backend](https://github.com/deso-protocol/backend)
  * 后端仓库将核心作为库嵌入，并在其基础上公开丰富的 API，以支持事务构建、将事务提交到区块链、存储用户数据等。从某种意义上说，它是第一个基于核心 DeSo 区块链构建的“演示”应用。\\
* **Frontend前端:** [github.com/deso-protocol/frontend](https://github.com/deso-protocol/frontend)
  * 这是一个类似于 diamondapp.com 前端的 Angular **演示应用**。它使用后端仓库公开的 API 支持所有查询。\\
* **Identity身份:** [github.com/deso-protocol/identity](https://github.com/deso-protocol/identity)
  * 这是一个轻量级的可嵌入式应用，可以在前端 Angular 应用中作为 iFrame 加载，处理所有签名功能。

下面是一个简单的图示，展示了这些仓库是如何组合在一起的：

<figure><img src="../../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 架构概述

我们认为理解架构的最简单方法是描述节点如何与其他节点同步，然后通过指向函数和行号来浏览关键代码。

我们使用以下commit提交哈希值来引用代码：

* **Core:** [135c03a958039423ac2c775cb83eb2a41d903511](https://github.com/deso-protocol/core/tree/135c03a958039423ac2c775cb83eb2a41d903511)
* **Backend:** [96d24569a7b4581644a330be168c441c948d9040](https://github.com/deso-protocol/backend/tree/96d24569a7b4581644a330be168c441c948d9040)
* **Frontend**: [c1363f7fb0239a835b1f2c91d395c8e2d22cbb8f](https://github.com/deso-protocol/frontend/tree/c1363f7fb0239a835b1f2c91d395c8e2d22cbb8f)
* **Identity**: [665281c54b8136a5b8965fb907aac7419ac4c735](https://github.com/deso-protocol/identity/tree/665281c54b8136a5b8965fb907aac7419ac4c735)

### 节点的主循环

* 节点所做的一切的入口是 [`main.go`](https://github.com/deso-protocol/backend/blob/96d2456/main.go#L15)。从后端仓库的 main 开始看代码比从核心仓库的 main 开始更好，因为核心仓库主要用作库依赖使用。此外，由于后端使用核心库作为依赖，我们从后端的main开始也会遍历所有核心功能。\\
  * 在 main 中有很多间接引用，这是因为我们使用 Viper 管理命令行标志。当运行后端二进制文件时，会传递一个命令，例如 "run"，触发 [cmd 包中定义的 Run() 函数](https://github.com/deso-protocol/backend/blob/96d2456/cmd/run.go#L23) \\
  * 所有可用的命令行标志可以在[`init()`](https://github.com/deso-protocol/backend/blob/96d2456/cmd/run.go#L45)查看。一些标志在`Run()`开始时的[`LoadConfig()`](https://github.com/deso-protocol/backend/blob/96d2456/cmd/run.go#L25)中初始化。\\
    * 请注意，核心仓库的标志实际上已导入到后端。这允许最大的可组合性，因此可以包含核心仓库并将其所有功能免费嵌入到二进制文件中。\\
  * 进入[`Run()`](https://github.com/deso-protocol/backend/blob/96d2456/cmd/run.go#L23) 函数后，节点所做的一切都可以明确地追踪。我们将在下面介绍一些关键的代码路径。\\
* 当节点 [启动](https://github.com/deso-protocol/backend/blob/96d2456/cmd/node.go#L31) 时，它会寻找可以从中下载区块和交易的对等节点。节点寻找对等节点的两种主要方法是：\\
  * DNS 引导。所有对等节点扫描形式为 `deso-seed-*.io`的域名，查看是否有有效的对等节点。执行此操作的函数是 [`addSeedAddrsFromPrefixes()`](https://github.com/deso-protocol/core/blob/135c03a/cmd/node.go#L303)，扫描的“前缀”列表在 [`constants.go`](https://github.com/deso-protocol/core/blob/135c03a/lib/constants.go#L479) 中定义。\\
    * 因为购买所有种子的成本是 O($1M)，而且节点只需要一个有效的同步对等节点来抵御“日蚀”攻击，而且节点每秒可以迭代数万个 DNS 记录，而且如果特定前缀被垄断，DNS 种子可以由节点操作员更改，我们认为这是寻找初始对等节点的安全方法。
*   命令行标志。`--connect-ips`表示对等节点将仅连接到指定的对等节点。`--add-ips`表示这些对等节点将被添加到对等节点将尝试连接的事物列表中。



    当我们启动新节点时，我们经常使用 `--connect-ips`和受信任的节点，因为这比从更广泛的互联网运行的节点海中引导更容易。\\
* ConnectionManager 负责管理与对等节点的所有连接。它使用 main.go 中启动的 [`Start()`](https://github.com/deso-protocol/core/blob/135c03a/lib/connection\_manager.go#L769)函数进行初始化。从这个函数开始追踪代码是了解如何建立和维护与对等节点连接的好方法。\\
* 当 ConnectionManager 连接到对等节点时，它会进行类似于比特币的“版本协商”。这发生在 ConnectPeer() 中。如果对等节点通过此版本协商，则通过 "newPeerChan" 将对等节点传递给 `server.go`。然后，`server.go` 负责与对等节点进行更高级别的交互。\\
  *   `server.go` 使用 [`Start()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1720)启动，这是开始追踪它的好地方。可以将 `server.go` 视为节点的“主循环”。



      它基本上是一个 for{} 循环，所有对等节点和服务都在向其添加消息。从[`messageHandler()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1515)查看此“主循环”如何运行。\\
* 从概念上讲，`server.go`  处理两种类型的消息。[控制消息](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1471)和[节点消息](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1489)，都通过 messageHandler进行处理。
  * 节点消息仅包含来自节点连接的某个节点的消息。你可以看到它们并不多，而且相当简单。\\
  * 控制消息基本上是关于节点内部发生的事情的通知。例如，新的节点连接或新的节点断开连接。\\
* 当一个对等节点连接时，`server.go` 从 ConnectionManager 获取一个 NewPeer 或  [`MsgDeSoNewPeer`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1474) 控制消息，并在 [`_handleNewPeer()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L775) 中处理它。这通常是`server.go` 的“起点”。\\
  * 如果对等节点有效，那么`server.go`将在 [`_startSync()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L709)中接受此对等节点作为“同步对等节点”，并向其发送 GetHeaders 或 [`MsgDeSoGetHeaders`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L765) 消息以开始从中同步标题和区块。\\
  * 节点的初始同步目前完全是单线程的。找到同步节点后，其他节点消息在节点下载过去 24 小时的区块之前基本上会被忽略。\\
* 以下是与节点同步的步骤，可以通过跟踪`server.go` 中的函数进行追踪：
  * ConnectionManager 将 `MsgDeSoNewPeer` 消息传递给 server.go，在  [`messageHandler()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1515)中进行处理。\\
  * 在[`_startSync()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L685)中选择一个远程节点作为 syncPeer。将其称为“远程节点”。\\
  * 在 \_startSync() 中向远程对等节点发送 [`MsgDeSoGetHeaders`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L765)。\\
  * 远程节点回复 `MsgDeSoGetHeaders`，发送 [`包含MsgDeSoHeaderBundle` 的 `_handleGetHeaders()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L439)。\\
    * 请注意，类似于比特币的“header locator头定位器”用于确定所需的请求头。
  * 节点在 [`_handleHeaderBundle()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L526) 中处理 `MsgDeSoHeaderBundle`，根据对等的同步情况回应不同的消息。\\
    * 如果需要更多头，它会发送 [ 另一个`MsgDeSoGetHeaders`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L692)。请注意，请求标题直到[ 最新的HeaderBundle is < `MaxHeadersPerMsg`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L604)。这是节点知道它已下载远程节点为其提供的所有标题的方式。\\
*
  * Node processes the `MsgDeSoHeaderBundle` at [`_handleHeaderBundle()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L526) and responds with different messages depending on how synced the peer is.\\
    * If more headers are required, it sends [another `MsgDeSoGetHeaders`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L692). Note that headers are requested until the number of headers in the [latest HeaderBundle is < `MaxHeadersPerMsg`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L604). This is how the node knows that it's downloaded all the headers the remote peer has for it.\\
    * If the node has exhausted the peer's headers then it downloads blocks until it has a block for every corresponding header that the peer sent it. This is exactly the same as the "headers-first" synchronization that Bitcoin does. The `MsgDeSoGetBlocks` message is sent in [`GetBlocks()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L480).\\
    * Processing a block happens in [`ProcessBlock()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1494), which is a great function to trace through. It calls [`ConnectBlock()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1766), which calls ConnectTransaction on each transaction, which we'll discuss later.\\
    * Once the node has all the headers it needs from the peer, and if the node has downloaded and validated all the blocks from this peer, then the node is fully synced.\\
      * Once we get to this state, the node listens to INV messages from all of its peers. If it sees an INV message for a new block that it doesn't have yet, then it will send the peer a `GetHeaders` request, which will kick off this headers-first process for the single missing header/block.\\
* Once the node has gotten through this loop, it is fully synced and in a "steady-state." At this point, the node listens for INV messages from its peer to update its state. `INV` messages or `MsgDeSoInv` are processed via [`messageHandler()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1515) just like everything else.\
  \
  `INV` messages can be for a block, as mentioned previously OR for a transaction. Below is the case for a transaction `INV`:\\
  * Note that some "handle" functions are defined in peer.go rather than server.go. When this is the case, the server.go [`_handlePeerMessages()`](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L1489) function will just enqueue the message for the peer's thread to process it.\
    \
    This is done in order to move processing into another thread for efficiency reasons (not doing this would cause server.go to be \*too\* single-threaded). Here you can see the `_handleInv()` in `server.go` [delegate the call](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1306) to peer.go, and here you see peer.go [dequeuing it to process it](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L562).\
    \
    Note that there are several messages that are delegated in this way, all defined in the [`StartDeSoMessageProcessor()`](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L530) function.\\
  * If the node is missing a transaction that it received an INV for, it sends a GetTransactions or [`MsgDeSoGetTransactions`](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L440) message to the peer.\\
  * This triggers the node's [`_handleGetTransactions()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1309) function in server.go, which results in a `TransactionBundle` or `MsgDeSoTransactionBundle` [being sent back](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L217).\\
  * The node receives the transaction bundle [here](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L218) and processes each transaction in [`_processTransactions()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1334) in `server.go`.\\
    * When a transaction is processed in `server.go`, it is basically just calling [`processTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/mempool.go#L1887) in `mempool.go`.\
      \
      If the transaction is valid then it will be added to the mempool, and if not then it will be rejected. In order to validate a transaction, mempool uses the previously mentioned [`ConnectTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L6043) function defined in `block_view.go`.\\
* Now we understand how a node syncs initial blocks, and how it accepts new blocks and transactions in the steady-state. The next step is to understand how blocks are created and mined:\\
  * `block_producer.go` runs in a continuous loop kicked off via a [`Start()`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_producer.go#L522) function called in main.go. `Start()` calls [`UpdateLatestBlockTemplate()`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_producer.go#L476) at regular intervals to create new blocks for miners to mine. This is a great function to trace.\\
  * Function [`_getBlockTemplate()`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_producer.go#L110) contains the logic for constructing a new block. It basically does the following:
    * Add txns from the mempool to the block until the block is full.
    * Compute the fee, merkle root, etc.\\
  * Newly-created "block template" is added to `recentBlockTemplatesProduced` in [`AddBlockTemplate()`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_producer.go#L376).\\
  * `block_producer.go` just produces block templates, but it's up to miners to compute winning hashes. That happens via a remote process as follows:\\
    * Every node exposes two functions via JSON API: [`GetBlockTemplate()`](https://github.com/deso-protocol/backend/tree/main/routes#L10372) and [`SubmitBlock()`](https://github.com/deso-protocol/backend/tree/main/routes#L10445). The URL paths for these and all other API functions can be seen [here](https://github.com/deso-protocol/backend/tree/main/routes#L9638) and [here](https://github.com/deso-protocol/core/blob/135c03a/lib/api.go#L63) (the latter powers the block explorer).\\
    * Miners run [`remote_miner_main.go`](https://github.com/deso-protocol/core/blob/135c03a/remote\_miner\_main.go) and connect to any node they want via a flag. This can be their own local node or a remote node like node.deso.org. `remote_miner_main.go` will continuously call `GetBlockTemplate()` on the chosen node and hash it until it's found a block.\
      \
      Once it has found a winning hash, it calls `SubmitBlock()`, which then causes the node to process it and broadcast it to the rest of the network.\\
      * Because all nodes expose `get-block-template`, all nodes can be used to mine blocks in this way. Miners generally don't need to do anything other than point to a valid DeSo node somewhere on the network.\\
    * Note that we are currently working on increasing the nonce size to 64 bits up from 32 bits. This will result in ExtraNonce being basically deprecated, and will make `GetBlockTemplate()` much faster because it won't have to copy a block.\\
  * Once a block has been submitted via SubmitBlock, it is then relayed to other peers via the INV mechanism described previously. This happens as follows:
    * [`SubmitBlock()` in `miner.go`](https://github.com/deso-protocol/backend/blob/47bcc8af71b039f857bd949fcea94bfbed8b57e8/routes/miner.go#L125) calls [`ProcessBlock()`](https://github.com/deso-protocol/backend/blob/47bcc8af71b039f857bd949fcea94bfbed8b57e8/routes/miner.go#L177)\\
    * `ProcessBlock()` notifies core's `server.go` that a block was connected by calling [`_handleBlockMainChainConnectedd()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1854)\\
    * `_handleBlockMainChainConnectedd()` updates the mempool using [`UpdateAfterConnectBlock()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1078), which removes transactions from the mempool that have been mined into the block\\
    * `ProcessBlock()` notifies `server.go` again by [enqueing a `MsgDeSoBlockAccepted`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L2135) message at the end, triggering a call to [`_handleBlockAccepted`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1127).
      * This creates an `INV` for the new block that gets relayed to all the peers who will then request it from this node.\\
* There is one more important thread that a node runs at startup, which is the BitcoinManager thread defined in `bitcoin_manager.go`. Like everything else, it has a [`Start()`](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L2105) function that is kicked off in `main.go` via `server.go` (called [here](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1741). It works as follows:\\
  * It looks for a Bitcoin peer and connects to it through [`_getBitcoinPeer()`](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1741).\\
  * It sends the Bitcoin peer a [`GetHeaders`](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1985) and kicks off a single-threaded main loop with its peer [here](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L2001).\\
  * It downloads headers until it is fully synced with the Bitcoin peer.
    * All we really need from a Bitcoin node is its header chain.
    * The headers are used to validate [BitcoinExchange](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2647) transactions when calling `ConnectTransaction()` in either [`ProcessBlock()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1688) or [`processTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/mempool.go#L999). A BitcoinExchange transaction is only valid if it has a merkle proof attached to it that has a valid Bitcoin header hash as its root. More on this later.\\
  * In addition to the header chain, new blocks are downloaded from the Bitcoin node in order to extract valid BitcoinExchange transactions from them. Basically, any transaction that sends Bitcoin to the sink address, [defined here](https://github.com/deso-protocol/core/blob/135c03a/lib/constants.go#L506), is recognized as being able to print DeSo on the Bitcoin chain.\\
    * Blocks are downloaded from the Bitcoin peer whenever a new header is received from the peer [here](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1414) and [here](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1333).\\
    * You can see how the extraction of a BitcoinExchange transaction works [here](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1632).\\
  * Like other services, whenever the BitcoinManager gets some new transactions or headers, it notifies server.go by adding a message that will be processed by `messageHandler`. This happens [here](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1098).\\
  * The BitcoinManager does some other things, like for example it is used to broadcast BitcoinExchange transactions to many peers at once [here](https://github.com/deso-protocol/core/blob/135c03a/lib/bitcoin\_manager.go#L1806). But its main purpose is to download the Bitcoin header chain and, to a lesser extent, to download new blocks and extract valid BitcoinExchange transactions from them.\\
  * Note also that using a single Bitcoin peer may seem insecure, but because the node checks the minimum work is above a certain threshold, it's generally not an issue.\
    \
    Additionally, nodes that run the main protocol code are pointed at specific trustworthy Bitcoin peers using [--bitcoin\_connect\_peer](https://github.com/deso-protocol/core/blob/135c03a/cmd/config.go#L91)

### Seed creation and transaction construction

Below we trace how seeds and transactions are created while giving detail on their format and how validation works.

* First, a user lands on an app like [Diamond](https://diamondapp.com), which is the Angular frontend.\\
  * All the API endpoints for the frontend are defined in a single file called [backend\_api\_service.ts](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/backend-api.service.ts)
    * All the routes are defined [here](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/backend-api.service.ts#L12).\\
  * They all hit corresponding API endpoints defined on the node's JSON API, which is fully defined in [frontend\_server.go](https://github.com/deso-protocol/backend/tree/main/routes).\\
    * All the routes are the same as the ones defined in `backend_api_service.go` and are defined [here](https://github.com/deso-protocol/backend/tree/main/routes#L9435) and configured [here](https://github.com/deso-protocol/backend/tree/main/routes#L9500).\\
    * When a node starts up it opens up three ports: A "web" port that serves the Angular app, a "protocol" port that is used to connect with peers and process all blockchain-related messages, and an "API" port that is used to handle requests from the Angular app.
      * By default these ports are: 4002=Angular app, 17001=JSON API, 17000=protocol port
      * Note that the “web” port is deprecated in favor of running the frontend Angular app as a stand-alone service. So very soon a node will only have a JSON API port and a protocol port.\\
    * Anytime the angular app needs to do something like construct a transaction or download the data for a user, it uses the API port. The JSON API is like "glue" between the blockchain and the frontend.\\
* Creating and storing the seed
  * When a user hits “Sign Up,” they are taken to identity.deso.org.\\
    * On identity.deso.org, the user generates a seed phrase and then [hits next](https://github.com/deso-protocol/identity/blob/665281c/src/app/sign-up/sign-up.component.ts#L69).
      * The seed is stored in the `localStorage` of identity.deso.org using a call to [addUser](https://github.com/deso-protocol/identity/blob/665281c/src/app/sign-up/sign-up.component.ts#L75).\\
    * All of the seed phrases stored in `localStorage` are encrypted using a call to [`getEncryptedUsers()`](https://github.com/deso-protocol/identity/blob/665281c/src/app/sign-up/sign-up.component.ts#L85).\\
      * The [access level](https://github.com/deso-protocol/identity/blob/665281c/src/app/account.service.ts#L32) of the host is determined. For example, [Diamond](https://diamondapp.com) has “FULL” access. Other nodes will have different access depending on what users have explicitly allowed.\\
      * If a host has “FULL” access, then an [encryption key](https://github.com/deso-protocol/identity/blob/665281c/src/app/crypto.service.ts#L70) is computed for that host, to be used in a subsequent step.\
        \
        This encryption key is [stored in localStorage](https://github.com/deso-protocol/identity/blob/665281c/src/app/crypto.service.ts#L44) where possible, but for some browsers like Safari it must be stored in a Cookie, which is less ideal but it works.\\
      * Once an encryption key is generated for the host, it is used to compute an [`encryptedSeedHex`](https://github.com/deso-protocol/identity/blob/665281c/src/app/account.service.ts#L36). Again, this only happens if the node has the FULL access level.\\
    * Then, if the host has the FULL access level, the encrypted users are sent back to the host (in our case it’s diamondapp.com) by a call to [login()](https://github.com/deso-protocol/identity/blob/665281c/src/app/sign-up/sign-up.component.ts#L84), which then does a [window.postMessage](https://github.com/deso-protocol/identity/blob/665281c/src/app/identity.service.ts#L44) back to the host.\\
      * Note: This is tab-to-tab communication. diamondapp.com opens identity.deso.org, identity generates the `encryptedSeedHex`, and then sends it back to diamondapp.com. This same process works if you replace diamondapp.com with the host of your own third-party node.\
        \
        The difference is that your third-party node will need to ask the user for permission in order to get encryptedSeedHex sent back to it.\\
  * Once diamondapp.com has the `encryptedSeedHex`, it uses it to sign things. It does this by calling various operations on an iframe of identity.deso.org embedded within it.\\
    * /frontend gets an unsigned transaction from the JSON API. Here is an example where it gets [an unsigned SubmitPost transaction](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/backend-api.service.ts#L598).\\
    * Then it calls [`signAndSubmitTransaction`](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/backend-api.service.ts#L612), which [sends](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/backend-api.service.ts#L270) it to the identity.deso.org iframe via a [postMessage](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/identity.service.ts#L84) to call [doSign()](https://github.com/deso-protocol/identity/blob/665281c/src/app/embed/embed.component.ts#L47).\\
      * Importantly, in order to have identity sign the transaction, diamondapp.com includes the [`encryptedSeedHex` and the transaction hex](https://github.com/deso-protocol/frontend/blob/96bdf0c/src/app/backend-api.service.ts#L271).\\
    * identity.deso.org then decrypts the `encryptedSeedHex` with the host-specific encryption key, signs the transaction, and returns the signed transaction back. This all happens [here](https://github.com/deso-protocol/identity/blob/665281c/src/app/embed/embed.component.ts#L73).\\
  * Why is this so complicated? Why send `encryptedSeedHex` back to the host? Wouldn’t it be better to just keep everything in identity.deso.org?\\
    * The reason for this setup is that iOS devices does not allow identity.deso.org to access persistent `localStorage` when it’s embedded as an `iframe` in diamondapp.com.\
      \
      This is due to Apple’s crusade against third-party cookies. However, Apple does allow identity.deso.org to access its cookies when its embedded as an iframe on diamondapp.com if those cookies are set as first-party cookies.\\
    * So, what do we do? We push the user to create their seed on identity.deso.org, where we can set an encryption key as a first-party cookie. Then, back on diamondapp.com we store the `encryptedSeedHex`.\
      \
      When signing is needed, the `encryptedSeedHex` is passed to the identity.deso.org iframe, which has access to the encryption key in the cookie, which it then uses to decrypt the `encryptedSeedHex` and sign the transaction.\\
    * One draw-back of this approach is that cookies are sent to the identity.deso.org automatically when the page or iframe loads. This is not ideal, but that information is useless without the actual seed. Moreover, and critically, cookies are only used on iOS devices.\
      \
      On non-iOS devices, the encryption key is stored in `localStorage`. This means that only iOS devices are subject to this drawback.\\
    * One other draw-back is that an XSS attack on diamondapp.com or a third-party node could technically give the attacker access to the `encryptedSeedHex`. However, this information is useless without the encryption key stored exclusively in identity.deso.org.\\
* When a user does any kind of "write" operation in the app, such as submitting a post, liking, or updating their profile, a corresponding endpoint in `frontend_server.go` is called to construct a transaction.\
  \
  That transaction is then returned unsigned, signed by the identity iframe, and then submitted back to core via [`SubmitTransaction()`](https://github.com/deso-protocol/backend/tree/main/routes#L2984).\\
* As an example, consider [`/send-deso`](https://github.com/deso-protocol/backend/blob/47bcc8a/routes/server.go#L45), which is relatively straightforward:\\
  * [First, a universal view is fetched.](https://github.com/deso-protocol/backend/tree/main/routes#L2849) More on this later, but it basically gives the endpoint a "union" of the "state" between what's in the mempool and what's in the blocks. For example, if someone sent you DeSo in a txn that's in the mempool, you can use the view to find that UTXO. And if they sent it to you in a txn that's been mined into a block, you can also find it in that view.\\
  * In order to create the spend transaction, the endpoint needs to find UTXO's for the user.\
    \
    This generally always happens in [`AddInputsAndChangeToTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L3033), which is a good function to trace through. We're not aware of any transaction assembly that does not utilize this function for UTXO fetching.\\
    * The key function is [`GetSpendableUtxosForPublicKey()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L2268), which generates a universal view that includes txns from the mempool and then returns all UTXO's that are associated with the particular public key. These UTXO's can then be assembled into a transaction.\\
    * Again, basically all transaction assembly runs through this codepath.\\
  * Then the transaction is sent back to the frontend and signed.\\
  * [`SubmitTransaction()`](https://github.com/deso-protocol/backend/tree/main/routes#L2983) is called.\\
  * The transaction is then validated and broadcasted in [`VerifyAndBroadcastTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L226).\\
    * It does [some pre-validation](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L2155) of the transaction by calling [`ConnectTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L6043) on it.\\
    * If the validation passes then it calls [`BroadcastTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L209), which calls [`_addNewTxn()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1023) in server.go, which adds the transaction to the mempool calling [`ProcessTransaction()`](https://github.com/deso-protocol/core/blob/135c03a/lib/mempool.go#L1943).\\
    * Once the transaction is in the mempool, the node will eventually relay the transaction to its peers via a separate thread running in server.go that's kicked off in `Start()` through [`_startTransactionRelayer()`](https://github.com/deso-protocol/core/blob/135c03a/lib/server.go#L1669).\\
      * This thread is basically looking at the mempool at regular intervals and sending transactions to peers that they don't already have. This is how a transaction that's generated in the UI makes it to the rest of the network.\\
  * Once a transaction has gone into the mempool then we're done. It will eventually be mined into a block.\\
* A note on the [`/burn-bitcoin`](https://github.com/deso-protocol/backend/blob/47bcc8a/routes/server.go#L44) endpoint:\\
  * This endpoint is called when a user buys DeSo using Bitcoin in the "Buy DeSo" tab. It does the following:\\
    * Constructs a Bitcoin transaction sending the user's Bitcoin to the "sink" address\\
    * Broadcasts it to the Bitcoin blockchain\\
    * Waits some amount of time for the transaction to propagate\\
    * Checks to see if a double-spend occurred during this interval.\\
    * If no double-spend was detected, the transaction is added to the DeSo mempool with the expectation that it will eventually mine into a Bitcoin block (and subsequently a DeSo block).\\
      * The fee is generally set to 2x the "fastest" fee to ensure very high probability that the txn is processed.\
        \
        This is currently set in the frontend, but there is no reason why it can't be re-enforced in either the frontend\_server.go code or in the mempool itself prior to accepting the Bitcoin txn.\\
    * Once this transaction has been accepted into the mempool, the user can immediately spend it.\\
      * This means there will be some risk of reversion of the user's transactions if the transaction isn't ultimately confirmed by the Bitcoin blockchain.\
        \
        But we have yet to have someone successfully double-spend against the latest iteration of the double-spend checking logic.\\
    * BitcoinExchange transactions can also be added to the mempool via relay from other peers.\
      \
      In this case, the node can be set to [ignore unmined Bitcoin transactions from peers](https://github.com/deso-protocol/core/blob/135c03a/lib/peer.go#L224) so there is minimal risk of a double-spend or reversion.\\
  * Importantly, no matter what the mempool does, the DeSo blockchain will not allow a BitcoinExchange transaction into it without at least one block of work on it.\
    \
    In practice, three blocks of work are required because miners wait for three blocks in order to be safe. This happens via a param called [`MinerBitcoinMinBurnWorkBlocks`](https://github.com/deso-protocol/core/blob/135c03a/lib/constants.go#L504) that is utilized by the block producer.

### Transaction format

* Generally, all important "messages" that need to get sent between peers, most notably `MsgTxn` and `MsgDeSoBlock`, are defined in [`network.go`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go). They all implement the very simple [`DeSoMessage`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L208) interface.\\
* All of these messages have serialization functions called ToBytes() that are defined by us in order to guarantee that all nodes serialize to the exact same bytes. If we were to rely on protobufs of JSON, nodes could get different serialized byte strings for the same messages because these formats do not guarantee consistent serialization across machines.\\
* Transactions are based on UTXO's. They contain the following:\\
  * [`TxInputs`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2200), which is effectively a list of \<PreviousTxID, index> pairs called [`UtxoKey`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2148) where the index refers to the output being spent.\\
  * [`TxOuptuts`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2201), which just specify what amounts are going to which public keys.\\
  * `TxnMeta`. More on this later\\
  * [`PublicKey`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2215). In DeSo transactions are very simple and only have one public key that can be deemed to be the "executor" of the transaction. The transaction is generally always signed by this public key.\\
  * [`ExtraData`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2221). This is a flexible map that arbitrary data can be added to. It is currently used to support Reposts via [`RecloutedPostHash`](https://github.com/deso-protocol/core/blob/135c03a/lib/constants.go#L944) and [`IsQuoteReclouted`](https://github.com/deso-protocol/core/blob/135c03a/lib/constants.go#L946) params.\
    \
    It can be used to augment a transaction without causing a hard fork, which significantly increases the extensibility of DeSo by the community. For example, one can trivially add a "pinned posts" feature using `ExtraData` without consulting the core DeSo devs about it.\\
* Note that the map keys of `ExtraData` are [always sorted](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2158) when serialized so that consistent serialization across machines is preserved even though we're using a map.\\
* Transaction metadata is used to determine what type of transaction we're dealing with. For each type of transaction in the system, a metadata type is defined that implements the [`DeSoTxnMetadata`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L299) interface.\
  \
  The full list of transaction types can be viewed [here](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L239). To see descriptions of each one, simply find where that transaction type implements the interface.\\
  * For example, here is the [`BitcoinExchangeMetadata`](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L2647). You can see it contains a full Bitcoin transaction plus a merkle proof into the Bitcoin blockchain. This is how a node verifies that a particular Bitcoin transaction has a sufficient amount of work on it.

### Transaction validation

* Virtually all transaction validation happens in [`_connectTransaction`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L5022) in `block_view.go`.
* Validation works by applying the transaction to a "view," which is basically a "simulation" of what would happen if the transaction were written to the database, but that doesn’t actually modify the database.\
  \
  This is useful because a view can allow you to "simulate" what would happen if you applied a bunch of transactions to the database in sequence in order to validate whole blocks before ever actually writing anything to the database. And this is exactly what [`ConnectBlock`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L5120) does.\\
  * A view is basically a "copy on write" system. When a transaction requires something to be written to the database, an in-memory entry is created representing that entry.\
    \
    This generally happens in calls to \_set.\*mappings and \_get.\*, such as [`_setProfileEntryMappings`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L2873) and [`_getProfileEntryForUsername`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L2757).\\
* If all of the transactions that have been applied to a view appear to be valid, the view can be "flushed" to the database, which writes all of the updates those transactions produced to the database. The flush code for the view is [here](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L6503), and it delegates to individual flush functions [here](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L6466).\\
  * In most cases, flushing to the db just requires first [deleting entries that have i`sDeleted=true`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L6347) and then [writing entries that have isDeleted=false](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L6384).\\
  * The core ProcessBlock function basically just applies all the txns in a block to a view via [`ConnectBlock()`](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1688) and then [flushes it](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1725). The mempool uses a view to validate transactions as well [inside of its core `processTransaction` function](https://github.com/deso-protocol/core/blob/135c03a/lib/mempool.go#L1402).\\
* We can walk through connecting an UpdateProfile transaction to see how it works.\\
  * `_connectTransaction` delegates to [`_connectUpdateProfile`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L5069)\`\`\\
  * [A bunch of validation happens](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4069)\\
  * UTXO's are generally always checked by a call to [`_connectBasicTransfer`](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4129), which returns the total input and output of the transaction.\\
  * An existing profile entry is [looked up](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4108) if one exists. If it exists, it is updated. Otherwise, a new one is created from scratch.
    * Updating an existing profile happens [here](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4154) while creating a new one happens [here](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4195).\\
  * In both cases, mappings for the profile are first [deleted from the view](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4242) and then [set on the view](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4246).\\
    * Note that deleting something from the view never actually deletes a mapping, it only marks it as `isDeleted=true`.\
      \
      This is because the flush needs to propagate this change to the db, and it can only do that if it knows the entry is scheduled to be deleted by leaving it in the view.\\
  * Finally, [some information is saved](https://github.com/deso-protocol/core/blob/135c03a/lib/block\_view.go#L4249) that allows us to roll back or "disconnect" the transaction in the future if needed.\\
* Every transaction has both a `_connect` and a `_disconnect`.\
  \
  The `_disconnect` restores the view to the state it was in before the transaction was connected. `_disconnect` code is rarely used, but it supports reorgs of blocks, which happen from time to time.\\
  * During a reorg, we need to disconnect some transactions from some blocks and connect transactions from some other blocks in order to validate the fork, \*before\* writing anything to the db.\
    \
    This happens in [ProcessBlock here](https://github.com/deso-protocol/core/blob/135c03a/lib/blockchain.go#L1786).
