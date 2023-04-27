# 1⃣ 身份：概述

## Starter

欢迎阅读 **DeSo 身份服务**文档！

如果你希望在 DeSo 区块链上构建一个 Web3 应用，那么你很可能需要使用 DeSo 身份服务。\
\
本指南解释了身份服务的工作原理，并应该帮助你了解如何将其集成到你的应用中。本指南适用于广泛的读者，只需对区块链技术和 TypeScript 语言有基本的了解。

在学习编程概念时，同时查看代码实现总是一个好主意。\
\
因此，在本指南中，我们将跟踪 DeSo 协议参考实现，位于[前端代码库](https://github.com/deso-protocol/frontend)下的 [`/src/app/identity.service.ts`](https://github.com/deso-protocol/frontend/blob/main/src/app/identity.service.ts)。\
\
如果你学习了所有关于身份认证的教程，你应该能够编写类似的代码来支持你的应用。\
\
那么让我们开始吧！

## 背景

区块链涉及大量的公钥密码学。

这是因为区块链上的每个交互都是在点对点的基础上进行的，不依赖于传统 Web2 应用中的某个中央权威。

因此，区块链基于消除信任的通信模型，并用公钥密码学的数学逻辑确保可信。

在这种系统中，每个用户都有一对公钥和私钥。

从传统基础设施中寻找类比，公钥就像用户名，私钥则类似于密码。通常，将这些密码原语集成到 Web 或移动应用中需要很高的软件开销和技术知识。

然而，我们认为构建 Web3 应用应该尽可能简单，而且不应该比构建中心化 Web 应用更复杂。

因此，我们创建了 **DeSo 身份服务**。

DeSo 身份服务为在 DeSo 区块链上构建的 Web 和移动应用提供了一种便捷且安全的方式来管理用户凭据（密钥对）。

实际上，当你使用由 DeSo 区块链驱动应用程序时，你可能已经遇到了身份应用。

身份服务充当一个安全的容器，可以通过身份 API 查询来处理与用户密钥材料相关的所有功能。

身份 API 位于[`https://identity.deso.org`](https://identity.deso.org)。

与 iOS 和 Android 的集成可以通过派生密钥或 Webview 完成，如我们的[mobile-integration.md](mobile-integration.md "mention")指南所述。

为了简化沟通，本文档中将 DeSo 身份服务简称为身份。

现在，请查看[concepts.md](concepts.md "mention") 指南，开始集成 DeSo 身份服务。
