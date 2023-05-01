---
description: Description of steps required to download and start your node
---

# 2⃣ 节点：设置

设置 DeSo 节点是一个简单的过程，但首先请确认您已安装了[#docker](requirements.md#docker "mention") 和[#git](requirements.md#git "mention")。

### 克隆代码仓库

在您希望存放节点内容的位置创建一个文件夹，然后在该位置打开您选择的终端。

在终端中运行命令 `git clone` [`https://github.com/deso-protocol/run.git`](https://github.com/deso-protocol/run.git) 。

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

### 下载容器

安装完成后，使用 `cd run` 命令进入 run 文件夹，然后执行命令`./run.sh`。

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

一个小终端将出现并自动下载节点的前端、后端和 nginx 容器。这可能需要几分钟的时间。

请注意，要打开或关闭节点，请打开 Docker GUI，导航到容器/应用程序选项卡，将鼠标悬停在 run 选项卡上，然后点击启动/停止按钮以打开或关闭节点。

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

恭喜您，您的 Deso 节点现在已在本地运行！在浏览器中导航至 [http://deso.run](http://deso.run/) 或 `locahost:8080`以查看您的本地运行的节点。
