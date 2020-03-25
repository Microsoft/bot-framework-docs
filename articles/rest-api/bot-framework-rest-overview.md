---
title: Bot Framework REST APIs  - Bot Service
description: Get started with the Bot Framework REST APIs that can be used to build bots and clients that connect to bots.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
---

# Bot Framework REST APIs

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-overview.md)
> - [Node.js](../nodejs/bot-builder-nodejs-overview.md)
> - [REST](../rest-api/bot-framework-rest-overview.md)

Most Bot Framework bots are built using the Bot Framework SDK, which organizes your bot and handles all conversation for you. An alternate to using the SDK is to send messages directly to the bot using REST API.

## Build a bot

By coding to Bot Framework REST APIs, you can send and receive messages with users on any channel configured in your bot's Azure Bot Service registration.

> [!TIP]
> The Bot Framework provides client libraries that can be used to build bots in either C# or Node.js.
> To build a bot using C#, use the [Bot Framework SDK for C#](../dotnet/bot-builder-dotnet-overview.md).
> To build a bot using Node.js, use the [Bot Framework SDK for Node.js](../nodejs/index.md).

Refer to [Azure Bot service](../bot-service-overview-introduction.md) to learn earn more about building bots using the service.

## Build a Direct Line or Web Chat client

Most channels, such as Facebook, Teams, or Slack provide clients, but with Direct Line and Web Chat you can enable your own client application to communicate with your bot. The Direct Line API implements an authentication mechanism that uses standard secret/token patterns and provides a stable schema, even if your bot changes its protocol version. To learn more about using the Direct Line API to enable communication between a client and your bot, see [Key concepts](bot-framework-rest-direct-line-3-0-concepts.md).

Direct Line and Web Chat clients can be in different languages and deployment (e.g. an app instead of a service). For more information, see [About Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-directline?view=azure-bot-service-4.0).
