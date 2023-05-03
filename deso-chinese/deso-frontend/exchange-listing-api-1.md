---
description: Use this React example to start building your first app on DeSo
---

# 2⃣ 前端：React 示例

这是一个简单的 [Create React App](https://create-react-app.dev/docs/getting-started) 项目，但这些示例可以轻松移植到您喜欢的框架或构建工具中。

Github: [https://github.com/deso-protocol/deso-examples-react](https://github.com/deso-protocol/deso-examples-react)

### 如何在本地运行这些示例

在您的终端中运行以下命令

```
git clone https://github.com/deso-protocol/deso-examples-react.git
cd deso-examples-react
npm i
npm run start
```

### 如何使用此仓库

如果您想将这些示例移植到您自己的应用程序中，请根据您喜欢的工具（Create React App、Vite、Nextjs、Remix、Angular、Vue 等）的文档设置一个项目。

如果您不确定，Create React App 是为快速原型设计/实验搭建开发环境的合理选择。

接下来，使用您喜欢的包管理器安装  [DeSo Protocol SDK](https://www.npmjs.com/package/deso-protocol)：

```
# npm
npm i deso-protocol

# yarn
yarn add deso-protocol
```

最后，使用此仓库中的示例帮助您为应用程序构建社交功能。

代码中有很多注释，但如果有任何不清楚的地方，请提出问题！

### 示例

* [配置](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/root.jsx#L7)
* [登录](https://github.com/deso-protocol/deso-examples-react/blob/main/src/components/nav.jsx#L27)
* [登出](https://github.com/deso-protocol/deso-examples-react/blob/main/src/components/nav.jsx#L31)
* 状态同步
  1. [创建React上下文](https://github.com/deso-protocol/deso-examples-react/blob/main/src/contexts.js#L7)
  2. [创建useState钩子](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/root.jsx#L18)
  3. [创建useEffect钩子](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/root.jsx#L24)
  4. [订阅身份](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/root.jsx#L40)
  5. [实例化一个上下文提供者](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/root.jsx#L117)
  6. [在任何地方使用身份登录状态](https://github.com/deso-protocol/deso-examples-react/blob/main/src/components/nav.jsx#L8)
  7. [在代码中响应变更](https://github.com/deso-protocol/deso-examples-react/blob/main/src/components/nav.jsx#L16)
* [查看权限](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/sign-and-submit-tx.jsx#L8)
* [请求权限](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/sign-and-submit-tx.jsx#L50)
* [创建，签名，提交交易](https://github.com/deso-protocol/deso-examples-react/blob/main/src/routes/sign-and-submit-tx.jsx#L61)
