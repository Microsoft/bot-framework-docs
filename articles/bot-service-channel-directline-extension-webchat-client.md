---
title: Use WebChat with the direct line app service extension
titleSuffix: Bot Service
description: Use WebChat with the direct line app service extension
services: bot-service
manager: kamrani
ms.service: bot-service
ms.topic: conceptual
ms.author: kamrani 
ms.date: 07/25/2019
---

# Use WebChat with the direct line app service extension

This article describes how to use WebChat with the direct line app service extension.

## Get Your Direct Line Secret

The first step is to find your direct line secret. You can do this by following the instructions described in [Connect a bot to Direct Line](bot-service-channel-connect-directline.md) article.

## Get the preview version of DirectLineJS
The preview version of DirectLineJS can be found here:
https://github.com/Jeffders/DirectLineAppServiceExtensionPreview/tree/master/libraries

## Integrate WebChat client

Generally speaking, the approach is the same as before. With the exception that a new version of **WebChat** has been created that supports two-way **WebSocket** traffic, which instead of connecting to [https://directline.botframework.com/](https://directline.botframework.com/) connects directly to your hosted bot.
The direct line URL for your bot will be `https://<your_app_service>.azurewebsites.net/.bot/`, where the `/.bot/` extension is the Direct Line **endpoint** on your App Service.
If you can configure your own domain name you still must append the `/.bot/` path to access the direct line REST APIs.

1. Exchange the secret for a token by following the instructions in the [Authentication](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-authentication?view=azure-bot-service-4.0) article. But, instead of obtaining a token at this location: `https://directline.botframework.com/v3/directline/tokens/generate`, you generate the token directly from your Direct Line App Service Extension at this location: `https://<your_app_service>.azurewebsites.net/.bot/v3/directline/tokens/generate`.  

1. Once you have a token, you can update the webpage that uses WebChat with these changes:

```html
<!DOCTYPE html>
<html lang="en-US">
<head>
    <title>Direct Line Streaming Sample</title>
    <script src="~/directLine.js"></script>
    <script src="https://cdn.botframework.com/botframework-webchat/master/webchat.js"></script>
    <style>
        html, body {
            height: 100%
        }

        body {
            margin: 0
        }

        #webchat,

        #webchat > * {
            height: 100%;
            width: 100%;
        }
    </style>
</head>

<body>
    <div id="webchat" role="main"></div>
    <script>
        const activityMiddleware = () => next => card => {
            if (card.activity.type === 'trace') {
                // Return false means, don't render the trace activities
                return () => false;
            } else {
                return children => next(card)(children);
            }
        };


        var dl = new DirectLine.DirectLine({
            secret: '<your token>',
            domain: 'https://<your_site>.azurewebsites.net/.bot/v3/directline',
            webSocket: true,
            conversationId: '<your conversation id>'
        });
        window.WebChat.renderWebChat({
            activityMiddleware,
            directLine: dl,
            userID: '<your generated user id>'
        }, document.getElementById('webchat'));
    </script>
</body>
</html>

```
