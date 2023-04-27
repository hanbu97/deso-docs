---
description: 高度可扩展裸机架构上的社交特性
---

# 1⃣ 裸机

从架构角度来看，理解DeSo的一个好方法是将其想象成一个比特币节点，只是演化为能够处理比仅发送/接收货币更广泛的交易类型，具有大量定制的存储和索引逻辑，以支持大规模的社交特性。

### 代码解析

对于对底层细节感兴趣的开发人员，[在本地设置节点和前端](../deso-repos/architecture-overview/dev-setup.md) 这是一个很好的起点，而[代码漫步](../deso-repos/architecture-overview/)是充分理解所有内容如何组合在一起的最佳方式。它看起来可能很紧凑，但是用简单的语言书写，完全理解它不应该花费超过一两个小时。

对于非开发人员而言，要了解DeSo的架构及其优势，最好的方法是继续阅读下一部分，其中用高层次的术语来解释相关概念。

### 大规模存储与索引

尽管传统的区块链（如以太坊）在创建开放金融生态系统方面表现出色，但它们并未设计成能够扩展，以处理竞争性社交媒体应用的存储和索引需求。

例如，DeFi应用通常需要在原值处更新余额，而不是创建一个新的“状态”。而在社交平台上，每篇帖子都会创建一个需要以特定方式存储和索引的新状态。

诸如此类的许多问题使得社交媒体成为一个特殊的用例，我们认为需要将其拆分，并为其提供专用的架构，以便提供妥善的服务。

DeSo的最大优势在于它并非通用区块链。

相反，它支持一系列狭义的社交导向特性，并在裸机上实现，使用自定义索引，每个节点在与同伴同步时达成共识。

相比之下，通用区块链必须通过虚拟机运行所有功能，而这通常比在裸机上运行慢几个数量级。即使如此，它们在同步时也无法构建用于查询的自定义索引。

举一个非常简单的例子，考虑一个更新用户名的社交交易。

节点需要在允许此交易进行之前检查用户名是否已被其他用户占用。

_简单吧？_然而，当你拥有一百万用户时，即使在今天最先进的通用区块链上，这种查找也变得极为昂贵。

相比之下，由于DeSo可以通过裸机访问来支持这种查找，因此它可以以与中心化社交应用一样快的速度，廉价且高效地创建一个简单的键值索引，而且随着用户基数的增长，甚至可以在多个磁盘或节点上进行分片。

### 裸机的优势

随着使用的增加和更多用例的考虑，裸机运行的优势只会增加。



例如，在允许某人回复之前检查父帖子是否存在，或者在允许某人出价之前检查NFT是否出售（请注意，以太坊对链上竞拍的支持不足导致了NFT市场集中于同步平台和中心化现象）。

再举一个简单的例子，考虑展示用户最近帖子的简单列表。

因为通用区块链通常不支持有序列表，所以如果不构建离链索引，这甚至是不可能实现的。

相比之下，DeSo天然支持诸如按时间戳排序的帖子、按代币价值排序的个人资料、按关联NFT组织的NFT竞拍等索引，而且这些索引随着用户基数的增长可以扩展。

这大大降低了运行节点的复杂性，从而可以显著提高生态系统的去中心化程度，以及可以在DeSo之上构建的应用数量。

增加。例如，在允许某人回复之前检查父帖子是否存在，或者在允许某人出价之前检查NFT是否出售（请注意，以太坊对链上竞拍的支持不足导致了NFT市场的显著集中化和中心化现象）。

再举一个简单的例子，考虑展示用户最近帖子的简单列表。因为通用区块链通常不支持有序列表，所以如果不构建离链索引，这甚至是不可能实现的。

相比之下，DeSo天然支持诸如按时间戳排序的帖子、按代币价值排序的个人资料、按关联NFT组织的NFT竞拍等索引，而且这些索引随着用户基数的增长可以扩展。

这大大降低了运行节点的复杂性，从而可以显著提高生态系统的去中心化程度，以及可以在DeSo之上构建的应用数量。

最后一个例子，DeSo节点的内存池甚至是从零开始编写的，以支持社交数据查询，而无需让用户等待区块确认。

这个看似微不足道的优化对于DeSo应用具备“即时”感至关重要，我们相信，如果没有它，DeSo将无法与传统的中心化社交应用竞争。