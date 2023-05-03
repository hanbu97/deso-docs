# 在本地设置节点和前端

本文档将教您如何设置开发环境。

虽然这不是一个严格的先决条件，但我们建议您先浏览一下 [DeSo代码漫步](broken-reference/)，因为它提供了一些有用的背景信息。

## 先决条件

要运行前端仓库，您需要运行**Node v13.13.0**和 **NPM 6.14.4**

我们建议使用NVM来设置这个环境。要运行后端，您需要安装Go v1.15.6。\
\
可以通过运行以下命令通过 [homebrew](https://brew.sh/) 安装它们：

```bash
brew install nvm
# 按照输出设置您的〜/.nvm文件夹，nvm脚本和shell补全
nvm install 13
# 检查版本
node -v
npm -v
# 然后安装go
brew install go@1.15
# 检查版本
go version
```

我们还假设您已经安装了 [Goland](https://www.jetbrains.com/go/) 。这是开发DeSo的推荐IDE，因为大部分代码都是Go代码。

另一个先决条件是 [vips](https://github.com/libvips/libvips)，可以通过 [homebrew](https://brew.sh/) 安装 –`brew install vips`– 或在Ubuntu上使用`apt install libvips-tools`安装。

## 设置

首先，您需要将所有仓库同步到同一个目录中。

其中一些仓库在技术上是可选的，但同步所有仓库可以让您更容易地在代码之间跳转。&#x20;

```
cd $WORKING_DIRECTORY
git clone https://github.com/deso-protocol/core.git
git clone https://github.com/deso-protocol/backend.git
git clone https://github.com/deso-protocol/frontend.git
git clone https://github.com/deso-protocol/identity.git
```

一旦所有这些仓库都被检出，我们建议将它们导入到一个Goland项目中。这样，您可以同时在所有仓库中进行搜索和开发。

要做到这一点，打开**Goland**，**hit**点击 **File > Open**，选择一个仓库文件夹，然后在提示时选择“**Attach**附加”。

如果您正确操作，您应该在一个Goland项目中加载所有四个仓库。

如果您不熟悉Goland，以下快捷键对于跳转代码很有用([完整速查表在此](https://www.jetbrains.com/help/go/mastering-keyboard-shortcuts.html)):

* **SHIFT+SHIFT:** 使用模糊搜索在所有四个仓库中打开任意文件。
* **CTRL+SHIFT+F:** 一次在所有四个仓库中使用正则表达式搜索。
* **CTRL+SHIFT+A:** 运行任何您通常可以在菜单中找到的操作。
* **CTRL+B:** 跳转到定义或查找用法。

如果您喜欢Vim，您还可以安装Vim插件，以便获得典型的Vim快捷键。

## 在本地构建和运行

### Running the frontend in development mode

```
# 假设我们从$WORKING_DIRECTORY开始，其中包含所有仓库
cd frontend
npm install

# 以下命令将在localhost:4200上提供前端服务，当发生更改时，会自动重新加载。
# 但在网站实际运行之前，您必须运行一个节点（请参阅下一节）。
npm start
```

### 在测试网模式下运行节点

```
# 假设我们从$WORKING_DIRECTORY开始，其中包含所有仓库。
# 另外假设我们已经运行了"ng serve"。
cd backend/scripts/nodes

# n0_test脚本在本地运行一个测试网区块链。它会马上启动，并以比主网更快的速度开始挖矿。
# 您可以通过在参数中设置--miner-public-key将您的公钥设置为接收区块奖励。
# 这将为您提供用于测试的资金。
# 您可以在登录一个帐户后，转到Admin选项卡，然后转到Network子选项卡查看节点状态。
export CGO_CFLAGS_ALLOW="-Xpreprocessor"
./n0_test

# 当n0_test运行时，您必须导航到以下URL。4200是ng serve的端口。请注意！
http://localhost:4200
```

\
默认情况下，您的浏览器将指向`localhost:17001`，这是默认的“主网”API端口。

然而，当您运行n0\_test时，您的节点会在`localhost:18001`上启动。\
\
要将您的前端指向您的测试网节点，您需要打开检查器，将`lastLocalNodev2`参数更改为`localhost:18001`，如下面的截图所示。\
\
在您执行此操作后，您应该可以注册，一切应该正常工作。\\

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

### 在主网模式下运行节点

大部分时间，我们使用测试网模式进行开发，因为它速度快且成本低。

然而，在推送之前确保我们的更改可以正常工作，我们喜欢在本地运行完整的主网节点。

```
# 假设我们从$WORKING_DIRECTORY开始，其中包含所有仓库。
# 另外假设我们已经运行了"ng serve"。
cd backend/scripts/nodes

# n0脚本运行一个连接到主网的同步节点。
# 它将从同步节点下载所有区块，然后开始从它们同步内存池。
# 您可以在登录一个帐户后，转到Admin选项卡，然后转到Network子选项卡查看节点状态。
# 请注意，同步区块链可能需要一个小时左右。
$ ./n0

# 当n0运行时，您必须导航到以下URL。4200是ng serve端口。
# 它应该自动连接到您的节点，该节点应该在localhost:17001上公开其API。
http://localhost:4200
```

## 运行本地身份服务（可选）

通常不需要在本地运行身份服务。

但是，运行它就像运行Angular应用程序一样简单：

```
# 假设我们从$WORKING_DIRECTORY开始，其中包含所有仓库
cd identity
npm install

# 安装angular cli
sudo npm install -g @angular/cli typescript tslint dep

# 以下命令将在localhost:4201上提供身份服务，并在发生更改时自动重新加载。
ng serve --port 4201
```

要将浏览器指向本地身份服务，而不是identity.deso.org，您需要更改一个localStorage值，类似于我们在运行测试网节点时所做的。

在这种情况下，我们需要将`lastIdentityServiceURL`更改为`http://localhost:4201`。

请参阅下面的截图：

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
