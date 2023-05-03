# 进行首次更改

在本教程中，我们将向您展示如何对DeSo代码库进行更改，并在本地开发环境中查看更改的效果。

## 先决条件

本指南假定您已经成功完成了上一部分的内容——[**设置你的开发环境**](broken-reference/)**。**

特别是，它假定您正在运行一个测试网络节点，用n0\_test显示一个前端用户界面，大致如下图所示：

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## 进行您的第一个前端更改

如果您的前端仓库已加载到Goland中，以下步骤应该能让您进行第一次前端更改，并实时查看本地节点的更新：\\

* 运行您的n0\_test。创建一个帐户，并确保您可以看到先决条件中显示的页面。\\
* 假设您正在使用Goland，请导航到`feed.component.ts`。提示：您可以使用SHIFT+SHIFT轻松跳转到它。\\
* 在文件中查找`GLOBAL_TAB`函数，并修改返回语句如下（您可以为您的信息流命名任何您喜欢的名称）：\\
  * `static GLOBAL_TAB = "Satoshi's Feed";`\\
* 保存您的更改。

保存更改后，您的浏览器应该更新为为您的信息流标签显示新标题：

<figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## 进行您的第一个后端更改

后端仓库运行一个API，前端Angular应用程序查询该API以获取其显示的所有信息。按照以下步骤进行我们的第一次API更改：

* 在深入代码之前，请转到管理员面板，将帖子添加到全局信息流，并通过刷新页面验证它是否显示。\\
* 使用Goland加载后端仓库后，找到`post.go`文件，其中定义了前端查询的一个API接口。提示：您可以使用SHIFT+SHIFT导航到它。\\
* 在该文件中，找到一个名为`GetPostsStateless`的函数。修改该函数末尾的响应，如下所示，以定制内容：\\
  * ```
        if len(postEntryResponses) > 0 {
            postEntryResponses[0].Body = "This is some content"
        }

        // 返回找到的帖子
        res := &GetPostsStatelessResponse{
            PostsFound: postEntryResponses,
        }
    ```
* 保存文件并重新启动n0\_test。当您对后端或核心的任何内容进行更改时，您需要重新启动节点以使它们生效。\\

现在，您应该在添加到信息流中的帖子中看到一些自定义内容。您可以修改后端中的这个接口来自定义返回给用户的数据。

![](../../.gitbook/assets/desopage3.png)

##
