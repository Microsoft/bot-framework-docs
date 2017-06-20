---
title: About the Bot Framework | Microsoft Docs
description: Learn about the Microsoft Bot Framework, a comprehensive framework of tools and services to build and deploy high quality bots.
author: RobStand
ms.author: rstand
manager: rstand
ms.topic: article
ms.prod: bot-framework
ms.date: 05/31/2017
---

# About the Bot Framework

The Bot Framework is a platform for building, connecting, testing, and deploying powerful and intelligent bots. With support for .NET, Node.js, and REST, you can [get the Bot Builder SDK](resources-tools-downloads.md) and quickly [start building bots](bot-builder-overview-getstarted.md) with the Bot Framework.

The following 15 minute video provides an overview of Bot Framework features.
<iframe src="https://channel9.msdn.com/Events/Build/2017/C9R03/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## What is a bot?
Think of a bot as an app that users interact with in a conversational way. Bots can communicate conversationally with text, cards, or speech. A bot may be as simple as basic pattern matching with a response, or it may be a sophisticated weaving of artificial intelligence techniques with complex conversational state tracking and integration to existing business services.

The Bot Framework enables you to build bots that support different types of interactions with users. You can design conversations in your bot to be freeform. Your bot can also have more guided interactions where it provides the user choices or actions. The conversation can use simple text strings or more complex rich cards that contain text, images, and action buttons. And you can add natural language interactions, which let your users interact with your bots in a natural and expressive way. 

Let's look at an example of a bot that schedules salon appointments. The bot understands the user's intent, presents appointment options using action buttons, displays the user's selection when they tap an appointment, and then sends a thumbnail card that contains the appointment's specifics. 
<p> 
<div style="text-align: center" markdown="1"> 
![salon bot example](~/media/salon_bot_example.png) 
</div>  

Bots are rapidly becoming an integral part of digital experiences. They are becoming as essential as a website or a mobile experience for users to interact with a service or application.

## Why use the Bot Framework?
Developers writing bots all face the same problems: bots require basic I/O, they must have language and dialog skills, and they must connect to users, preferably in any conversation experience and language the user chooses. The Bot Framework provides powerful tools and features to help solve these problems.

### Bot Builder
To help you build your bot, the Bot Framework includes [Bot Builder](bot-builder-overview-getstarted.md), which provides rich and full-featured SDKs for the .NET and Node.js platforms. The SDKs provide features that make interactions between bots and users much simpler. Bot Builder also includes an emulator for debugging your bots, as well as a large set of sample bots you can use as building blocks. 

### Bot Framework portal
Managing your bot is easy with the Bot Framework  portal. The Bot Framework  portal gives you one convenient place to register, connect, and manage your bot. It also provides diagnostic tools and a web chat control you can use to embed your bot on a web page.

![Configure a bot in the developer portal](~/media/portal-configure-bot.png) 

### Channels
The Bot Framework supports several popular channels for connecting your bots and people. Users can start conversations with your bot on any channel that you've configured your bot to work with, including email, Facebook, Skype, Slack, and SMS.

![List of channels on the portal](~/media/portal-channels-list.png) 

 Hey guys, I saw wechat icon, This is great, But is it ready for now? I can not see it in the dev.botframework.com portal.

## Build smart bots
You can take advantage of Microsoft Cognitive Services to add smart features like natural language understanding, image recognition, speech, and more. [Learn more](~/cognitive-services-bot-intelligence-overview.md) about adding intelligence to your bot.

## Next steps
Dive deeper into [the capabilities](overview-how-bot-framework-works.md) of the Bot Framework. Get started  [building your first bot](bot-builder-overview-getstarted.md) and learn more about [designing great bots](~/bot-design-principles.md).

[NodeGetStarted]:~/nodejs/bot-builder-nodejs-quickstart.md
[DotNETGetStarted]:~/dotnet/bot-builder-dotnet-quickstart

