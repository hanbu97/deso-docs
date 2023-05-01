# 3⃣ 节点：保持最新

这是一份关于如何与核心代码仓库的硬分叉保持同步的手把手指南。此外，还提供了关于核心团队成员如何执行可能导致网络硬分叉的更新的指南。

本文档旨在帮助节点运营商和核心团队以有组织的方式发布新代码，特别是让开发者社区有足够的时间和资源升级他们的节点。

## 节点运营商的操作说明 <a href="#_cv2t10tt14ya" id="_cv2t10tt14ya"></a>

1. 在 GitHub 上订阅新版本发布通知
   * 需要关注哪些代码仓库？
     * [https://github.com/deso-protocol/core](https://github.com/deso-protocol/core) (重要)
     * [https://github.com/deso-protocol/backend](https://github.com/deso-protocol/backend) (重要)
     * [https://github.com/deso-protocol/rosetta-deso](https://github.com/deso-protocol/rosetta-deso) (对Coinbase等交易所重要))
     * [https://github.com/deso-protocol/identity](https://github.com/deso-protocol/identity)
     * [https://github.com/deso-protocol/frontend](https://github.com/deso-protocol/frontend)
   * 如何订阅？
     1. 点击页面顶部的 “Watch”
     2. 选择 “Custom”
     3. 勾选 “Releases”\\
2. 当主要版本更新发生时，例如从2.9.9升级到3.0.0，请务必阅读发布说明，并在下周内重启您的节点以避免问题。
   * 请注意，偶尔需要进行重新同步，除非与第二个运行节点并行执行，否则可能会导致大约20分钟的停机时间。请务必阅读发布说明以避免任何中断。\\
3. 关注  [@deso](https://diamondapp.com/u/deso) 和 [@nader](https://diamondapp.com/u/nader)r 在 [node.deso.org](https://node.deso.org/) 或 [DiamondApp](https://diamondapp.com/) 上的链上公告和讨论。\\
4. 在Twitter上关注 [@desoprotocol](https://twitter.com/desoprotocol) 和 [@nadertheory](https://twitter.com/nadertheory)&#x20;

## 核心团队的操作说明 <a href="#_9wdvp7tesgvk" id="_9wdvp7tesgvk"></a>

### 代码准备 <a href="#_2tidf3tg7ql4" id="_2tidf3tg7ql4"></a>

1. 通过 ForkHeight 对所有分叉更改进行限制，初始值 mainnet 和 testnet 应为 `math.MaxUint32`，以及 regtest 应为 0。\\
2. 确保代码通过所有基本测试：
   1. 核心单元测试
   2. 核心集成测试
   3. 使用 hypersync + txindex 在 testnet 和 mainnet 上同步节点
   4. 使用 blocksync + txindex 在 testnet 和 mainnet 上同步节点
   5. rosetta 可以同步 mainnet
   6. 对于postgres，重复步骤c. 和 d.
   7. 测试所有更改在参考前端中的工作情况
   8. 确保在部署复杂更改时运行更全面的测试\\
3. 假设您已准备好下一节中所有步骤的内容，您应在太平洋时间12:00 PM宣布所有内容，并将 testnet 的分叉高度设置为从公告发布后的第一天，mainnet 的分叉高度应当**至少为**从公告发布起的第七天。       &#x20;
   * 我们将尽量为节点运营商提供尽可能多的升级时间，以两周为限，避免出错。\
     \
     然而，为了快速推进升级，有时需要在较短的时间内进行更新，但通常至少提前一周发出警告。

### 发布准备 <a href="#_nkce44tidl7s" id="_nkce44tidl7s"></a>

1. 撰写关于分叉中发生的情况的描述。根据接下来的步骤，使其成为几句话或几段文字。 \\
2. 草拟一个分叉准备清单文档，该文档是关于节点运营商需要执行哪些操作来升级节点的逐步检查表/操作指南( [受到Prism的合并准备检查表启发](https://docs.prylabs.network/docs/prepare-for-merge))\。
3. (可选)编写一个分叉前后对照表，描述分叉中发生的情况。左列应描述“之前”技术的不同方面，右列显示它们在“分叉后”如何更新/更改( [受到Prism的合并准备检查表启发](https://docs.prylabs.network/docs/prepare-for-merge))。\\
4. 将上述所有内容放在GitHub上的一个新draft草稿发布中，类似于[ConsenSys在teku中所做的方式](https://github.com/ConsenSys/teku/releases)，或者[ethereum所做的](https://github.com/ethereum/go-ethereum/releases) 那样。\\
5. 假设发布编号为XX.YY.ZZ（主要.次要.修订）:\\
   * 您应该增加主要部分 XX
     * \-> 如果节点运营商需要在固定时间内升级他们的软件，否则冒着陷入过时分叉的风险\\
   * 您应该增加次要部分 YY
     * \->如果节点运营商可以不做任何事情仍然正确地参与网络。 代码以向后兼容的方式增强节点。\\
   * 您应该增加修订部分 ZZ
     * \-> 如果更新以向后兼容的方式修复Bug错误。\\
6. 发布新版本。

### 公告 <a href="#_persa8n1a2dg" id="_persa8n1a2dg"></a>

1. 为分叉创建Twitter公告
2. 为分叉创建Diamond公告
3. 在Discord上创建分叉公告
