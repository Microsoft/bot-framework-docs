---
title: Convert a .NET v3 bot to a skill
description: Shows how to convert existing .NET v3 bots to skills and consume them from a .NET v4 bot.
keywords: .net, bot migration, skills, v3 bot
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2020
monikerRange: 'azure-bot-service-4.0'
---

# Convert a .NET v3 bot to a skill

This article describes how to convert 3 sample .NET v3 bots to skills and to create a v4 skill consumer that can access these skills.
To convert a JavaScript v3 bot to a skill, see how to [Convert a JavaScript v3 bot to a skill](javascript-v3-as-skill.md).
To migrate a .NET bot from v3 to v4, see how to [Migrate a .NET v3 bot to a .NET Framework v4 bot](conversion-framework.md).

## Prerequisites

- Visual Studio 2019.
- .NET Core 3.1.
- .NET Framework 4.6.1 or later.
- An Azure subscription. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.
- Copies of the v3 .NET sample bots to convert: an echo bot, the [**PizzaBot**](https://aka.ms/v3-cs-pizza-bot), and the [**SimpleSandwichBot**](https://aka.ms/v3-cs-simple-sandwich-bot).
- A copy of the v4 .NET sample skill consumer: [**SimpleRootBot**](https://aka.ms/cs-simple-root-bot).

## About the bots

In this article, each v3 bot is updated to act as a skill. A v4 skill consumer is included, so that you can test the converted bots as skills.

- The **EchoBot** echoes back messages it receives. As a skill, it _completes_ when it receives an "end" or "stop" message.
  The bot to convert is based on the v3 Bot Builder Echo Bot project template.
- The **PizzaBot** walks a user through ordering a pizza. As a skill, it sends the user's order back to the parent when finished.
- The **SimpleSandwichBot** walks a user through ordering a sandwich. As a skill, it sends the user's order back to the parent when finished.

Also, a v4 skill consumer, the **SimpleRootBot**, demonstrates how to consume the skills and allows you to test them.

To use the skill consumer to test the skills, all 4 bots need to be running at the same time. The bots can be tested locally using the Bot Framework Emulator, with each bot using a different local port.

## Create Azure resources for the bots

Bot-to-bot authentication requires that each participating bot has a valid app ID and password.

1. Create a Bot Channels Registration for the bots as needed.
1. Record the app ID and password for each one.

## Conversion process

To convert an existing bot to a skill bot takes just a few steps, as outlined in the next couple sections. For more in-depth information, see [about skills](../skills-conceptual.md).

- Update the bot's `web.config` file to set the bot's app ID and password and to add an _allowed callers_ property.
- Add claims validation. This will restrict access to the skill so that only users or your root bot can access the skill. See the [additional information](#additional-information) section for more information about default and custom claims validation.
- Modify the bot's messages controller to handle `endOfConversation` activities from the root bot.
- Modify the bot code to return an `endOfConversation` activity when the skill completes.
- Whenever the skill completes, if it has conversation state or maintains resources, it should clear its conversation state and release resources.
- Optionally add a manifest file.
  Since a skill consumer does not necessarily have access to the skill code, use a skill manifest to describe the activities the skill can receive and generate, its input and output parameters, and the skill's endpoints.
  The current manifest schema is [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Convert the echo bot

1. Create a new project from the v3 **Bot Builder Echo Bot** project template, and set it to use port 3979.

   1. Create the project.
   1. Open the project's properties.
   1. Select the **Web** category, and set the **Project Url** to `http://localhost:3979/`.
   1. Save your changes and close the properties tab.

1. To the configuration file, add the echo bot's app ID and password. Also in app settings, add an `EchoBotAllowedCallers` property and add the simple root bot's app ID to its value.

   **V3EchoBot\\Web.config**

   [!code-xml[app settings](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Web.config?range=11-16)]

1. Add a custom claims validator and a supporting authentication configuration class.

   **V3EchoBot\\Authentication\\CustomAllowedCallersClaimsValidator.cs**

   This implements custom claims validation and throws an `UnauthorizedAccessException` if validation fails.

   [!code-csharp[claims validator](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Authentication/CustomAllowedCallersClaimsValidator.cs?range=4-72&highlight=45,63,66)]

   **V3EchoBot\\Authentication\\CustomSkillAuthenticationConfiguration.cs**

   This loads the allowed callers information from the configuration file and uses the `CustomAllowedCallersClaimsValidator` for claims validation.

   [!code-csharp[allowed callers](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Authentication/CustomSkillAuthenticationConfiguration.cs?range=4-20&highlight=12-14)]

1. Update the `MessagesController` class.

   **V3EchoBot\\Controllers\\MessagesController.cs**

   Update the using statements.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Controllers/MessagesController.cs?range=4-15)]

   Update the class attribute from `BotAuthentication` to `SkillBotAuthentication`. Use the optional `AuthenticationConfigurationProviderType` parameter to use the custom claims validation provider.

   [!code-csharp[attribute](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Controllers/MessagesController.cs?range=19-21)]

   In the `HandleSystemMessage` method, add a condition to handle an `endOfConversation` message. This allows the skill to clear state and release resources when the conversation is ended from the skill consumer.

   [!code-csharp[on end of conversation](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Controllers/MessagesController.cs?range=45-65)]

1. Modify the bot code to allow the skill to flag that the conversation is complete when it receives an "end" or "stop" message from the user. The skill should also clear state and release resources when it ends the conversation.

   **V3EchoBot\\Dialogs\\RootDialog.cs**

   [!code-csharp[message received](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/Dialogs/RootDialog.cs?range=21-41&highlight=5-13)]

1. Use this manifest for the echo bot. Set the endpoint app ID to the bot's app ID.

   **V3EchoBot\\wwwroot\\echo-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3EchoBot/wwwroot/echo-bot-manifest.json?highlight=22)]

   For the skill-manifest schema, see [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Convert the pizza bot

1. Open your copy of the PizzaBot project, and set it to use port 3980.

   1. Open the project's properties.
   1. Select the **Web** category, and set the **Project Url** to `http://localhost:3980/`.
   1. Save your changes and close the properties tab.

1. To the configuration file, add the pizza bot's app ID and password. Also in app settings, add an `AllowedCallers` property and add the simple root bot's app ID to its value.

   **V3PizzaBot\\Web.config**

   [!code-xml[app settings](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Web.config?range=11-16)]

1. Add a `ConversationHelper` class that has helper methods to
   - Send the `endOfConversation` activity when the skill ends. This can return the order information in the activity's `Value` property and set the `Code` property to reflect why the conversation ended.
   - Clear conversation state and release any associated resources.

   **V3PizzaBot\\ConversationHelper.cs**

   [!code-csharp[conversation helper](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/ConversationHelper.cs?range=4-79)]

1. Update the `MessagesController` class.

   **V3PizzaBot\\Controllers\\MessagesController.cs**

   Update the using statements.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Controllers/MessagesController.cs?range=4-16)]

   Update the class attribute from `BotAuthentication` to `SkillBotAuthentication`. This bot uses the default claims validator.

   [!code-csharp[attribute](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Controllers/MessagesController.cs?range=20-21)]

   In the `Post` method, modify the `message` activity condition to allow the user to cancel their ordering process from within the skill. Also, add an `endOfConversation` activity condition to allow the skill to clear state and release resources when the conversation is ended from the skill consumer.

   [!code-csharp[on end of conversation](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/Controllers/MessagesController.cs?range=69-87)]

1. Modify the bot code.

   **V3PizzaBot\\PizzaOrderDialog.cs**

   Update the using statements.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/PizzaOrderDialog.cs?range=4-14)]

   Add a welcome message. This will help the user know what's going on when the root bot invokes the pizza bot as a skill.

   [!code-csharp[start form](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/PizzaOrderDialog.cs?range=29-35)]

   Modify the bot code to allow the skill to flag that the conversation is complete when the user cancels or completes their order. The skill should also clear state and release resources when it ends the conversation.

   [!code-csharp[form complete](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/PizzaOrderDialog.cs?range=76-104)]

1. Use this manifest for the pizza bot. Set the endpoint app ID to the bot's app ID.

   **V3PizzaBot\\wwwroot\\pizza-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3PizzaBot/wwwroot/pizza-bot-manifest.json?highlight=22)]

   For the skill-manifest schema, see [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Convert the sandwich bot

1. Open your copy of the SimpleSandwichBot project, and set it to use port 3981.

   1. Open the project's properties.
   1. Select the **Web** category, and set the **Project Url** to `http://localhost:3981/`.
   1. Save your changes and close the properties tab.

1. To the configuration file, add the pizza bot's app ID and password. Also in app settings, add an `AllowedCallers` property and add the simple root bot's app ID to its value.

   **V3SimpleSandwichBot\\Web.config**

   [!code-xml[app settings](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Web.config?range=11-16)]

1. Add a `ConversationHelper` class that has helper methods to
   - Send the `endOfConversation` activity when the skill ends. This can return the order information in the activity's `Value` property and set the `Code` property to reflect why the conversation ended.
   - Clear conversation state and release any associated resources.

   **V3SimpleSandwichBot\\ConversationHelper.cs**

   [!code-csharp[conversation helper](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/ConversationHelper.cs?range=4-79)]

1. Update the `MessagesController` class.

   **V3SimpleSandwichBot\\Controllers\\MessagesController.cs**

   Update the using statements.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Controllers/MessagesController.cs?range=4-17)]

   Update the class attribute from `BotAuthentication` to `SkillBotAuthentication`. This bot uses the default claims validator.

   [!code-csharp[attribute](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Controllers/MessagesController.cs?range=21-22)]

   In the `Post` method, modify the `message` activity condition to allow the user to cancel their ordering process from within the skill. Also, add an `endOfConversation` activity condition to allow the skill to clear state and release resources when the conversation is ended from the skill consumer.

   [!code-csharp[on end of conversation](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Controllers/MessagesController.cs?range=42-60)]

1. Modify the sandwich form.

   **V3SimpleSandwichBot\\Sandwich.cs**

   Update the using statements.

   [!code-csharp[using statements](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Sandwich.cs?range=4-8)]

   Modify the form's `BuildForm` method to allow the skill to flag that the conversation is complete.

   [!code-csharp[form complete](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/Sandwich.cs?range=47-54&highlight=6)]

1. Use this manifest for the sandwich bot. Set the endpoint app ID to the bot's app ID.

   **V3SimpleSandwichBot\\wwwroot\\sandwich-bot-manifest.json**

   [!code-json[manifest](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V3SimpleSandwichBot/wwwroot/sandwich-bot-manifest.json?highlight=22)]

   For the skill-manifest schema, see [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Create the v4 root bot

The simple root bot consumes the 3 skills and lets you verify that the conversion steps worked as planned. This bot will run locally on port 3978.

1. To the configuration file, add the root bot's app ID and password. For each of the v3 skills, add the skill's app ID.

   **V4SimpleRootBot\\appsettings.json**

   [!code-json[configuration](~/../botbuilder-samples/MigrationV3V4/CSharp/Skills/V4SimpleRootBot/appsettings.json?highlight=2-3,8,13,18)]

## Test the root bot

Download and install the latest [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme).

1. Build and run all four bots locally on your machine.
1. Use the Emulator to connect to the root bot.
1. Test the skills and skill consumer.

## Additional information

### Bot-to-bot authentication

The root and skill communicate over HTTP. The framework uses bearer tokens and bot application IDs to verify the identity of each bot. It uses an authentication configuration object to validate the authentication header on incoming requests. You can add a claims validator to the authentication configuration. The claims are evaluated after the authentication header. Your validation code should throw an error or exception to reject the request.

The default claims validator reads the `AllowedCallers` application setting from the bot's configuration file. This setting should contain a comma separated list of the application IDs of the bots that are allowed to call the skill, or "*" to allow all bots to call the skill.

To implement a custom claims validator, implement classes that derive from `AuthenticationConfiguration` and `ClaimsValidator` and then reference the derived authentication configuration in the `SkillBotAuthentication` attribute. Steps 3 and 4 of the [convert the echo bot](#convert-the-echo-bot) section has example claims validation classes.
