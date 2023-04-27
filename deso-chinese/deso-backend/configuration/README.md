---
description: Overview of the configuration flags for running your own backend
---

# 1⃣ 后端：配置

\*\*核心协议:\*\*想在 DeSo 区块链上构建下一个伟大的社交应用，还是想运行一个节点并了解所有接口的细节？

这一部分就是为你准备的。

相较于私有的、垄断的 Web2 社交媒体，DeSo 区块链上的所有数据都是公开的，且存储在链上。

这意味着**任何人都可以访问这些信息，并使用这些数据构建自己的应用**。

然而，区块链需要交易和密码学来验证信息。

我们知道这可能会带来麻烦，因此我们为你（开发者）提供了[身份：概述](../../deso-identity/identity/)这个 API 和服务，让你专注于最重要的事情：构建应用程序。

在开始构建并在链上读写新数据时，无需定义和维护自己的数据库，也无需编写从链中提取数据的逻辑。

要将数据写入区块链，你只需要后端 API，用[construct-transactions](../construct-transactions/ "mention")来构建交易，然后使用[#submit-a-transaction](../transaction-utilities.md#submit-a-transaction "mention") 接口提交它们，还需要 Identity，用[#sign](../../deso-identity/iframe-api/endpoints.md#sign "mention")来对交易进行签名。

要读取数据，你可以使用我们的[api](../api/ "mention") 接口来检索所需的一切信息。

作为开发者，你需要做的就是实现前端（或修改参考实现）以及围绕从后端接口接收的数据的实现任何自定义逻辑。 如果你正在运行自己的节点并使用参考后端实现，那么有许多可用的标志供你管理节点上的行为和功能。

以下的每个张杰都描述了与某些功能相关的一组参数配置。
