# 1⃣ 关联

社交网络可以被视为一个无向图，用户和帖子充当节点，他们之间的互动充当连接它们的边。

在DeSo区块链上，“_关联_”是一种交易类型，用于实现这些边以帮助连接此图形。

关联有两种类型：

1. **用户关联**
2. **帖子关联**

总共，我们在DeSo区块链上引入了**4种不同的交易类型**以支持关联：

* _CREATE\_USER\_ASSOCIATION(_创建用户关联_)_
* _DELETE\_USER\_ASSOCIATION(_删除用户关联_)_
* _CREATE\_POST\_ASSOCIATION(_创建帖子关联_)_
* _DELETE\_POST\_ASSOCIATION(_删除帖子关联_)_

简单地说，1) 用户关联将一个用户（交易者）连接到另一个用户（目标用户），2) 帖子关联将一个用户（交易者）连接到一个帖子（目标帖子）。

除了交易者和目标用户或帖子外，关联还有三个可自定义字段：

1. The association type(关联类型)
2. The association value(关联值)
3. The application scope(应用程序范围)

关联类型允许关联成为多态数据结构，表示用户与其他用户或用户与帖子之间的多种不同关系。

应用程序范围允许在单个应用程序范围内或在DeSo区块链上的所有应用程序范围内指定关联。

### 用户示例

通过一些示例用例来解释关联是最容易的。

让我们从**用户关联**开始。

用户关联可用于为其他用户背书，类似于LinkedIn上提供的技能支持功能。

假设**用户A**想在Diamond上为**用户B**认证掌握“JavaScript”编程语言。

**用户A**可以提交以下关联交易：

![](https://images.deso.org/398599c540928a7983b7107939dcbf1e81c07a0a48348da47cd348f63ea9ff41.webp)

这种关联是**用户A**对**用户B**掌握“JavaScript”知识的官方链上支持。

这是无法伪造的，因为与DeSo区块链上的所有交易一样，**用户A**必须使用他的私钥签署有效载荷才能被接受。

这允许在DeSo区块链之上构建的应用程序使用DeSo API查询**用户A**支持的所有用户或认证**用户B**掌握JavaScript的所有用户。

我们现在启用的其他用户关联用例包括举报用户、标记用户、屏蔽用户、用户统计和记分板以及可证明的/可控制的团体成员。稍后我们将进一步讨论这个问题。

### 帖子示例

让我们考虑一个**帖子关联**的例子。

目前，DeSo 区块链允许用户提交 LIKE 交易，对帖子表示“点赞”。然而，关联功能将允许他们对帖子进行任何反应。

例如，假设**用户A** 想要在全球范围内为**用户B** 的帖子点亮爱心或者用大拇指、点赞、踩、笑脸、哭脸或其他emoji表情作为反应。

**用户A**可以提交以下关联交易：

![](https://images.deso.org/0cbd84466205c7f53a44fb9af221fb66095e598e7b83da8539638048c4a98eec.webp)

基于 DeSo 区块链构建的应用程序可以查询**用户B** 帖子的反应数量，点亮爱心的所有用户等。

我们还考虑到了其他现在可以实现的帖子关联用例，包括标记帖子、举报帖子以及将帖子标记为 NSFW 等。

可以想象现在可以在 DeSo 区块链上利用用户关联构建 Reddit 克隆应用程序，以便指定管理员并通过帖子关联为这些管理员标记帖子的子版块。

最棒的是，所有其他 DeSo 功能都是免费的，如单点登录、打赏、NFT、DeSo 代币、DeSoDollar 稳定币等。

### 关联功能具有无限的用途

请注意，**关联类型**和**关联值**属性可以是任何用户定义的字符串。因此，除了上面描述的例子外，关联还有无限的可能性。

DeSo API 还允许进行高级查询，以对链上关联进行切片和切块，为各种应用提供支持。

通过在 DeSo 区块链上添加一个新的交易类型，使得在链上存储与用户和帖子相关的通用键值数据之间的关联，创造了无限的新可能性，而且 DeSo 的交易费用非常低。

让我们来看看关联功能的更多具体应用：

#### 回应

目前，用户可以对帖子进行点赞，但关联功能现在可以让应用程序允许对帖子进行任意回应，如大拇指、反对、开心脸、心形表情、笑脸、哭脸等。

* **Association Class:** _User(用户)_
* **Transactor:** _发起回应的用户_
* **Target Post:** _被回应的帖子_
* **Association Type:** _REACTION（回应）_
* **Association Value**: _THUMBS\_UP（_大拇指_） | THUMBS\_DOWN（_反对_） | HEART（_爱心_） | LAUGH（_大笑_） | CRY （_哭泣_）…_
* **App**: _可以选择将帖子反应回应在一个应用程序内_

#### 版主

关联可以用于设置应用程序的版主。更一般地说，它们可以用于构建整个链上应用程序的角色权限系统。

* **Association Class**: _User(用户)_
* **Transactor**: _应用程序的公钥_
* **Target User**: _被授予权限的用户_
* **Association Type**: _ACCESS\_ROLE（_访问角色_）_
* **Association Value**: _ADMIN（_管理员_） | LEVEL\_1（_等级1_） | LEVEL\_2（_等级2_） | LEVEL\_3（_等级3_） …_
* **App**: _应用程序的公钥_

#### 标记帖子

关联可以用于任意标记帖子。然后，这些标签可以用来排序和管理内容。

* **Association Class**: _Post（帖子）_
* **Transactor**: _进行标记的用户_
* **Target Post**: _被标记的帖子_
* **Association Type**: _TAG（标签）_
* **Association Value**: _NSFW（色情） | Sports（体育） | Business（商业） | Technology（科技） …_
* **App**: _可以选择将帖子标签限制在一个应用程序内_

**子版块**

结合上述版主和标签用例，可以实现链上的 Reddit 克隆版。用户可以提交帖子并将其标记为属于特定子版块，例如 r/bitcoin。

应用程序可以分配版主，他们可以执行一些管理功能，如拉黑帖子或用户。用户还可以在每个子版块中对帖子进行点赞或点踩。

#### 举报、标记、屏蔽用户

关联允许用户举报、标记或屏蔽其他用户。然后，应用程序可以使用此信息，例如，如果有足够多的单独用户举报，就可以将用户从应用程序中屏蔽。

* **Association Class**: _User（用户）_
* **Transactor**: _进行举报、标记、屏蔽的用户_
* **Target User**: _被举报、标记、屏蔽的用户_
* **Association Type**: _REPORT（举报） | FLAG（标记）| BLOCK（屏蔽）_
* **Association Value**_: 可以选择性地提供举报、标记或屏蔽的理由_
* **App**: ：_可以选择性地将操作限制在单个应用内_

#### 举报、标记、隐藏帖子

关联允许用户举报、标记或隐藏帖子。然后，应用程序可以使用此信息，例如，隐藏帖子。

* **Association Class**: _Post（_帖子_）_
* **Transactor**: _进行举报、标记或隐藏的用户_
* **Target Post**: _被举报、标记或隐藏的帖子_
* **Association Type**: _REPORT（举报） | FLAG （标记）| HIDE（隐藏）_
* **Association Value**: _可以选择性地提供举报、标记或隐藏的理由_

#### 类似Facebook的好友请求

关联使应用程序能够让用户互相发送好友请求。然后，应用程序可以查询用户是否为好友，并限制内容的查看范围，例如仅限好友或密友等。

#### **1.** 好友请求

* **Association Class**: _User（用户）_
* **Transactor**: _请求建立好友关系的用户_
* **Target User**:_被请求建立好友关系的用户_
* **Association Type**: _FRIEND\_REQUEST（好友请求）_
* **Association Value**: _REQUEST（请求）_
* **App**: _可以选择性地将好友请求限制在单个应用内_

#### **2.** 好友请求批准

* **Association Class**: _User（用户）_
* **Transactor**: _批准现有好友请求的用户_
* **Target User**: _最初发起好友请求的用户_
* **Association Type**: _FRIEND\_REQUEST（好友请求）_
* **Association Value**: APPROVE_（批准）_
* **App**: _可以选择性地将好友请求批准限制在单个应用内_

#### 类似Instagram的关注请求

按照上述类似Facebook的好友请求的请求→批准模式，关联允许用户提交和批准关注请求。

应用程序可以将内容限制为仅供关注者查看。

关联看起来与上面的完全相同，只是关联类型将是FOLLOW\_REQUEST关注请求而不是FRIEND\_REQUEST好友请求。

#### 类似Facebook的群组

关联允许用户创建群组，然后限制谁可以加入该群组。用户甚至可以提交请求，以获得准入。

* **Association Class**: _User（用户）_
* **Transactor**: _群组所有者_
* **Target User**: _被允许加入群组的用户_
* **Association Type**: _GROUP\_MEMBERSHIP（群组成员资格）_
* **Association Value**: _群组名称，例如：哈佛大学校友_
* **App**: _可以选择性地将群组成员资格限制在单个应用内_

#### 类似LinkedIn的认证

关联允许用户为其他用户的特定技能进行认证，就像LinkedIn上的功能一样。

* **Association Class**: _User（用户）_
* **Transactor**: _进行认证的用户_
* **Target User**: _被认证的用户_
* **Association Type**: _ENDORSEMENT（背书）_
* **Association Value**: _被认证的技能，例如：SQL | JavaScript | 市场营销 | 演讲_
* **App**: _可以选择性地将认证限制在单个应用内_

#### 投票

关联允许用户提交带有投票选项的帖子，然后用户提交他们的投票。

* **Association Class**: _Post（帖子）_
* **Transactor**: _进行投票的用户_
* **Target Post**: _包含投票的帖子_
* **Association Type**: _POLL\_RESPONSE（投票回应）_
* **Association Value**: _投票回应，例如：是 | 否_
* **App**: _可以选择性地将投票回应限制在单个应用内_

#### 游戏排行榜

关联允许游戏将其每日用户排行榜存储在链上。

例如，考虑一个每日单词游戏挑战，游戏希望存储用户以及他们猜单词所用的尝试次数，以显示每日排行榜。

这可以通过关联实现：

* **Association Class**: _User（用户）_
* **Transactor**: _游戏应用的公钥_
* **Target User**: _获得高分的用户_
* **Association Type**: _2023-01-26\_HIGH\_SCORE(2023-01-26\_高分记录)_
* **Association Value**: _他们的分数，例如：4_
* **App**: _游戏应用的公钥_

DeSo将推出一套推荐的应用程序参考标准。

我们希望这能展示关联所能实现的强大功能，我们迫不及待地想看到开发者们发掘出的新可能性。
