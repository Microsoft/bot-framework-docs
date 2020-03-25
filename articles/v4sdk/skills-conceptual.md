---
title: About skills | Microsoft Docs
description: Describes how conversational logic in one bot can be used by another bot using the Bot Framework SDK.
keywords: bot skill, host bot, skill bot.
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 12/09/2019
monikerRange: 'azure-bot-service-4.0'
---

# About skills

[!INCLUDE [applies-to-v4](../includes/applies-to.md)]

<!-- Value prop: why skills -->
Starting with version 4.7 of the Bot Framework SDK, you can extend a bot using another bot (a skill).
A skill can be consumed by various other bots, facilitating reuse, and in this way, you can create a user-facing bot and extend it by consuming your own or 3rd party skills.

<!-- Terminology -->
- A _skill_ is a bot that can perform a set of tasks for another bot.
  Depending on design, a skill can also function as a typical, user-facing bot.
- A _skill consumer_ is a bot that can invoke one or more skills. With respect to skills, a _root bot_ is a user-facing bot that is also a skill consumer.
- A _skill manifest_ is a JSON file that describes the actions the skill can perform, its input and output parameters, and the skill's endpoints.
  - Developers who don't have access to the skill's source code can use the information in the manifest to design their skill consumer. See how to [implement a skill](./skill-implement-skill.md) for a sample skill manifest.
  - The _skill manifest schema_ is a JSON file that describes the schema of the skill manifest. The current version is [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

In other words, the user interacts directly with the root bot, and the root bot delegates some of its conversational logic to a skill.

<!-- Requirements/contract -->
The skills feature is designed so that:

- Skills can work with both the Bot Framework adapter and custom adapters.
- Skills can work with the Microsoft Teams channel, which makes heavy use of `invoke` activities.
- Skills support user authentication; however, user authentication is local to the skill or skill consumer and cannot be transferred to another bot.
- A skill consumer can consume multiple skills.
- A skill consumer can run multiple skills in parallel.
- A skill consumer can consume a skill regardless of the language or SDK version of the skill.
- The Bot Connector service provides bot-to-bot authentication; however, you can test a root bot locally using the Emulator.
- A skill can also be a skill consumer. Connecting through multiple skills will add network latency and the potential for error. Such bots are more complex and can be more difficult to debug.
<!--TBD: - Skills support proactive messaging. -->

## Architecture

A skill and skill consumer are separate bots, and you publish them independently.

- The skill and skill consumer communicate over HTTP.
- A skill consumer can invoke more than one skill.
- A skill can be invoked by more than one consumer.

A skill needs to include additional logic to send an `endOfConversation` activity when it completes, so that the skill consumer knows when to stop forwarding activities to the skill.

A root bot implements at least two HTTP endpoints, one for receiving activities from the user and one for receiving activities from skills. The skill consumer needs to pair code that receives the HTTP method request with a skill handler.
It requires added logic for managing a skill, such as when to invoke or cancel the skill, and so on. In addition to the usual bot and adapter objects, the consumer includes a few skill-related objects, used to exchange activities with the skill.

This diagram outlines the flow of activities from the user to the root bot to a skill and back again.

![Architecture diagram](./media/skills-conceptual-architecture.png)

1. The root bot's adapter receives activities from the user and forwards them to the root bot's activity handler.
1. The root bot uses a skill HTTP client to send an activity to the skill. It gets the consumer-skill conversation information from a skill definition and a skill conversation ID factory. This includes the service URL that the skill will use to reply to the activity.
1. The skill's adapter receives activities from the skill consumer and forwards them to the skill's activity handler.
1. When the skill responds, the root bot's skill handler receives the activity. It gets the root-user conversation information from the skill conversation ID factory. It then forwards the activity to the root bot's adapter.
1. The root bot's adapter internally generates a proactive message to resume the conversation with the user.
1. The root bot's adapter sends any messages from the skill to the user.

These objects help manage skills and route skill traffic:

- A _Bot Framework skill_ describes routing information for a skill and can be read from the skill consumer's configuration file.
- A _skill HTTP client_ sends activities to a skill.
- A _skill handler_ receives activities from a skill.
- The _skill conversation ID factory_ translates between the user-root conversation reference and the root-skill conversation reference.
- The Bot Connector service provides both channel and bot-to-bot authentication. Using an _authentication configuration_ object, you can add claims validation to a bot (skill or skill consumer) to limit which applications or users have access.

The skill client and skill handler objects both use the _conversation ID factory_ to translate between the conversation the root bot uses to interact with the user and the conversation the root bot uses to interact with the skill.

## Bot-to-bot communication

It's important to understand certain aspects of this design, independent of which bot you're designing.

<!--
- infrastructure concerns:
  - stateless, cross-server application (memory management and middleware).
  - authentication, in both directions, plus claims validation.
- implementation concerns:
  - classes, objects, and logic you need to add to your host (and skill).
  - when to start and stop a skill.
  - managing multiple skills.
-->

### Conversation references

The user-root conversation is different than the root-skill conversation.

The _conversation ID factory_ helps to manage traffic between a skill consumer and a skill. The factory translates between the ID of the conversation the root has with the user and the one it has with the skill.
In other words, it generates a conversation ID for use between the root and the skill, and recovers the original user-root conversation ID from the root-skill conversation ID.

<!-- Hopefully, this just gets folded into the SDK and does not need to get described in detail.
- The host needs to save or encode original conversation ID and service URL and create a conversation ID for use between it and the skill.
  - Generated conversation IDs must be usable as a URL path parameter.
  - A modified activity gets the new conversation ID and service URL (of the host, as the host provides a channel interface to the skill).
- Upon receiving an activity from the skill, host needs to decode or recover the original conversation ID and service URL so that the activity can get forwarded back to the user in the original conversation.
-->

### Cross-server coordination
<!-- or, Statelessness in the host -->

The root and skill communicate over HTTP.
So, the root instance that receives an activity from a skill may not be the same instance that sent the initiating activity; in other words, different servers may handler these two requests.

- Always save state in the skill consumer before forwarding an activity to a skill.
  This ensures that the instance that receives traffic from a skill can pick up where the previous instance left off before it invoked the skill.
- When the skill handler receives an activity from a skill, it translates it into a form appropriate for the skill consumer, and forwards it to the consumer's adapter.

### Skill consumer and skill state

The skill consumer and skill manage their own state separately. However, the consumer creates the conversation ID that it uses to communicate with the skill. This can have an effect on conversation state in the skill.

> [!IMPORTANT]
> As noted previously, when the skill consumer delegates a user activity to a skill, a different instance of the consumer may receive the skill response. The consumer should always save conversation state immediately before forwarding an activity to a skill.

### Bot-to-bot authentication

<!-- TODO Add appropriate info about this new(?) feature to the bot basics article. -->

Service-level authentication is managed by the Bot Connector service. The framework uses bearer tokens and bot application IDs to verify the identity of each bot.

The Bot Framework uses an _authentication configuration_ object to validate the authentication header on incoming requests.

#### Claims validation

You can add a _claims validator_ to the authentication configuration. The claims are evaluated after the authentication header. Your validation code should throw an error or exception to reject the request.

There are various reasons you might reject an otherwise authenticated request:

- When the skill consumer should accept traffic only from skills that it may have initiated a conversation with.
- When a skill is part of a paid-for service, and users not in the database should not have access.
- When you want to restrict access to the skill to specific skill consumers.

<!--TODO Need a link for more information about claims and claims-based validation.-->

## Skill consumers

<!--
### Skill consumer design
Supports: Bot Framework adapter, custom adapters, MS Teams channel (what Teams calls "proactive" 1-1 conversations, created off a group/channel/team conversation)
-->

From the user's perspective, the root bot is the bot they are interacting with.
From the skill's perspective, the skill consumer is the channel over which it communicates with the user.

As a skill consumer, a root bot includes some additional logic to manage traffic between it and a skill:

- Configuration information for each skill the root uses.
- A _conversation ID factory_ that allows the root to switch back and forth between the conversation it is having with the user and the one it is having with a skill.
- A _skill client_ that can package and forward activities to a skill bot.
- A _skill handler_ that can receive requests and unpack activities from a skill bot.

### Managing skills

Starting and letting a single skill run to completion is managed with a few additions to the host. More complicated scenarios are possible, with multiple skills or conversation threads.

#### Skill descriptions

For each skill, add a _Bot Framework skill_ object to the skill consumer's configuration file. Each one will have an ID, an app ID, and the endpoint for the skill.

| Property | Description
| :--- | :--- |
| _ID_ | The ID or key of the skill, specific to the skill consumer. |
| _App ID_ | The `appId` assigned to the bot resource when the skill was registered on Azure. |
| _Skill endpoint_ | The messaging endpoint for the skill. This is the URL the consumer will use to communicate with the skill. |

#### Skill client and skill handler

<!-- Is this still accurate? -->
The skill consumer uses a skill client to send activities to a skill. The client:

- Takes an activity to forward to the skill, either from a user or generated by the consumer.
- Replaces the original conversation reference with one for the consumer-skill conversation.
- Adds a bot-to-bot authentication token.
- Sends the updated activity to the skill.

The skill consumer uses a skill handler to receive activities from a skill. The handler:

- Handles the channel service `SendToConversation` and `ReplyToActivity` REST API methods.
- Enforces authentication and claims validation.
- Retrieves the original conversation reference.
- Generates an activity for the consumer's adapter. This activity will either signal that the skill has completed or be an activity to forward to the user.

<!-- These section should probably reference a how-to. -->
#### Managing a skill from a skill consumer

You need to add logic to your skill consumer to track any active skills.
It is up to the consumer as to how it manages skills in general, whether it can maintain multiple active skills in parallel or not, and so on.
Specific scenarios to consider include:

- Initiating a new consumer-skill conversation. (This will be associated with a specific consumer-user conversation.)
  - To pass parameters to a skill, set the _value_ property in the initial activity to the skill.
- Continuing an existing consumer-skill conversation.
- Recognizing an `endOfConversation` activity from the skill as signalling an end of a consumer-skill conversation.
  - To retrieve any return value from a skill, check the activity's _value_ property.
  - To check why the skill is ending, check the activity's _code_ parameter, which could indicate that the skill encountered an error.
- Cancelling a skill from the consumer by sending an `endOfConversation` activity to the skill.

#### Invoking a skill from a dialog

If you are using the [dialogs library](bot-builder-concept-dialog.md), you can use a _skill dialog_ to manage a skill. While the skill dialog is the active dialog, it will forward activities to the associated skill.
<!--Language-specific Notes:
- C#: SkillDialog, SkillDialogOptions, SkillDialogArgs
  - public SkillDialog(SkillDialogOptions dialogOptions, string dialogId = null)
- JS: SkillDialog, SkillDialogOptions, BeginSkillDialogOptions
  - public constructor(dialogOptions: SkillDialogOptions, dialogId?: string)
- Py (WIP): SkillDialog, SkillDialogOptions, SkillDialogArgs
  - def __init__(self, dialog_options: SkillDialogOptions, conversation_state: ConversationState)
-->

- When you create the skill dialog, use the _dialog options_ parameter to provide all the information the dialog needs to manage the skill, such as the consumer's app ID and callback URL, the conversation ID factory to use, the skill's properties, and so on.
  - If you want to manage more than one skill as a dialog, you will need to create a separate skill dialog for each skill.
  - Often, you will add the skill dialog to a component dialog.
- To start the skill dialog, use the dialog context's _begin_ method and provide the skill dialog's ID. Use the _options_ parameter to provide the activity the consumer will send as the first activity to the skill.
- You can cancel or interrupt the skill dialog as you would any other dialog. See how to [handle user interruptions](bot-builder-howto-handle-user-interrupt.md) for an example.

## Skill bots

With minor modifications, any bot can act as a skill. Skill bots:

- Implement access restrictions via a claims validator.
- As appropriate, check for initialization parameters in the initial activity's _value_ property.
- Send messages to the user as normal, via `message` activities.
- Signal skill completion or cancellation via an `endOfConversation` activity.
  - Provide the return value, if any, in the activity's _value_ property.
  - Provide an error code, if any, in the activity's _code_ property.

### Skill activities

 Some skills can perform a variety of tasks or _activities_. For example, a to-do skill might allow create, update, view, and delete activities that can be accessed as discrete conversations. <!--TODO Flesh this out-->

### Skill manifest

Since a skill consumer does not necessarily have access to the skill code, use a skill manifest to describe the activities the skill can receive and generate, its input and output parameters, and the skill's endpoints. For the skill-manifest schema, see [skill-manifest-2.0.0.json](https://github.com/microsoft/botframework-sdk/blob/master/schemas/skills/skill-manifest-2.0.0.json).

## Additional information

For more on how to implement skill and skill consumer bots, see how to [implement a skill](skill-implement-skill.md) and [implement a skill consumer](skill-implement-consumer.md).

<!--
You publish skill and skill consumer bots separately. For more information, see _TBD: link to create a skills bot from scratch how-to or tutorial_.

For more about skill manifests, see the _TBD: creating a manifest section of implementing a skill bot_.
-->
