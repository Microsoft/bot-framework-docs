---
title: Bot Framework User-Agent requests - Bot Service
description: Understanding requests from bot framework to your web server.
author: JohnD-ms
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
---
# Bot Framework User-Agent requests

If you’re reading this message, you may have received a request from a Microsoft Bot Framework service. This guide will help you understand the nature of these requests and provide steps to stop them, if so desired.

If you received a request from our service, it likely had a User-Agent header formatted similar to the string below:

```User-Agent: BF-DirectLine/3.0 (Microsoft-BotFramework/3.0 +https://botframework.com/ua)```

The most important part of this string is the **Microsoft-BotFramework** identifier, which is used by the Microsoft Bot Framework, a collection of tools and services that allows independent software developers to create and operate their own bots.

If you’re a bot developer, you may already know why these requests are being directed to your service. If not, continue reading to learn more.

## Why is Microsoft contacting my service?

The Bot Framework connects users on chat services like Skype and Facebook Messenger to bots, which are web servers with REST APIs running on internet-accessible endpoints. The HTTP calls to bots (also called webhook calls) are sent only to URLs specified by a bot developer who registered with the Bot Framework developer portal.

If you’re receiving unsolicited requests from Bot Framework services to your web service, it is likely because a developer has either accidentally or knowingly entered your URL as the webhook callback for their bot.

## To stop these requests

For assistance in stopping undesired requests from reaching your web service, please contact [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).
