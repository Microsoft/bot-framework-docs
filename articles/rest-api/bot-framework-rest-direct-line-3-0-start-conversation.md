---
title: Start a conversation - Bot Service
description: Learn how to start a conversation using Direct Line API v3.0. 
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/13/2017
---

# Start a conversation

Direct Line conversations are explicitly opened by clients and may run as long as the bot and client participate and have valid credentials. While the conversation is open, both the bot and client may send messages. More than one client may connect to a given conversation and each client may participate on behalf of multiple users.

## Open a new conversation

To open a new conversation from your client, issue POST to the /v3/directline/conversations endpoint.

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer SECRET_OR_TOKEN
```

The following snippets provide an example of the Start Conversation request and response.

### Request

```http
POST https://directline.botframework.com/v3/directline/conversations
Authorization: Bearer RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn
```

### Response

If the request is successful, the response will contain an ID for the conversation, a token, a value that indicates the number of seconds until the token expires, and a stream URL that the client may use to [receive activities via WebSocket stream](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket).

```http
HTTP/1.1 201 Created
[other headers]
```

```json
{
  "conversationId": "abc123",
  "token": "RCurR_XV9ZA.cwA.BKA.iaJrC8xpy8qbOF5xnR2vtCX7CZj0LdjAPGfiCpg4Fv0y8qbOF5xPGfiCpg4Fv0y8qqbOF5x8qbOF5xn",
  "expires_in": 1800,
  "streamUrl": "https://directline.botframework.com/v3/directline/conversations/abc123/stream?t=RCurR_XV9ZA.cwA..."
}
```

Typically, a Start Conversation request is used to open a new conversation and an **HTTP 201** status code is returned if the new conversation is successfully started. However, if a client submits a Start Conversation request with a Direct Line token in the `Authorization` header that has previously been used to start a conversation using the Start Conversation operation, an **HTTP 200** status code will be returned to indicate that the request was acceptable but no conversation was created (as it already existed).

> [!TIP]
> You have 60 seconds to connect to the WebSocket stream URL. If the connection cannot be established during this time, you can [reconnect to the conversation](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md) to generate a new stream URL.

## Start Conversation versus Generate Token

The Start Conversation operation (`POST /v3/directline/conversations`) is similar to the [Generate Token](bot-framework-rest-direct-line-3-0-authentication.md#generate-token) operation (`POST /v3/directline/tokens/generate`) in that both operations return a `token` that can be used to access a single conversation. However, the Start Conversation operation also starts the conversation, contacts the bot, and creates a WebSocket stream URL, whereas the Generate Token operation does none of these things. 

If you intend to start the conversation immediately with your client, use the Start Conversation operation. If you plan to distribute the token to clients and want them to initiate the conversation, use the [Generate Token](bot-framework-rest-direct-line-3-0-authentication.md#generate-token) operation instead. 

## Additional resources

- [Key concepts](bot-framework-rest-direct-line-3-0-concepts.md)
- [Authentication](bot-framework-rest-direct-line-3-0-authentication.md)
- [Receive activities via WebSocket stream](bot-framework-rest-direct-line-3-0-receive-activities.md#connect-via-websocket)
- [Reconnect to a conversation](bot-framework-rest-direct-line-3-0-reconnect-to-conversation.md)
