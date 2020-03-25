---
title: Manage state data - Bot Service
description: Learn how to store and retrieve state data using the Bot State service. 
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
---

# Manage state data

Bots typically use storage to keep track of a user's place in the conversation or details relevant to their relationship with the bot. The Bot Framework SDK manages user and conversation state automatically for bot developers. 

Originally, Bot Framework shipped with a state service for storing this data, but most modern bots (and all recent releases of the Bot Framework SDK) use storage controlled directly by the bot developer instead of this centrally managed service. 

The central Bot Framework State service is retired as of March 30, 2018. For more information, see [Reminder: The Bot Framework State service has been retired – what you need to know](https://blog.botframework.com/2018/04/02/reminder-the-bot-framework-state-service-has-been-retired-what-you-need-to-know/).
