---
title: Key concepts in the Bot Framework Direct Line API 3.0  - Bot Service
description: Understand key concepts in the Bot Framework Direct Line API 3.0. 
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/06/2019
---

# Key concepts in Direct Line API 3.0

You can enable communication between your bot and your own client application by using the Direct Line API. This article introduces key concepts in Direct Line API 3.0 and provides information about relevant developer resources. You can build a client using the SDK, REST API, or Web Chat. 

## Authentication

Direct Line API 3.0 requests can be authenticated either by using a **secret** that you obtain from the Direct Line channel configuration page in the [Azure Portal](https://portal.azure.com) or by using a **token** that you obtain at runtime. For more information, see [Authentication](bot-framework-rest-direct-line-3-0-authentication.md).

## Starting a conversation

Direct Line conversations are explicitly opened by clients and may run as long as the bot and client participate and have valid credentials. For more information, see [Start a conversation](bot-framework-rest-direct-line-3-0-start-conversation.md).

## Sending messages

Using Direct Line API 3.0, a client can send messages to your bot by issuing `HTTP POST` requests. A client may send a single message per request. For more information, see [Send an activity to the bot](bot-framework-rest-direct-line-3-0-send-activity.md).

## Receiving messages

Using Direct Line API 3.0, a client can receive messages from your bot either via `WebSocket` stream or by issuing `HTTP GET` requests. Using either of these techniques, a client may receive multiple messages from the bot at a time as part of an `ActivitySet`. For more information, see [Receive activities from the bot](bot-framework-rest-direct-line-3-0-receive-activities.md).

## Developer resources

### Client libraries

The Bot Framework provides client libraries that facilitate access to Direct Line API 3.0 via C# and Node.js. 

- To use the .NET client library within a Visual Studio project, install the `Microsoft.Bot.Connector.DirectLine` <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine" target="_blank">NuGet package</a>. 

- To use the Node.js client library, install the `botframework-directlinejs` library using <a href="https://www.npmjs.com/package/botframework-directlinejs" target="_blank">NPM</a> (or <a href="https://github.com/Microsoft/BotFramework-DirectLineJS" target="_blank">download</a> the source).

::: moniker range="azure-bot-service-3.0"

### Sample code

The <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples" target="_blank">BotBuilder-Samples</a> GitHub repo contains multiple samples that show how to use Direct Line API 3.0 with C# and Node.js.

| Sample | Language | Description |
|----|----|----|
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/core-DirectLine" target="_blank">Direct Line Bot Sample</a> | C# | A sample bot and a custom client communicating to each other using the Direct Line API. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/CSharp/core-DirectLineWebSockets" target="_blank">Direct Line Bot Sample (using client WebSockets)</a> | C# | A sample bot and a custom client communicating to each other using the Direct Line API and WebSockets. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/core-DirectLine" target="_blank">Direct Line Bot Sample</a> | JavaScript | A sample bot and a custom client communicating to each other using the Direct Line API. |
| <a href="https://github.com/Microsoft/BotBuilder-Samples/tree/v3-sdk-samples/Node/core-DirectLineWebSockets" target="_blank">Direct Line Bot Sample (using client WebSockets)</a> | JavaScript | A sample bot and a custom client communicating to each other using the Direct Line API and WebSockets. |

::: moniker-end

### Web chat control 

The Bot Framework provides a control that enables you to embed a Direct-Line-powered bot into your client application. For more information, see the <a href="https://github.com/Microsoft/BotFramework-WebChat" target="_blank">Microsoft Bot Framework WebChat control</a>.
