---
title: Send welcome message to users - Bot Service
description: Learn how to develop your bot to provide a welcoming user experience.
keywords: overview, develop, user experience, welcome, personalized experience, C#, JS, welcome message, bot, greet, greeting 
author: DanDev33
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/7/2020
monikerRange: 'azure-bot-service-4.0'
---
# Send welcome message to users

[!INCLUDE[applies-to](../includes/applies-to.md)]

The primary goal when creating any bot is to engage your user in a meaningful conversation. One of the best ways to achieve this goal is to ensure that from the moment a user first connects, they understand your bot’s main purpose and capabilities, the reason your bot was created. This article provides code examples to help you welcome users to your bot.

## Prerequisites

- Understand [bot basics](bot-builder-basics.md).
- A copy of the **Welcome user sample** in either [C# Sample](https://aka.ms/welcome-user-mvc), [JS Sample](https://aka.ms/bot-welcome-sample-js) or [Python Sample](https://aka.ms/bot-welcome-python-sample-code). The code from the sample is used to explain how to send welcome messages.

## About this sample code

This sample code shows how to detect and welcome new users when they are initially connected to your bot. The following diagram shows the logic flow for this bot.

### [C#](#tab/csharp)

The two main events encountered by the bot are:

- `OnMembersAddedAsync` which is called whenever a new user is connected to your bot
- `OnMessageActivityAsync` which is called whenever a new user input is received.

![welcome user logic flow](media/welcome-user-flow.png)

Whenever a new user is connected, they are provided with a `WelcomeMessage`, `InfoMessage`, and `PatternMessage` by the bot.
When a new user input is received, WelcomeUserState is checked to see if `DidBotWelcomeUser` is set to _true_. If not, an initial welcome user message is returned to the user.

### [JavaScript](#tab/javascript)

The two main events encountered by the bot are:

- `onMembersAdded` which is called whenever a new user is connected to your bot
- `onMessage` which is called whenever a new user input is received.

![welcome user logic flow](media/welcome-user-flow-js.png)

Whenever a new user is connected, they are provided with a `welcomeMessage`, `infoMessage`, and `patternMessage` by the bot.
When a new user input is received, `welcomedUserProperty` is checked to see if `didBotWelcomeUser` is set to _true_. If not, an initial welcome user message is returned to the user.
If `DidBotWelcomeUser` is _true_, the user's input is evaluated. Based on the content of the user's input this bot will do one of the following:

- Echo back a greeting received from the user.
- Display a hero card providing addition information about bots.
- Resend the `WelcomeMessage` explaining expected inputs for this bot.

### [Python](#tab/python)

The two main events encountered by the bot are:

- `on_members_added_activity` which is called whenever a new user is connected to your bot
- `on_message_activity` which is called whenever a new user input is received.

![welcome user logic flow](media/welcome-user-flow-python.png)

Whenever a new user is connected, they are provided with a *welcome message*, *information message*, and a *pattern message* by the bot.
When a new user input is received, the `welcome_user_state.did_welcome_user` property is checked to see if it is set to *true*. If it is not set to *true*, an initial welcome user message is returned to the user. If it is set to *true*, based on the content of the user's input this bot will do one of the following:

- Echo back a greeting received from the user.
- Display a hero card providing addition information about bots.

---

## Create user state

### [C#](#tab/csharp)

The user state object is created at startup and dependency injected into the bot constructor.

**Startup.cs**  
[!code-csharp[define state](~/../botBuilder-samples/samples/csharp_dotnetcore/03.welcome-user/Startup.cs?range=30-34)]

**Bots\WelcomeUserBot.cs**  
[!code-csharp[consume state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=41-47)]

### [JavaScript](#tab/javascript)

At startup, user state is defined in `index.js` and consumed by the bot constructor.

**index.js**  
[!code-javascript[define state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/index.js?range=51-55)]

**bots/welcomeBot.js**  
[!code-javascript[consume state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomeBot.js?range=16-22)]

### [Python](#tab/python)

At startup, user state is defined in `app.py` and consumed by the bot constructor.

**app.py**  
[!code-python[define state](~/../botbuilder-samples/samples/python/03.welcome-user/app.py?range=61-66)]

**bots/welcome-user-bot.py**  
[!code-python[consume state](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=23-29)]

---

## Create property accessors

### [C#](#tab/csharp)

We now create a property accessor that provides us a handle to `WelcomeUserState` inside the `OnMessageActivityAsync` method.
Then call the `GetAsync` method to get the properly scoped key. We then save user state data after each user input iteration using the `SaveChangesAsync` method.

**Bots\WelcomeUserBot.cs**  
[!code-csharp[Get state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-71)]
[!code-csharp[Save state](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range= 103-105)]

### [JavaScript](#tab/javascript)

We now create a property accessor that provides us a handle to welcomedUserProperty which is persisted within userState.

**bots/welcomeBot.js**  
[!code-javascript[Get state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=24-27)]
[!code-javascript[Save state](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=89-97)]

### [Python](#tab/python)

This sample creates a conversation state property accessor, `user_state_accessor`, in the bot constructor.

**bots/welcome-user-bot.py**  
[!code-python[constructor](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=31)]

It uses the property accessor in the `on_message_activity` handler and overrides the `on_turn` handler to save state before the end of the turn.

[!code-python[Get state](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=80-83)]
[!code-python[save state](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=39-43)]

---

## Detect and greet newly connected users

### [C#](#tab/csharp)

In **WelcomeUserBot**, we check for an activity update using `OnMembersAddedAsync()` to see if a new user has been added to the conversation and then send them a set of three initial welcome messages `WelcomeMessage`, `InfoMessage` and `PatternMessage`. Complete code for this interaction is shown below.

**Bots\WelcomeUserBot.cs**  
[!code-csharp[Define messages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=20-39)]
[!code-csharp[Send messages](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=55-66)]

### [JavaScript](#tab/javascript)

This JavaScript code sends initial welcome messages when a user is added. This is done by checking the conversation activity and verifying that a new member was added to the conversation.

**bots/welcomeBot.js**  
[!code-javascript[onMembersAdded](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=63-86)]

### [Python](#tab/python)

The `on_members_added_activity` checks to see if a new user has been added and then sends three initial welcome messages: a *welcome message*, an *information message* and a *pattern message*.

**bots/welcome-user-bot.py**  
[!code-python[on_members_added_activity](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=45-74)]

---

## Welcome new user and discard initial input

### [C#](#tab/csharp)

It is also important to consider when your user’s input might actually contain useful information, and this may vary for each channel. To ensure your user has a good experience on all possible channels, we check the status flag _didBotWelcomeUser_ and if this is "false", we do not process the initial user input. We instead provide the user with an initial welcome message. The bool _welcomedUserProperty_ is then set to "true", stored in UserState and our code will now process this user's input from all additional message activities.

**Bots\WelcomeUserBot.cs**  
[!code-csharp[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=68-84)]
[!code-csharp[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=101-105)]

### [JavaScript](#tab/javascript)

It is also important to consider when your user’s input might actually contain useful information, and this may vary for each channel. To ensure your user has a good experience on all possible channels, we check the didBotWelcomedUser property, if it does not exist, we set it to "false" and do not process the initial user input. We instead provide the user with an initial welcome message. The bool didBotWelcomeUser is then set to "true" and our code processes the user input from all additional message activities.

**bots/welcomeBot.js**  
[!code-javascript[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=24-39)]
[!code-javascript[DidBotWelcomeUser](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=57-61)]

### [Python](#tab/python)

It is also important to consider when the user’s input might actually contain useful information, this may vary for each channel. To ensure the user has a good experience on all possible channels, `on_message_activity` checks the `did_welcome_user` property. The first time, it sets  it to *false* and does not process the user input. Instead, it provides the user with an initial welcome message. Then it sets `did_welcome_user` to *true* and processes the user input from all additional message activities.

**bots/welcome-user-bot.py**  
[!code-python[DidBotWelcomeUser](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=85-95)]

---

## Process additional input

Once a new user has been welcomed, user input information is evaluated for each message turn and the bot provides a response based on the context of that user input. The following code shows the decision logic used to generate that response.

### [C#](#tab/csharp)

An input of 'intro' or 'help' calls the function `SendIntroCardAsync` to present the user with an informational hero card. That code is examined in the next section of this article.

**Bots\WelcomeUserBot.cs**  
[!code-csharp[SwitchOnUtterance](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=85-100)]

### [JavaScript](#tab/javascript)

An input of 'intro' or 'help' uses CardFactory to present the user with an Intro Adaptive Card. That code is examined in the next section of this article.

**bots/welcomeBot.js**  
[!code-javascript[SwitchOnUtterance](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=40-56)]

### [Python](#tab/python)

An user's input of *intro* or *help* causes the bot to call `__send_intro_card` which presents the user with an intro adaptive card.

**bots/welcome-user-bot.py**  
[!code-python[SwitchOnUtterance](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=101-106&highlight=97-106)]

---

## Using hero card greeting

As mentioned above, some user inputs generate a *Hero Card* in response to their request. You can learn more about hero card greetings here [Send an Intro Card](./bot-builder-howto-add-media-attachments.md). Below is the code required to create this bot's hero card response.

### [C#](#tab/csharp)

**Bots\WelcomeUserBot.cs**  
[!code-csharp[SendHeroCardGreeting](~/../BotBuilder-Samples/samples/csharp_dotnetcore/03.welcome-user/bots/WelcomeUserBot.cs?range=107-125)]

### [JavaScript](#tab/javascript)

**bots/welcomeBot.js**  
[!code-javascript[SendIntroCard](~/../BotBuilder-Samples/samples/javascript_nodejs/03.welcome-users/bots/welcomebot.js?range=99-124)]

### [Python](#tab/python)

**bots/welcome-user-bot.py**  
[!code-python[SendIntroCard](~/../botbuilder-samples/samples/python/03.welcome-user/bots/welcome_user_bot.py?range=108-143)]

---

## Test the bot

Download and install the latest [Bot Framework Emulator](https://aka.ms/bot-framework-emulator-readme)

1. Run the sample locally on your machine. If you need instructions, refer to the README file for [C# Sample](https://aka.ms/welcome-user-mvc) or [JS Sample](https://aka.ms/bot-welcome-sample-js).
1. Use the emulator to test the bot as shown below.

![test welcome bot sample](media/welcome-user-emulator-1.png)

Test hero card greeting.

![test welcome bot card](media/welcome-user-emulator-2.png)

## Additional Resources

Learn more about various media responses in [Add media to messages](./bot-builder-howto-add-media-attachments.md).

## Next steps

> [!div class="nextstepaction"]
> [Gather user input](bot-builder-prompts.md)
