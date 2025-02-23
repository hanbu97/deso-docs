---
description: 将去中心化社交网络通过创新的 PoS 路线进行扩展
---

# 2⃣ 共识 (PoW & PoS)

## 2023年3月宣布的革命性PoS

DeSo[最近宣布](https://diamondapp.com/u/nader/blog/the-next-phase-of-deso-revolution-proof-of-stake-bitclout-20?feedTab=Hot)将于2023年转向权益证明机制，并首次公开了详细提案。

在此处阅读突破性的**Revolution Proof of Stake**白皮书：\
\
[https://revolution.deso.com/](https://revolution.deso.com/) (密码 = _decentralization_)

## 从 PoW 到 PoS

如今，DeSo运行在混合的工作量证明共识机制之上，使其能够比比特币或以太坊使用更少的能源，同时仍然保持安全免受51%攻击。

可以通过bitpool.me等网站估算DeSo网络当前的功耗，并且我们认为它显著低于500千瓦。

尽管如此，DeSo背后的核心开发团队仍然在探索更合适的共识机制，已经投入了大量资源来开发一项具有突破性意义的权益证明提案，预计在未来几周内我们将揭示该提案，并在2023年底之前推出。

重要的是，与DeSo其他所有内容一样，这个提议将特别适合社交应用，解决社交应用程序所面临的独特问题。

## 一个简化的扩容路线

目前有许多不同且适合的方式可以对DeSo架构进行扩展，以支持10亿用户。

从根本上说，只要功能集保持足够狭窄，并专注于尽可能地在裸机运行一切，那么大多数像Instagram这样的中心化社交平台所使用的扩容方案和经验教训都可以直接应用于DeSo的扩容。

鉴于上述情况，我们认为提供一个经过计算的扩容路线图是有价值的，以便区块链爱好者可以对实现十亿用户的具体目标感到安心。

以下是一个具体的扩展路线图，包括四个相对简单的阶段：

* **阶段1**：权益证明（Proof of Stake）
* **阶段2**：更大的区块
* **阶段3**：Warp同步
* **阶段4**：分片

以下是DeSo在每个阶段的扩容量的数学计算：

1. **权益证明（Proof of Stake）**
   * 虽然PoS并不是DeSo扩容的必要条件，但它仍然是一个头等大事，因此，它将在其他扩展步骤之前或与之同时进行。\\
2. **更大的区块**
   * DeSo区块链的平均帖子大小为**218字节**。\\
   *   除了POST之外，还有10种其他[交易类型](https://github.com/deso-protocol/core/blob/135c03a/lib/network.go#L239)，例如LIKE和FOLLOW。在最近的一个区块中，帖子大约占区块总大小的**1/3**。\\

       <img src="https://lh4.googleusercontent.com/YSLyEVtV0Ynx--mta7IP3QS5aVrZiq7MBVmIc9h9bZwbCrLXXTIIDzO2Gm9RYOjaqQONhOju-F7RvaTIVO6vWJ5AMASXIYHMI4z9sjK3acpoXOmhRHX99-35qS4I54KBl2C3zjnH" alt="" data-size="original">
   * DeSo区块链目前每5分钟产生高达2MB的区块。\\
   * 因此，它可以实现每秒约30笔交易，即**每秒约10篇帖子**（= 2e6字节/区块 /（218字节/帖子 \* 60秒/分钟 \* 5分钟/区块）\* 1帖子 / 3笔交易）\\
   * 如果我们将区块大小增加到每五分钟16MB，我们可以粗略地推算出它可以扩展到每秒约240笔交易=**每秒约80篇帖子**。
   * 作为对比，Twitter平均每秒约有 [6000 篇](https://www.dsayce.com/social-media/tweets-day/)帖子，拥有3亿用户\\
   * 因此，在每秒80篇帖子的情况下，我们应该能够大致容纳80/6000 = 1.33%的3亿用户，即**400万用户**。\\
   * 这就是我们仅通过增加基本区块大小就可以达到的地方。但我们还有其他一些牌可以打。\\
3. **Warp 同步**
   * 通过类似以太坊的[warp 或 snap sync](https://blog.ethereum.org/2021/03/03/geth-v1-10-0/)同步，我们放宽了一个关键约束，即所有节点始终需要验证交易的完整历史记录。（您仍然可以运行归档节点，但对于正常操作来说这不是必需的。）
     * &#x20;举一个具体的例子，如果您只下载每个用户当前的创建者代币余额，那么所有该用户的交易实际上都压缩成了几个整数，因为您不关心历史（只关心最终状态）。
   * 与现在相比，我们转向一个模型，节点默认先同步并验证当前区块链状态的快照，然后在此基础上仅同步几周的区块。\\
   * 通常，区块链性能的瓶颈是验证速度。对于DeSo，我们已经进行了测试，表明在英特尔至强E-2276M上运行的节点可以以**每秒约12MB = 每天1.04TB = 每秒约55,000笔交易**的速度验证交易。\\
   * 那么，如果我们只是从最小验证的状态开始下载，它有多大？以10gbps下载10TB = 10e12/10e9_8/60/60大约需要**2.2小时**。以10gbps下载100TB = 10e12/10e9_8/60/60需要**大约22.2小时**来下载状态。\\
   * 借助 warp 同步，区块大小可以超过16MB，因为将节点更新所需的区块数量可以减少到仅一周的区块，而不是从一开始的所有区块历史。\\
   * 假设使用 warp 同步，我们将区块大小进一步增加到每5分钟120MB的区块。每5分钟120MB的区块能够实现多少交易/秒（TPS）？如果我们假设每笔交易218字节（这是每篇帖子的值），那么（120e6字节/（5分钟\* 60秒/分钟））/（218字节/交易）=**约1800笔交易/秒**。\\
   * 同步一周每5分钟出现一个120MB区块的情况下，所需的带宽是多少，需要多长时间？\\
     * 带宽大致为（120e6字节/区块\* 1区块/5分钟\* 60分钟/小时\* 24小时/天\* 7天/周）/ 1e9字节/GB = 每周238GB \\
     * 在之前提到的英特尔至强E-2276M上12MB/s的验证速度下，需要大约238e9字节/周/（12e6已验证字节/秒\* 60秒/分钟\* 60分钟/小时）= 5.5-6小时。因此在良好硬件上以12MB/s的验证速度**下载并验证一周120MB的区块需要5.5-6小时**。\\
   * 在这些假设下，这意味着 warp 升级可以使我们在保持长期每秒1,800笔交易（TPS）的同时，将节点同步时间缩短至约5.5小时。.\\
     * 1,811 TPS vs Twitter 的每秒6,000帖子和3亿用户（假设仅限帖子，不包括点赞）\\
     * 用户 = 300M \* 1,811 / 6,000 / 每帖3笔交易 = **约3,000万用户**。\\
4. **分片**
   * 此外，所有交易都可以进行写分片，使节点同步并行化，从而提供多个数量级的速度提升。\
     \
     例如，我们可以进行一个非常简单的优化，将所有帖子分片到帖子子链中，然后将其他交易分片到其他两个子链中。\\
   * 这将使节点能够以3倍速度同步，这意味着我们可以在不增加同步时间的情况下支持**约9,000万用户**。\\
   * 最终，所有交易都可以根据用户ID分片到子链中，从而实现几乎无限的并行化。\
     \
     例如，通过使用30个分片，我们在不增加同步时间的情况下实现了TPS的另一个约10倍的乘数，从而实现**约10亿用户**。.\\
   * 通过增加分片数量，这个数字可以进一步扩大。
