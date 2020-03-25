---
title: Convert a JavaScript v3 bot to a skill
description: Shows how to convert existing JavaScript v3 bots to skills and consume them from a JavaScript v4 bot.
keywords: JavaScript, bot migration, skills, v3 bot
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2020
monikerRange: 'azure-bot-service-4.0'
---

# Convert a JavaScript v3 bot to a skill

This article describes how to convert 2 sample JavaScript v3 bots to skills and to create a v4 skill consumer that can access these skills.
To convert a .NET v3 bot to a skill, see how to [Convert a .NET v3 bot to a skill](net-v3-as-skill.md).
To migrate a JavaScript bot from v3 to v4, see how to [Migrate a Javascript v3 bot to a v4 bot](conversion-javascript.md).

## Prerequisites

- Visual Studio Code.
- Node.js.
- An Azure subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.
- To test the skills, you will need the Bot Framework emulator and local copies of the bots:
  - The v3 JavaScript echo skill: [**Skills/v3-skill-bot**](https://aka.ms/v3-js-echo-skill).
  - The v3 JavaScript booking skill: [**Skills/v3-booking-bot-skill**](https://aka.ms/v3-js-booking-skill).
  - The v4 JavaScript sample skill consumer: [**Skills/v4-root-bot**](https://aka.ms/js-simple-root-bot).

## About the bots

In this article, each v3 bot is created to act as a skill. A v4 skill consumer is included, so that you can test the converted bots as skills.

- The **v3-skill-bot** echoes back messages it receives. As a skill, it _completes_ when it receives an "end" or "stop" message. The bot to convert is based on a minimal v3 bot.
- The **v3-booking-bot-skill** allows the user to book a flight or a hotel. As a skill, it sends collected information back to the parent when finished.

Also, a v4 skill consumer, the **v4-root-bot**, demonstrates how to consume the skills and allows you to test them.

To use the skill consumer to test the skills, all 3 bots need to be running at the same time. The bots can be tested locally using the Bot Framework Emulator, with each bot using a different local port.

## Create Azure resources for the bots

Bot-to-bot authentication requires that each participating bot has a valid app ID and password.

1. Create a Bot Channels Registration for the bots as needed.
1. Record the app ID and password for each one.

## Conversion process

To convert an existing bot to a skill bot takes just a few steps, as outlined in the next couple sections. For more in-depth information, see [about skills](../skills-conceptual.md).

- Update the bot's `.env` file to set the bot's app ID and password and to add a _root bot app ID_ property.
- Add claims validation. This will restrict access to the skill so that only users or your root bot can access the skill. See the [additional information](#additional-information) section for more information about default and custom claims validation.
- Modify the bot's messages controller to handle `endOfConversation` activities from the root bot.
- Modify the bot code to return an `endOfConversation` activity when the skill completes.
- Whenever the skill completes, if it has conversation state or maintains resources, it should clear its conversation state and release resources.
- Optionally add a manifest file.
  Since a skill consumer does not necessarily have access to the skill code, use a skill manifest to describe the activities the skill can receive and generate, its input and output parameters, and the skill's endpoints.
  The current manifest schema is [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Convert the echo bot

See [Skills/v3-skill-bot](https://aka.ms/v3-js-echo-skill) for an example of a v3 echo bot that has been converted to a basic skill.

1. Create a simple JavaScript v3 bot project and import required modules.

   **v3-skill-bot/app.js**

   [!code-javascript[require statements](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=5-7)]

1. Set the bot to run locally on port 3979.

   **v3-skill-bot/app.js**

   [!code-javascript[Setup server and port](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=9-13)]

1. In the configuration file, add the echo bot's app ID and password. Also, add a `ROOT_BOT_APP_ID` property with the simple root bot's app ID as its value.

   **v3-skill-bot/.env**

   [!code[.env file](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/.env)]

1. Create the chat connector for the bot. This one uses the default authentication configuration. Set `enableSkills` to `true` to allow the bot to be used as a skill. `allowedCallers` is an array of the app IDs of the bots allowed to use this skill. If the first value of this array was '*', then any bot could use this skill.

   **v3-skill-bot/app.js**

   [!code-javascript[chat connector](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=24-30&highlight=5-6)]

1. Update the message handler to send an `endOfConversation` activity when the user chooses to end the skill.

   **v3-skill-bot/app.js**

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/app.js?range=35-46&highlight=6)]

1. Since this bot does not maintain conversation state and does not create any resources for the conversation, the bot does not need to handle any `endOfConversation` activities that it receives from the skill consumer.

1. Use this manifest for the echo bot. Set the endpoint app ID to the bot's app ID.

   **v3-skill-bot/manifest/v3-skill-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-skill-bot/manifest/v3-skill-bot-manifest.json?highlight=22)]

   For the skill-manifest schema, see [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Convert the booking bot

See [Skills/v3-booking-bot-skill](https://aka.ms/v3-js-booking-skill) for an example of a v3 booking bot that has been converted to a basic skill.
Before conversion, the bot was similar to the v3 [core-MultiDialogs](https://aka.ms/v3-js-core-multidialogs) sample.

1. Import required modules.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[require statements](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=2-7)]

1. Set the bot to run locally on port 3980.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[Setup server and port](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=9-13)]

1. In the configuration file, add the booking bot's app ID and password. Also, add a `ROOT_BOT_APP_ID` property with the simple root bot's app ID as its value.

   **v3-booking-bot-skill/.env**

   [!code[.env file](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/.env)]

1. Create the chat connector for the bot. This one uses a custom authentication configuration. Set `enableSkills` to `true` to allow the bot to be used as a skill. `authConfiguration` contains the custom authentication configuration object to use for authentication and claims validation.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[chat connector](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=18-24&highlight=5-6)]

   **v3-booking-bot-skill/allowedCallersClaimsValidator.js**

   This implements custom claims validation and throws an error if validation fails.

   [!code-javascript[custom claims validation](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/allowedCallersClaimsValidator.js?range=4-47&highlight=22,25,39,41)]

1. Update the message handler to send an `endOfConversation` activity when the skill ends. Note that `session.endConversation()` clears conversation state in addition to sending an `endOfConversation` activity.

   **v3-booking-bot-skill/app.js**

   Implement a helper function to set the `endOfConversation` activity's `code` and `value` properties and clear conversation state.
   If the bot managed any other resources for the conversation, you would release them here, too.

   [!code-javascript[endConversation function](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=122-134&highlight=12)]

   When the user completes the process, use the helper method to end the skill and return the user's collected data.

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=39-40)]

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=72-77&highlight=4)]

   If instead the user ends the process early, the helper method is still invoked.

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=86-101&highlight=9-10)]

   [!code-javascript[universal bot](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=107-108)]

1. If the skill is cancelled by the skill consumer, the consumer sends an `endOfConversation` activity. Handle this activity and release any resources associated with the conversation.

   **v3-booking-bot-skill/app.js**

   [!code-javascript[endConversation function](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/app.js?range=110-115)]

1. Use this manifest for the booking bot. Set the endpoint app ID to the bot's app ID.

   **v3-booking-bot-skill/manifest/v3-booking-bot-skill-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v3-booking-bot-skill/manifest/v3-booking-bot-skill-manifest.json?highlight=22)]

   For the skill-manifest schema, see [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Create the v4 root bot

The simple root bot consumes the 2 skills and lets you verify that the conversion steps worked as planned. This bot will run locally on port 3978.

1. To the configuration file, add the root bot's app ID and password. For each of the v3 skills, add the skill's app ID.

   **v4-root-bot/.env**

   [!code-json[configuration](~/../botbuilder-samples/MigrationV3V4/Node/Skills/v4-root-bot/.env?highlight=2-3,6,10)]

## Test the root bot

Download and install the latest [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).

1. Start all three bots locally on your machine.
1. Use the Emulator to connect to the root bot.
1. Test the skills and skill consumer.

## Additional information

### Bot-to-bot authentication

The root and skill communicate over HTTP. The framework uses bearer tokens and bot application IDs to verify the identity of each bot. It uses an authentication configuration object to validate the authentication header on incoming requests. You can add a claims validator to the authentication configuration. The claims are evaluated after the authentication header. Your validation code should throw an error or exception to reject the request.

When creating a chat connector, include either an `allowedCallers` or an `authConfiguration` property in the settings parameter to enable bot-to-bot authentication.

The default claims validator for the chat connector uses the `allowedCallers` property. Its value should be an array of the application IDs of the bots that are allowed to call the skill. Set the first element to '*' to allow all bots to call the skill.

To use a custom claims validation function, set the `authConfiguration` field to your validation function. This function should accept an array of claim objects and throw an error if validation fails. Step 4 of the [convert the booking bot](#convert-the-booking-bot) section has an example claims validator.
