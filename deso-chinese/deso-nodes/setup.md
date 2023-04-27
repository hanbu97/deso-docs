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

A small terminal will appear and automatically download the containers for the node's frontend, backend, and nginx. This may take a few minutes.

Note in order to turn your node on or off open the Docker GUI navigate to the containers/apps tab, hover over the run tab, and hit the start/stop button to turn your node on or off.

![](../.gitbook/assets/docker-toggle-container.PNG)

Congratulations, your Deso node is now running locally! Navigate to [http://deso.run](http://deso.run/) or `locahost:8080` in your browser to see your local instance.
