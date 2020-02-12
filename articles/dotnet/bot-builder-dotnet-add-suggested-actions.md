---
title: Add suggested actions to messages (v3 C#) - Bot Service
description: Learn how to add suggested actions to messages using the Bot Framework SDK for .NET.
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 03/13/2018
monikerRange: 'azure-bot-service-3.0'
---
# Add suggested actions to messages

[!INCLUDE [pre-release-label](../includes/pre-release-label-v3.md)]

> [!div class="op_single_selector"]
> - [.NET](../dotnet/bot-builder-dotnet-add-suggested-actions.md)
> - [Node.js](../nodejs/bot-builder-nodejs-send-suggested-actions.md)
> - [REST](../rest-api/bot-framework-rest-connector-add-suggested-actions.md)

[!INCLUDE [Introduction to suggested actions](../includes/snippet-suggested-actions-intro.md)]

## Send suggested actions

To add suggested actions to a message, set the `SuggestedActions` property of the activity to a list of [CardAction][cardAction] objects that represent the buttons to be presented to the user. 

This code example shows how to create a message that presents three suggested actions to the user:

[!code-csharp[Add suggested actions](../includes/code/dotnet-add-suggested-actions.cs#addSuggestedActions)]

When the user taps one of the suggested actions, the bot will receive a message from the user that contains the `Value` of the corresponding action.

## Additional resources

- [Activities overview](bot-builder-dotnet-activities.md)
- [Create messages](bot-builder-dotnet-create-messages.md)
- [Activity class](https://aka.ms/ActivityClass-dotnet-API)
- <a href="/dotnet/api/microsoft.bot.connector.imessageactivity" target="_blank">IMessageActivity interface</a>
- <a href="/dotnet/api/microsoft.bot.connector.cardaction" target="_blank">CardAction class</a>
- <a href="/dotnet/api/microsoft.bot.connector.suggestedactions" target="_blank">SuggestedActions class</a>

[cardAction]: /dotnet/api/microsoft.bot.connector.cardaction

[inspector]: ../bot-service-channel-inspector.md


