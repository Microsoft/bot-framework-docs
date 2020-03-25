---
title: End a conversation - Bot Service
description: Learn how to end a conversation using Direct Line API v3.0. 
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
---

# End a conversation

The **endOfConversation** [activity](https://aka.ms/botSpecs-activitySchema) means the channel or bot has ended the conversation. 

> [!NOTE] 
> While the **endOfConversation** event is only sent by very few channels, the Cortana channel is the only one that accepts it. Other channels, including Direct Line, do not implement this functionality and instead drop or forward the activity on; each channel determines how to react to an endOfConversation activity.

## Send an endOfConversation activity

To request to end a conversation with Cortana channel, POST End of Conversation Activity to the channel's messaging endpoint.

### Request

```http
POST https://directline.botframework.com/v3/directline/conversations/abc123/activities
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0
[other headers]
```

```json
{
    "type": "endOfConversation",
    "from": {
        "id": "user1"
    }
}
```

### Response

If the request is successful, the response will contain an ID for the activity that was sent.

```http
HTTP/1.1 200 OK
[other headers]
```

```json
{
  "id": "0004"
}
```

## Additional resources

- [Key concepts](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentication](bot-framework-rest-direct-line-3-0-authentication.md)
- [Send an activity to the bot](bot-framework-rest-direct-line-3-0-send-activity.md)
