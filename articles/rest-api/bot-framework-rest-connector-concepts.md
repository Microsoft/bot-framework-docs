---
title: Key concepts in the Bot Connector and Bot State services - Bot Service
description: Understand key concepts in the Bot Framework's Bot Connector service and Bot State service. 
author: RobStand
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/20/2020
---

# Key concepts

Bot Framework and the Azure Bot service allow your bot to communicate with users on Teams, Cortana, Facebook, and more. Channels are available in two forms: as service included as part of Azure Bot Service and as adapter libraries for use with the Bot Framework SDK. This article focuses on the standardized channels included in the Azure Bot Service.

## Bot Framework Channels

Bot Framework Channels enable your bot to exchange messages with channels configured in the [Azure Portal](https://portal.azure.com). It uses industry-standard REST and JSON over HTTPS and enables authentication with JWT Bearer tokens. For detailed information about how to use the Bot Connector service, see [Authentication](bot-framework-rest-connector-authentication.md) and the remaining articles in this section.

### Activity

The Bot Connector service exchanges information between between bot and channel (user) by passing an [Activity][Activity] object. The most common type of activity is **message**, but there are other activity types that can be used to communicate various types of information to a bot or channel. For details about Activities in the Bot Connector service, see [Activities overview](https://aka.ms/botSpecs-activitySchema).

## Authentication

Bot Framework Services use JWT Bearer tokens for authentication. For detailed information about how to authenticate outbound requests that your bot sends to the Bot Framework, how to authenticate inbound requests that your bot receives from the Bot Framework, and more, see [Authentication](bot-framework-rest-connector-authentication.md). 

## Client libraries

The Bot Framework provides client libraries that can be used to build bots in either C#, JavaScript, and Python.

- To build a bot using C#, use the [Bot Framework SDK for C#](../dotnet/bot-builder-dotnet-overview.md). 
- To build a bot using Node.js, use the [Bot Framework SDK for Node.js](../nodejs/index.md). 

In addition to simplifying calls to Bot Framework REST APIs, each Bot Framework SDK also provides a powerful system for building dialogs that encapsulate conversational logic, built-in prompts for simple things such as Yes/No, strings, numbers, and enumerations, built-in support for powerful AI frameworks such as <a href="https://www.luis.ai/" target="_blank">LUIS</a>, and more. 

> [!NOTE]
> As an alternative to using the SDK, you can generate your own client library in the language of your choice by using the <a href="https://aka.ms/connector-swagger-file" target="_blank">Bot Connector Swagger file</a> or code direct to its REST API.

## Bot State service

The Microsoft Bot Framework State service is retired as of March 30, 2018. Previously, bots built on the Azure Bot Service or the Bot Builder SDK had a default connection to this service hosted by Microsoft to store bot state data. Bots will need to be updated to use their own state storage.

## Additional resources

Learn more about building bots using the Bot Connector service by reviewing articles throughout this section, beginning with [Authentication](bot-framework-rest-connector-authentication.md). If you encounter problems or have suggestions regarding the Bot Connector service, see [Support](../bot-service-resources-links-help.md) for a list of available resources. 

[Activity]: bot-framework-rest-connector-api-reference.md#activity-object
