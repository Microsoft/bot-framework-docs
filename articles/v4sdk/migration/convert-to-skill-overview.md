---
title: Converting a v3 bot to a skill overview - Bot Service
description: Provides an overview of converting your v3 bot to a skill, and consuming it from a v4 bot.
keywords: bot migration
author:  clearab
ms.author: anclear
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/28/2020
monikerRange: 'azure-bot-service-4.0'
---

# Convert a v3 bot to a skill

In some scenarios it may not make sense to migrate from a v3 bot to a v4 bot immediately, but you may still want to take advantage of the additional functionality available in the v4 SDK. In these cases it may make sense to convert your v3 bot to a skill, and create a skill consumer bot based on the v4 SDK to pass messages to your v3 bot. See [the skills overview article](../skills-conceptual.md) for additional information on skills and skill consumers.

For more complex bots, this approach can also allow for a gradual migration. You'll be able to control which messages are handled in your skill consumer bot, and which are passed on to your skill. This let's you migrate functionality to the new bot in stages, and you can eventually retire the skill once all the functionality has moved.

## What's required

### Upgrade your v3 bot SDK to the most current 3.x version

The hooks necessary to convert your bot to a skill were added to version 3.30.2 of the .NET SDK and version 3.30.0 of the JavaScript SDK.

### Convert your v3 bot to a skill

Once you've upgraded your SDK version you'll need to add some logic to your bot to handle sending and receiving messages to your skill consumer bot.

### Create a v4 skill consumer bot

You'll next need to create a bot to act as your skill consumer, and add logic to determine which messages are routed to your skill. This new bot will be what your customers interact with, and where you'll add additional functionality built on the expanded capabilities of the v4 SDK.

### Connect your users to the new skill consumer bot

Finally, you'll need to replace your v3 bot with the new v4 bot you've created.

## Get started

Ready to get started?

- [Convert a .NET v3 bot to a skill](net-v3-as-skill.md)
- [Convert a JavaScript v3 bot to a skill](javascript-v3-as-skill.md)
