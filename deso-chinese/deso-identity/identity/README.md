# 1⃣ Identity: Overview

## Starter

Welcome to the **DeSo Identity Service** documentation!\
\
If you're looking to build a Web3 app on the DeSo blockchain, you will most likely want to use the DeSo Identity Service.\
\
This guide explains how Identity works and it should give you a good understanding of how to integrate it into your app. This guide is intended for a broad range of readers and assumes only a basic understanding of blockchain technology and the TypeScript language.\
\
When learning programming concepts it's always a good idea to simultaneously look at a code implementation.\
\
That's why in this guide, we will be tracing through the DeSo Protocol reference implementation located in the [frontend repository,](https://github.com/deso-protocol/frontend) under [`/src/app/identity.service.ts`](https://github.com/deso-protocol/frontend/blob/main/src/app/identity.service.ts).\
\
If you go through all of the Identity tutorials, you should be able to write a similar code to support your application. \
\
So let's get started!

## Background

Blockchains involve a lot of public key cryptography.

This is because every interaction on a blockchain occurs on a peer-to-peer basis, without relying on some central authority such as in traditional Web2 applications.

As a result, blockchains are based on communication models that eliminate trust from the equation and substitute it for the mathematical confidence of public key cryptography.

In such systems, each user has a pair of public and private keys.&#x20;

Drawing an analogy from traditional infrastructures, public keys are like usernames, and private keys work similarly to passwords. Typically, integrating these cryptographic primitives into a web or mobile application would have required significant software overhead and technical knowledge.

However, we believe that building Web3 applications should be as simple as possible, and no more complicated than building apps on the centralized web.

And so, we created the **DeSo Identity Service**.

The DeSo Identity service provides a convenient and secure way to manage user credentials (key pairs) in web and mobile applications built on the DeSo blockchain.

In fact, you've probably already encountered the Identity app when using applications powered by the DeSo blockchain.

Identity acts as a secured container that can be queried through Identity API to handle all the functionality related to users' key material.

The Identity API is located under [`https://identity.deso.org`](https://identity.deso.org).

Currently, it integrates most smoothly with web applications, which can be accomplished using our [window-api](../window-api/ "mention") and [iframe-api](../iframe-api/ "mention").

Integrations with iOS and Android can be done through derived keys or webview, as explained in our [mobile-integration.md](mobile-integration.md "mention") guide.

For simpler communication, the DeSo Identity Service will henceforth be referred to as Identity in this documentation.

Now check out the [concepts.md](concepts.md "mention") guide to get started integrating with the DeSo Identity Service.
