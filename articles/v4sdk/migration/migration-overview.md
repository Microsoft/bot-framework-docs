---
title: Bot Framework SDK Migration overview - Bot Service
description: Provides an overview of the changes to the SDK and how to migrate from v3 to v4.
keywords: bot migration
author:  mmiele
ms.author: v-mimiel
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 06/11/2019
monikerRange: 'azure-bot-service-4.0'
---

# Migration overview

The Bot Framework SDK v4 builds on the customers' feedback and learning experience from previous SDKs. The new version introduces the right levels of abstraction while enabling a flexible  architecture of the bot components. This, for example, allows you to create a simple bot and then expand it in sophistication using the modularity and extensibility of the Bot Framework SDK v4.

> [!NOTE]
> The Bot Framework SDK v4 strives to keep simple things simple and makes complex things possible.

An open approach has been adopted so that the Bot Framework v4 SDK has been built with the cooperation from the community. When you first submit a Pull Request, a [Contributor License Agreement](https://cla.opensource.microsoft.com/) (CLA) automatically determines whether you need a license. You will only need to do this once across all repositories. Typically, there is a time interval to establish a set of objectives to achieve.

## What happens to bots built using SDK v3

The Bot Framework SDK v3 will be retired but existing V3 bot workloads will continue to run without interruption. For more information, see: [Bot Framework SDK Version 3 Lifetime Support](https://docs.microsoft.com/azure/bot-service/bot-service-resources-bot-framework-faq?view=azure-bot-service-4.0#bot-framework-sdk-version-3-lifetime-support).

We highly recommend that you start migrating your V3 bots to V4. In order to support this migration we have produced related documentation and will provide extended support for migration initiatives via standard channels.

If you can not migrate from a v3 bot to a v4 bot immediately, you may still want to take advantage of the additional functionality available in the v4 SDK. You can convert your v3 bot to a skill, and create a skill consumer bot based on the v4 SDK to pass messages to your v3 bot. For more information, see how to [convert a v3 bot to a skill](convert-to-skill-overview.md).

## Advantages

- Richer, flexible and open architecture: Enables more flexible conversations design
- Availability: Introduces additional scenarios with new channel capabilities
- Expanded Subject Matter Expert (SME) personnel in the development cycle: New GUI designer permits non-developers to collaborate on conversation design
- Development velocity: New debugging and testing developer tools
- Performance insights: New telemetry features to evaluate and improve conversation quality
- Intelligence: Improved cognitive services capabilities

## Why migrate
<!-- [!] The declarative model introduced with Adaptive Dialogs would go great here (when ready).  -->
- Flexible and improved conversation management
  - Bot Adapter for activity processing
  - Refactored state management
  - New dialogs library
  - Middleware for composable and extensible designs: Clean and consistent hooks to customize behavior
- Built for .NET Core
  - Improved performance
  - Cross platform compatibility (Windows/Mac/Linux)
- Consistent programming model across multiple programming languages
- Improved documentation
- Bot Inspector provides extended debugging capabilities
- Virtual Assistant
  - Comprehensive solution that simplifies the creation of bots with basic conversational intents, Dispatch integration, QnA Maker, Application Insights and an automated deployment.
  - Extensible Skills. Compose conversational experiences by stitching together re-usable conversational capabilities, known as Skills.
- Testing Framework: Out of the box test capabilities with new transport independent adaptor architecture
- Telemetry: Gain key insights into your bot’s health and behavior with the Conversational AI Analytics
- Coming (preview)
  - Adaptive Dialogs: Build conversations that can be dynamically changed as the conversation progresses
  - Language Generation: Define multiple variations on a phrase
- Future
  - Declarative design permits level of abstraction for designers
  - GUI Dialog Designer
- Azure Bot Service 
  - Direct Line Speech Channel. Bringing together the Bot Framework and Microsoft's Speech Services. This provides a channel that enables streamed speech and text bi-directionally from the client to the bot application

## What's changed

The Bot Framework SDK v4 supports the same underlying Bot Framework Service as v3. However, v4 is a refactoring of the previous SDKs to allow more flexibility and control over a bot's creation. This includes the following:

- Introduced a bot adapter
  - The adapter is part of the activity processing stack
  - It handles Bot Framework authentication and initializes context for each turn
  - It manages incoming and outgoing traffic between a channel and your bot's turn handler, encapsulating the calls to the Bot Framework Connector service
  - For more details, see [How bots work](../bot-builder-basics.md)
- Refactored state management
  - State data is no longer automatically available within a bot
  - State is now managed via state management objects and property accessors
  - For more details, see [Managing state](../bot-builder-concept-state.md)
- Introduced new Dialogs library
  - v3 dialogs need to be rewritten for the new dialog library
  - For more details, see [Dialogs library](../bot-builder-concept-dialog.md)

## What's involved in migration work

- Update setup logic
- Port any critical user state
  - Note: sensitive user state cannot be kept in a bot state, instead store in separate storage under your control
- Port bot and dialog logic (see language-specific topics for more details)

### Migration estimation worksheet

The following worksheets can guide you in estimating your migration workload. In the **Occurrences** column replace *count* with your actual numeric value. In the **T Shirt** column enter values such as: *Small*, *Medium*, *Large* based on your estimation.

# [C#](#tab/csharp)

| Step | V3 | V4 | Occurrences | Complexity | T Shirt |
| -- | -- | -- | -- | -- | -- |
To get the incoming activity | IDialogContext.Activity | ITurnContext.Activity | count | Small  
To create and send an activity to the user | activity.CreateReply(“text”) IDialogContext.PostAsync | MessageFactory.Text(“text”) ITurnContext.SendActivityAsync | count | Small |
State management | UserData, ConversationData, and PrivateConversationData context.UserData.SetValue context.UserData.TryGetValue botDataStore.LoadAsyn | UserState, ConversationState, and PrivateConversationState  With property accessors | context.UserData.SetValue - count context.UserData.TryGetValue - count botDataStore.LoadAsyn - count | Medium to Large (See [user state management](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0#state-management) available) |
Handle the start of your dialog | Implement IDialog.StartAsync | Make this the first step of a waterfall dialog. | count | Small |  
Send an activity | IDialogContext.PostAsync. | Call ITurnContext.SendActivityAsync. | count | Small |  
Wait for a user's response | Use an IAwaitable<IMessageActivity>parameter and call IDialogContext.Wait | Return await ITurnContext.PromptAsync to begin a prompt dialog. Then retrieve the result in the next step of the waterfall. | count | Medium (depends on flow) |  
Handle continuation of your dialog | IDialogContext.Wait | Add additional steps to a waterfall dialog, or implement Dialog.ContinueDialogAsync | count | Large |  
Signal the end of processing until the user's next message | IDialogContext.Wait | Return Dialog.EndOfTurn. | count | Medium |  
Begin a child dialog | IDialogContext.Call | Return await the step context's BeginDialogAsyncmethod. If the child dialog returns a value, that value is available in the next step of the waterfall via the step context's Resultproperty. | count | Medium |  
Replace the current dialog with a new dialog | IDialogContext.Forward | Return await ITurnContext.ReplaceDialogAsync. | count | Large |  
Signal that the current dialog has completed | IDialogContext.Done | Return await the step context's EndDialogAsync method. | count | Medium |  
Fail out of a dialog. | IDialogContext.Fail | Throw an exception to be caught at another level of the bot, end the step with a status of Cancelled, or call the step or dialog context's CancelAllDialogsAsync. | count | Small |  

# [JavaScript](#tab/javascript)

| Step | V3 | V4 | Occurrences | Complexity | T Shirt |
| -- | -- | -- | -- | -- | -- |
To get the incoming activity | IMessage | TurnContext.activity | count | Small  
To create and send an activity to the user | Call Session.send('message') | Call TurnContext.sendActivity | count | Small |
State management | UserState & ConversationState UserState.get(), UserState.saveChanges(), ConversationState.get(), ConversationState.saveChanges() | UserState & ConversationState with property accessors | count | Medium to Large (See [user state management](https://docs.microsoft.com/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0#state-management) available) |
Handle the start of your dialog | call session.beginDialog, passing in the id of the dialog | call DialogContext.beginDialog | count | Small |  
Send an activity | Call Session.send | Call TurnContext.sendActivity | count | Small |  
Wait for a user's response | call a prompt from within the waterfall step, ex: builder.Prompts.text(session, 'Please enter your destination'). Retrieve the response in the next step. | Return await TurnContext.prompt to begin a prompt dialog. Then retrieve the result in the next step of the waterfall. | count | Medium (depends on flow) |  
Handle continuation of your dialog | Automatic | Add additional steps to a waterfall dialog, or implement Dialog.continueDialog | count | Large |  
Signal the end of processing until the user's next message | Session.endDialog | Return Dialog.EndOfTurn | count | Medium |  
Begin a child dialog | Session.beginDialog | Return await the step context's beginDialog method. If the child dialog returns a value, that value is available in the next step of the waterfall via the step context's Result property. | count | Medium |  
Replace the current dialog with a new dialog | Session.replaceDialog | ITurnContext.replaceDialog | count | Large |  
Signal that the current dialog has completed | Session.endDialog | Return await the step context's endDialog method. | count | Medium |  
Fail out of a dialog. | Session.pruneDialogStack | Throw an exception to be caught at another level of the bot, end the step with a status of Cancelled, or call the step or dialog context's cancelAllDialogs. | count | Small |  

---

# [C#](#tab/csharp)

The Bot Framework SDK v4 is based on the same underlying REST API as v3. However, v4 is a refactoring of the previous version of the SDK to allow more flexibility and control over the bots.

We recommend migrating to .NET Core, since the performance is very much improved. 
However, some existing V3 bots are using external libraries that do not have a .NET Core equivalent. In this case, the Bot Framework SDK v4 can be used with .NET Framework version 4.6.1 or higher. You can find an example at [corebot](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_webapi) location.

When migrating a project from v3 to v4, you can choose one of these options: convert in place for **.NET Framework** or port to a new project for **.NET Core**.

#### .NET Framework

- Update and install NuGet packages
- Update your Global.asax.cs file
- Update your MessagesController class
- Convert your dialogs

For more information, see [Migrate a .NET v3 bot to a .NET Framework v4 bot](conversion-framework.md).

#### .NET Core

- Create the new project using a template
- Install additional NuGet packages as necessary
- Personalize your bot, update your Startup.cs file, and update your controller class
- Update your bot class
- Copy over and update your dialogs and models

For more information, see [Migrate a .NET v3 bot to a .NET Core v4 bot](conversion-core.md).

# [JavaScript](#tab/javascript)

The **Bot Framework JavaScript SDK v4** introduces several fundamental changes on how bots are authored. These changes affect the syntax for developing bots in Javascript, especially around creating bot objects, defining dialogs, and coding event handling logic. The Bot Framework SDK v4 is based on the same underlying REST API as v3. However, v4 is a refactoring of the previous version of the SDK to allow more flexibility and control over the bots, in particular:

- Dialogs and bot instances have been further decoupled. In v3, dialogs were registered directly in the bot's constructor. In v4, you now pass dialogs into bot instances as arguments, providing greater compositional flexibility
- v4 provides an `ActivityHandler` class, which helps automate the handling of different types of activities, such as message, conversation update, and event activities
- For NodeJS bot migration, you need to create a new v4 NodeJS bot in either TypeScript or JavaScript. Recreate the bot logic using the new v4 constructs described in the related migration documentation

#### Migrate from v3 to v4

- Create the new project and add dependencies
- Update the entry point and define constants
- Create the dialogs, re-implementing them using the SDK v4
- Update the bot code to run the dialogs
- Port the `store.js` utility file

For more information, see [Migrate a SDK v3 Javascript bot to v4](conversion-javascript.md).

---

## Additional resources

The following additional resources provide more information that can help during the migration.  

### [C#](#tab/csharp)

<!-- _Mini-TOC with explainer for .NET topics_ -->
The following topics describe the differences between .NET v3 and v4 Bot Framework SDKs, the major changes between the two versions, and the steps to migrate a bot from v3 to v4.

| Topic | Description |
| :--- | :--- |
| [Differences between the v3 and v4 .NET SDK](migration-about.md) |Common differences between v3 and v4 SDKs |
| [.NET migration quick reference](net-migration-quickreference.md) |Major changes in v3 vs v4 SDKs |
| [Migrate a .NET v3 bot to a Framework v4 bot](conversion-framework.md) |Migrate a v3 to a v4 bot using the same project type |
| [Migrate a .NET v3 bot to a Core v4 bot](conversion-core.md) | Migrate a v3 to a v4 bot in a new .NET Core project|

### [JavaScript](#tab/javascript)

<!-- _Mini-TOC with explainer for JavaScript topics_ -->
The following topics describe the differences between JavaScript v3 and v4 Boto Framework SDKs, the major changes between the two versions, and the steps to migrate a bot from v3 to v4.

| Topic | Description |
| :--- | :--- |
| [Differences between the v3 and v4 JavaScript SDK](migration-about-javascript.md) | Common differences between v3 and v4 SDKs |
| [JavaScript migration quick reference](javascript-migration-quickreference.md)| Major changes in v3 vs v4 SDKs|
| [Migrate a JavaScript v3 bot to v4](conversion-javascript.md) |Migrate a v3 to a v4 bot |

---

### Code samples

The following are code samples you can use to learn the Bot Framework SDK V4 or jump start your project.

| Samples | Description |
| :--- | :--- |
| [Bot Framework Migration from V3 to V4 Samples](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4) <img width="200">| Migration samples from Bot Framework V3 SDK to V4 SDK |
| [Bot Builder .NET Samples](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore) | Bot builder C# .NET core samples |
| [Bot Builder JavaScript Samples](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs) | Bot builder JavaScript (node.js) samples |
| [Bot Builder All Samples](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples) | Bot builder all samples |

### Getting help

The following resources provide additional information and support for developing bots.

[Bot Framework additional resources](https://docs.microsoft.com/azure/bot-service/bot-service-resources-links-help?view=azure-bot-service-4.0)

### References

Please, refer to the following resources for more details and background information.

| Topic | Description |
| :--- | :--- |
| [What's new in Bot Framework](https://docs.microsoft.com/azure/bot-service/what-is-new?view=azure-bot-service-4.0) | Bot Framework and Azure Bot Service key features and improvements|
|[How bots work](../bot-builder-basics.md)|The internal mechanism of a bot|
|[Managing state](../bot-builder-concept-state.md)|Abstractions to make state management easier|
|[Dialogs library](../bot-builder-concept-dialog.md)| Central concepts to manage a conversation|
|[Send and receive text messages](../bot-builder-howto-send-messages.md)|Primary way a bot communicate with users|
|[Send Media](../bot-builder-howto-add-media-attachments.md)|Media attachments, such as images, video, audio, and files| 
|[Sequential conversation flow](../bot-builder-dialog-manage-conversation-flow.md)| Questioning as the main way a bot interacts with users|
|[Save user and conversation data](../bot-builder-howto-v4-state.md)|Tracking a conversation while stateless|
|[Complex Flow](../bot-builder-dialog-manage-complex-conversation-flow.md)|Manage complex conversation flows |
|[Reuse Dialogs](../bot-builder-compositcontrol.md)|Create independent dialogs to handle specific scenarios|
|[Interruptions](../bot-builder-howto-handle-user-interrupt.md)| Handling interruptions to create a robust bot|
|[Activity Schema](https://aka.ms/botSpecs-activitySchema)|Schema for humans and automated software|
