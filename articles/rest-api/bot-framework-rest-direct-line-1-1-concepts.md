---
title: Key concepts in the Bot Framework Direct Line API 1.1  - Bot Service
description: Understand key concepts in the Bot Framework Direct Line API 1.1. 
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
---

# Key concepts in Direct Line API 1.1

You can enable communication between your bot and your own client application by using the Direct Line API. 

> [!IMPORTANT]
> This article introduces key concepts in Direct Line API 1.1 and provides information about relevant developer resources. If you are creating a new connection between your client application and bot, use [Direct Line API 3.0](bot-framework-rest-direct-line-3-0-concepts.md) instead.

## Authentication

Direct Line API 1.1 requests can be authenticated either by using a **secret** that you obtain from the Direct Line channel configuration page in the [Azure Portal](https://portal.azure.com) or by using a **token** that you obtain at runtime.  For more information, see [Authentication](bot-framework-rest-direct-line-1-1-authentication.md).

## Starting a conversation

Direct Line conversations are explicitly opened by clients and may run as long as the bot and client participate and have valid credentials. For more information, see [Start a conversation](bot-framework-rest-direct-line-1-1-start-conversation.md).

## Sending messages

Using Direct Line API 1.1, a client can send messages to your bot by issuing `HTTP POST` requests. A client may send a single message per request. For more information, see [Send a message to the bot](bot-framework-rest-direct-line-1-1-send-message.md).

## Receiving messages

Using Direct Line API 1.1, a client can receive messages by polling with `HTTP GET` requests. In response to each request, a client may receive multiple messages from the bot as part of a `MessageSet`. For more information, see [Receive messages from the bot](bot-framework-rest-direct-line-1-1-receive-messages.md).

## Developer resources

### Client library

The Bot Framework provides a client library that facilitates access to Direct Line API 1.1 via C#. To use the client library within a Visual Studio project, install the `Microsoft.Bot.Connector.DirectLine` <a href="https://www.nuget.org/packages/Microsoft.Bot.Connector.DirectLine/1.1.1" target="_blank">v1.x NuGet package</a>. 

As an alternative to using the C# client library, you can generate your own client library in the language of your choice by using the <a href="https://docs.botframework.com/restapi/directline/swagger.json" target="_blank">Direct Line API 1.1 Swagger file</a>.
