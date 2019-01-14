---
title: Send and receive text message | Microsoft Docs
description: Learn about how to send and receive text messages within the Bot Framework SDK.
keywords: sending message, message activities, simple text message, message, text message, receive message  
author: ivorb
ms.author: v-ivorb
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.subservice: sdk
ms.date: 11/08/2018
monikerRange: 'azure-bot-service-4.0'
---

# Send and receive text message 

[!INCLUDE [pre-release-label](../includes/pre-release-label.md)]

The primary way your bot will communicate with users, and likewise receive communication, is through **message** activities. Some messages may simply consist of plain text, while others may contain richer content such as cards or attachments. Your bot's turn handler receives messages from the user, and you can send responses to the user from there. The turn context object provides methods for sending messages back to the user. This article describes how to send simple text messages.

## Send a text message

To send a simple text message, specify the string you want to send as the activity:

# [C#](#tab/csharp)

In the bot's `OnTurnAsync` method, use the turn context object's `SendActivityAsync` method to send a single message response. You can also use the object's `SendActivitiesAsync` method to send multiple responses at once.

```cs
await turnContext.SendActivityAsync($"Welcome!");
```

# [JavaScript](#tab/javascript)

In the bot's `onTurn` handler, use the turn context object's `sendActivity` method to send a single message response. You can also use the object's `sendActivities` method to send multiple responses at once.

```javascript
await context.sendActivity("Welcome!");
```
---
## Receive a text message

To receive a simple text message, use the *text* property of the *activity* object. 

# [C#](#tab/csharp)

In the bot's `OnTurnAsync` method, use the `following code to receive a message. 

```cs
var responseMessage = turnContext.Activity.Text;
```

# [JavaScript](#tab/javascript)

In the bot's `OnTurnAsync` method, use the following code to receive a message. 
```javascript
let text = turnContext.activity.text;
```
---


## Additional resources
For more information about activity processing in general, see [activity processing](~/v4sdk/bot-builder-basics.md#the-activity-processing-stack). For sending richer content, see how to add [rich media](bot-builder-howto-add-media-attachments.md) attachments.
