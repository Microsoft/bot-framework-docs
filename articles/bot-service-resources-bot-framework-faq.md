---
title: Bot Service Frequently Asked Questions - Bot Service
description: A list of Frequently Asked Questions about elements of the Bot Framework and when new features will become available.
author: scheyal
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 02/19/2020
---

# Bot Framework Frequently Asked Questions

This article contains answers to some frequently asked questions about the Bot Framework.

## Background and availability

### Why did Microsoft develop the Bot Framework?

We created the Bot Framework to make it easier for developers to build and connect great bots to users, wherever they converse, including on Microsoft's premier channels.

### What is the v4 SDK?
Bot Framework v4 SDK builds on the feedback and learnings from the prior Bot Framework SDKs. It introduces the right levels of abstraction while enabling rich componentization of the bot building blocks. You can start with a simple bot and grow your bot in sophistication using a modular and extensible framework. You can find [FAQ](https://github.com/Microsoft/botbuilder-dotnet/wiki/FAQ) for the SDK on GitHub.

### Running a bot offline

<!-- WIP -->
Before talking about the use of a bot offline, meaning a bot not deployed on Azure or on some other host services but on premises, let's clarify a few points.

- A bot is a web service that does not have a UI, so the user must interact with it via other means, in the form of channels, which use the [Bot Framework Service](rest-api/bot-framework-rest-connector-concepts.md). The connector functions as a *proxy* to relay messages between a client and the bot.
- The **connector** is a global application hosted on Azure nodes and spread geographically for availability and scalability. 
- You use the [Bot Channel Registration](bot-service-quickstart-registration.md) to register the bot with the connector.
    >[!NOTE]
    > The bot must have its endpoint publicly reachable by the connector.

You can run a bot offline with limited capabilities. For example, if you want to use a bot offline that has LUIS capability, you must build a container for the bot, and required tools, and a container for LUIS. Both connected via Docker Compose bridged network.

This is a "partial" offline solution because a Cognitive Services container needs periodic online connection.

> [!NOTE]
> The QnA service is not supported in a bot running offline.

For more information, see:

- [Deploy the Language Understanding (LUIS) container to Azure Container Instances](https://docs.microsoft.com/azure/cognitive-services/luis/deploy-luis-on-container-instances)
- [Container support in Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support)

## Bot Framework SDK Version 3 Lifetime Support and Deprecation Notice
Microsoft Bot Framework SDK V4 was released in September 2018, and since then we have shipped a few dot-release improvements. As announced previously, the V3 SDK is being retired. Accordingly, there will be no more development in V3 repositories. **Existing V3 bot workloads will continue to run without interruption. We have no plans to disrupt any running workloads**.

As mentioned, Bot Builder SDK V3 bots continue to run and be supported by Azure Bot Service. Bot Builder SDK V3 will only be supported  for critical security bug fixes, connector, and protocol layer compatibility updates.  

All new features and capabilities are developed exclusively on [Bot Framework SDK V4](https://github.com/microsoft/botframework-sdk).  Customers are encouraged to migrate their bots to V4 as soon as possible.

We highly recommend that you start migrating your V3 bots to V4. In order to support this migration we have produced migration documentation and will provide extended support for migration initiatives (via standard channels such as Stack Overflow and Microsoft Customer Support).


For more information please refer to the following references:
* [Essential Migration Guidance](https://aka.ms/bf-migration-overview)
* Primary V4 Repositories to develop Bot Framework bots
  * [Botbuilder for dotnet](https://github.com/microsoft/botbuilder-dotnet)
  * [Botbuilder for JS](https://github.com/microsoft/botbuilder-js) 
* QnA Maker Libraries were replaced with the following V4 libraries:
  * [Libraries for dotnet](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.AI.QnA)
  * [Libraries for JS](https://github.com/Microsoft/botbuilder-js/blob/master/libraries/botbuilder-ai/src/qnaMaker.ts)
* Azure Libraries were replaced with the following V4 libraries:
  * [Botbuilder for JS Azure](https://github.com/Microsoft/botbuilder-js/tree/master/libraries/botbuilder-azure)
  * [Botbuilder for dotnet Azure](https://github.com/Microsoft/botbuilder-dotnet/tree/master/libraries/Microsoft.Bot.Builder.Azure)

### V3 Status Summary

#### ABS Service
1.	The ABS service side will continue to support running V3 bots with no planned end of life and any running bots will not be disrupted. 
2.	Channels will remain compatible with V3 with no disruption or end of life plan.
3.	Creation of new V3 bots is disabled on the portal; however, expert users who wish to deploy their V3 bots independently, not on ABS (e.g. as webapp service) can do so.

#### SDK and Tools
1.	We are not investing in V3 from SDK side, and will only apply critical security fixes to the SDK branches for the foreseeable future (Exception: We plan to add a Skills connector to allow V4 bots to call legacy V3 bots).
2.	SDKs and tools development is exclusively on V4 with no V3 work done or planned (hence we’re already “there”).
3.	We do not prevent anyone from running old tools to manage their V3 bots. 


## How can I migrate Azure Bot Service from one region to another?

Azure Bot Service does not support region move. It’s a global service that is not tied to any specific region.

## Channels
### When will you add more conversation experiences to the Bot Framework?

We plan on making continuous improvements to the Bot Framework, including additional channels, but cannot provide a schedule at this time.  
If you would like a specific channel added to the framework, [let us know][Support].

### I have a communication channel I’d like to be configurable with Bot Framework. Can I work with Microsoft to do that?

We have not provided a general mechanism for developers to add new channels to Bot Framework, but you can connect your bot to your app via the [Direct Line API][DirectLineAPI]. If you are a developer of a communication channel and would like to work with us to enable your channel in the Bot Framework [we’d love to hear from you][Support].

### If I want to create a bot for Microsoft Teams, what tools and services should I use?

The Bot Framework is designed to build, connect, and deploy high quality, responsive, performant and scalable bots for Teams and many other channels. The SDK can be used to create text/sms, image, button and card-capable bots (which constitute the majority of bot interactions today across conversation experiences) as well as bot interactions which are Teams-specific such as rich audio and video experiences.

If you already have a great bot and would like to reach the Teams audience, your bot can easily be connected to Teams (or any supported channel) via the Bot Framework for REST API (provided it has an internet-accessible REST endpoint).

### How do I create a bot that uses the US Government data center?

There are 2 major steps required to create a bot that uses a US Government data center.
1. Add a “channel provider” setting in your appsettings.json (or the App Service Settings). This needs to be specifically set to this name/value constant: ChannelService = "https://botframework.azure.us". An example using appsetting.json is shown below.

```json
{
  "MicrosoftAppId": "", 
  "MicrosoftAppPassword": "",
  "ChannelService": "https://botframework.azure.us"
}
```
2. If you are using .NET core, you will need to add a ConfigurationChannelProvider in your startup.cs file. How you do this varies based on which version of the SDK you are using.

- For versions 4.3 and above, in your ConfigureServices method, you need to create a ConfigurationChannelProvider instance. When using the BotFrameworkHttpAdapter class, you inject this as singleton into the service collection like this:

```csharp  
services.AddSingleton<IChannelProvider, ConfigurationChannelProvider>();
```
- For versions prior to 4.3, in your ConfigureServices method, find the AddBot method. When setting the options, make sure you add:

```csharp
options.ChannelProvider = new ConfigurationChannelProvider();
```
You can find more information concerning Govenment Services [here](https://docs.microsoft.com/azure/azure-government/documentation-government-services-aiandcognitiveservices#azure-bot-service)

## Security and Privacy
### Do the bots registered with the Bot Framework collect personal information? If yes, how can I be sure the data is safe and secure? What about privacy?

Each bot is its own service, and developers of these services are required to provide Terms of Service and Privacy Statements per their Developer Code of Conduct. For more information, see [bot review guidelines](https://docs.microsoft.com/azure/bot-service/bot-service-review-guidelines?view=azure-bot-service-4.0).

### Can I host my bot on my own servers?
Yes. Your bot can be hosted anywhere on the Internet. On your own servers, in Azure, or in any other datacenter. The only requirement is that the bot must expose a publicly-accessible HTTPS endpoint.

### How do you ban or remove bots from the service?

Users have a way to report a misbehaving bot via the bot’s contact card in the directory. Developers must abide by Microsoft terms of service to participate in the service.

### Which specific URLs do I need to whitelist in my corporate firewall to access Bot Framework services?
If you have an outbound firewall blocking traffic from your bot to the Internet, you'll need to whitelist the following URLs in that firewall:
- login.botframework.com (Bot authentication)
- login.microsoftonline.com (Bot authentication)
- westus.api.cognitive.microsoft.com (for Luis.ai NLP integration)
- state.botframework.com (Bot state storage for prototyping)
- cortanabfchanneleastus.azurewebsites.net (Cortana channel)
- cortanabfchannelwestus.azurewebsites.net (Cortana Channel)
- *.botframework.com (channels)
- Additional URLs for specific Bot Framework channels

> [!NOTE] 
> You may use `<channel>.botframework.com` if you’d prefer not to whitelist a URL with an asterisk. `<channel>` is equal to every channel your bot uses such as `directline.botframework.com`, `webchat.botframework.com`, and `slack.botframework.com`. It is also worthwhile to watch traffic over your firewall while testing the bot to make sure nothing else is getting blocked.

### Can I block all traffic to my bot except traffic from the Bot Framework Service?
Bot Framework Services are hosted in Azure datacenters world-wide and the list of Azure IPs is constantly changing. Whitelisting certain IP addresses may work one day and break the next as the Azure IP Addresses change.
 
### Which RBAC role is required to create and deploy a bot?

Creating a bot in the Azure portal requires Contributor access either in the subscription or in a specific resource group. A user with the *Contributor* role in a resource group can create a new bot in that specific resource group. A user in the *Contributor* role for a subscription can create a bot in a new or existing resource group.

Using the Azure CLI, a role-based access control approach can support custom roles. If you want to make a custom role with more constrained permissions, the following set will allow the user to create and deploy a bot that also supports LUIS, QnA Maker, and Application Insights.

  "Microsoft.Web/*",
  "Microsoft.BotService/*",
  "Microsoft.Storage/*",
  "Microsoft.Resources/deployments/*",
  "Microsoft.CognitiveServices/*",
  "Microsoft.Search/searchServices/*",
   "Microsoft.Insights/*",
  "Microsoft.Insights/components/*"

LUIS and QnA Maker require Cognitive Services permissions. QnA Maker also requires Search permissions. When creating a custom role, remember that any inherited *deny* permissions will supercede these *allow* permissions.
 
### What keeps my bot secure from clients impersonating the Bot Framework Service?
1. All authentic Bot Framework requests are accompanied by a JWT token whoes cryptographic signature can be verified by following the [authentication](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) guide. The token is designed so attackers cannot impersonate trusted services.

2. The security token accompanying every request to your bot has the ServiceUrl encoded within it, which means that even if an attacker gains access to the token, they cannot redirect the conversation to a new ServiceUrl. This is enforced by all implementations of the SDK and documented in our authentication [reference](https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-connector-authentication?view=azure-bot-service-3.0#bot-to-connector) materials.

3. If the incoming token is missing or malformed, the Bot Framework SDK will not generate a token in response. This limits how much damage can be done if the bot is incorrectly configured.
4. Inside the bot, you can manually check the ServiceUrl provided in the token. This makes the bot more fragile in the event of service topology changes so this is possible but not recommended.


Note that these are outbound connections from the bot to the Internet. There is not a list of IP Addresses or DNS names that the Bot Framework Connector Service will use to talk to the bot. Inbound IP Address whitelisting is not supported.

## Rate limiting
### What is rate limiting?
The Bot Framework service must protect itself and its customers against abusive call patterns (e.g., denial of service attack), so that no single bot can adversely affect the performance of other bots. To achieve this kind of protection, we’ve added rate limits (also known as throttling) to our endpoints. By enforcing a rate limit, we can restrict the frequency with which a client or bot can make a specific call. For example: with rate limiting enabled, if a bot wanted to post a large number of activities, it would have to space them out over a time period. Please note that the purpose of rate-limiting is not to cap the total volume for a bot. It is designed to prevent abuse of the conversational infrastructure that does not follow human conversation patterns. For example, flooding two conversations with more content than two human could ever consume.

### How will I know if I’m impacted?
It is unlikely you’ll experience rate limiting, even at high volume. Most rate limiting would only occur due to bulk sending of activities (from a bot or from a client), extreme load testing, or a bug. When a request is throttled, an HTTP 429 (Too Many Requests) response is returned along with a Retry-After header indicating the amount of time (in seconds) to wait before retrying the request would succeed. You can collect this information by enabling analytics for your bot via Azure Application Insights. Or, you can add code in your bot to log messages. 

### How does rate limiting occur?
It can happen if:
-	a bot sends messages too frequently
-	a client of a bot sends messages too frequently
-	Direct Line clients request a new Web Socket too frequently

### What are the rate limits?
We’re continuously tuning the rate limits to make them as lenient as possible while at the same time protecting our service and our users. Because thresholds will occasionally change, we aren’t publishing the numbers at this time. If you are impacted by rate limiting, feel free to reach out to us at [bf-reports@microsoft.com](mailto://bf-reports@microsoft.com).

## Bot Framework SDK
## Why doesn't the Typing activity do anything?
Some channels do not support transient typing updates in their client.

## What is the difference between the Connector library and Builder library in the SDK?

The Connector library is the exposition of the REST API. The Builder library adds the conversational dialog programming model and other features such as prompts, waterfalls, chains, and guided form completion. The Builder library also provides access to cognitive services such as LUIS.

## How do user messages relate to HTTPS method calls?

When the user sends a message over a channel, the Bot Framework web service will issue an HTTPS POST to the bot's web service endpoint. The bot may send zero, one, or many messages back to the user on that channel, by issuing a separate HTTPS POST to the Bot Framework for each message that it sends.


## How can I intercept all messages between the user and my bot?

Using the Bot Framework SDK for .NET, you can provide implementations of the `IPostToBot` and `IBotToUser` interfaces to the `Autofac` dependency injection container. Using the Bot Framework SDK for any language, you can use middleware for much the same purpose. The [BotBuilder-Azure](https://github.com/Microsoft/BotBuilder-Azure) repository contains C# and Node.js libraries that will log this data to an Azure table.

## What is the IDialogStack.Forward method in the Bot Framework SDK for .NET?

The primary purpose of `IDialogStack.Forward` is to reuse an existing child dialog that is often "reactive", where the child dialog (in `IDialog.StartAsync`) waits for an object `T` with some `ResumeAfter` handler. In particular, if you have a child dialog that waits for an `IMessageActivity` `T`, you can forward the incoming `IMessageActivity` (already received by some parent dialog) by using the `IDialogStack.Forward` method. For example, to forward an incoming `IMessageActivity` to a `LuisDialog`, call `IDialogStack.Forward` to push the `LuisDialog` onto the dialog stack, run the code in `LuisDialog.StartAsync` until it schedules a wait for the next message, and then immediately satisfy that wait with the forwarded `IMessageActivity`.

`T` is usually an `IMessageActivity`, since `IDialog.StartAsync` is typically constructed to wait for that type of activity. You might use `IDialogStack.Forward` to a `LuisDialog` as a mechanism to intercept messages from the user for
some processing before forwarding the message to an existing `LuisDialog`. Alternatively, you can also use `DispatchDialog` with `ContinueToNextGroup` for that purpose.

You would expect to find the forwarded item in the first `ResumeAfter` handler (e.g. `LuisDialog.MessageReceived`) that is scheduled by `StartAsync`.


## What is the difference between "proactive" and "reactive"?

From the perspective of your bot, "reactive" means that the user initiates the conversation by sending a message to the bot, and the bot reacts by responding to that message. In contrast, "proactive" means that the bot initiates the conversation by sending the first message to the user. For example, a bot may send a proactive message to notify a user when a timer expires or an event occurs.

## How can I send proactive messages to the user?

For examples that show how to send proactive messages, see the [C# V4 samples](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/16.proactive-messages) and [Node.js V4 samples](https://github.com/Microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/16.proactive-messages) within the BotBuilder-Samples repository on GitHub.

## How can I reference non-serializable services from my C# dialogs in SDK v3?

There are multiple options:

* Resolve the dependency through `Autofac` and `FiberModule.Key_DoNotSerialize`. This is the cleanest solution.
* Use [NonSerialized](https://msdn.microsoft.com/library/system.nonserializedattribute(v=vs.110).aspx) and [OnDeserialized](https://msdn.microsoft.com/library/system.runtime.serialization.ondeserializedattribute(v=vs.110).aspx) attributes to restore the dependency on deserialization. This is the simplest solution.
* Do not store that dependency, so that it won't be serialized. This solution, while technically feasible, is not recommended.
* Use the reflection serialization surrogate. This solution may not be feasible in some cases and risks serializing too much.

## What is an ETag?  How does it relate to bot data bag storage?

An [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) is a mechanism for [optimistic concurrency control](https://en.wikipedia.org/wiki/Optimistic_concurrency_control). The bot data bag storage uses ETags to prevent conflicting updates to the data. An ETag error with HTTP status code 412 "Precondition Failed" indicates that there were multiple messages received in parallel before the bot could finish its first operation.

The dialog stack and state are stored in bot data bags. For example, you might see the "Precondition Failed" ETag error if your bot is still processing a previous message when it receives a new message for that conversation.

## What are the possible machine-readable resolutions of the LUIS built-in date, time, duration, and set entities?

For a list of examples, see the [Pre-built entities section](/azure/cognitive-services/LUIS/luis-reference-prebuilt-entities) of the LUIS documentation.

## How can I use more than the maximum number of LUIS intents?

You might consider splitting up your model and calling the LUIS service in series or parallel.

## How can I use more than one LUIS model?

Both the Bot Framework SDK for Node.js and the Bot Framework SDK for .NET support calling multiple LUIS models from a single LUIS intent dialog. Keep in mind the following caveats:

* Using multiple LUIS models assumes the LUIS models have non-overlapping sets of intents.
* Using multiple LUIS models assumes the scores from different models are comparable, to select the "best matched intent" across multiple models.
* Using multiple LUIS models means that if an intent matches one model, it will also strongly match the "none" intent of the other models. You can avoid selecting the "none" intent in this situation; the Bot Framework SDK for Node.js will automatically scale down the score for "none" intents to avoid this issue.

## Where can I get more help on LUIS?

* [Introduction to Language Understanding (LUIS) - Microsoft Cognitive Services](https://www.youtube.com/watch?v=jWeLajon9M8) (video)
* [Advanced Learning Session for Language Understanding (LUIS)](https://www.youtube.com/watch?v=39L0Gv2EcSk) (video)
* [LUIS documentation](/azure/cognitive-services/luis/)
* [Language Understanding Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 


## What are some community-authored dialogs?

* [BotAuth](https://www.nuget.org/packages/BotAuth) - Azure Active Directory authentication
* [BestMatchDialog](http://www.garypretty.co.uk/2016/08/01/bestmatchdialog-for-microsoft-bot-framework-now-available-via-nuget/) - Regular expression-based dispatch of user text to dialog methods

## What are some community-authored templates?

* [ES6 BotBuilder](https://github.com/brene/botbuilder-es6-template) - ES6 Bot Builder template

## Related Services
### How does the Bot Framework relate to Cognitive Services?

Both the Bot Framework and [Cognitive Services](https://www.microsoft.com/cognitive) are built from years of research and use in popular Microsoft products. These capabilities enable every organization to take advantage of the power of data, the cloud and intelligence to build their own intelligent systems that unlock new opportunities, increase their speed of business and lead the industries in which they serve their customers.

### What is the Direct Line channel?

Direct Line is a REST API that allows you to add your bot into your service, mobile app, or webpage.

You can write a client for the Direct Line API in any language. Simply code to the [Direct Line protocol][DirectLineAPI], generate a secret in the Direct Line configuration page, and talk to your bot from wherever your code lives.

Direct Line is suitable for:

* Mobile apps on iOS, Android, and Windows Phone, and others
* Desktop applications on Windows, OSX, and more
* Webpages where you need more customization than the [embeddable Web Chat channel][WebChat] offers
* Service-to-service applications


## App Registration

### I need to manually create my App Registration. How do I create my own App Registration?

Creating your own App Registration will be necessary for situations like the following:

- You created your bot in the Bot Framework portal (such as https://dev.botframework.com/bots/new) 
- You are unable to make app registrations in your organization and need another party to create the App ID for the bot you're building
- You otherwise need to manually create your own App ID (and password)

To create your own App ID, follow the steps below.

1. Sign into your [Azure account](https://portal.azure.com). If you don't have an Azure account, you can [sign up for a free account](https://azure.microsoft.com/free/).
2. Go to [the app registrations blade](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) and click **New registration** in the action bar at the top.

    ![new registration](media/app-registration/new-registration.png)

3. Enter a display name for the application registration in the *Name* field and select the supported account types. The name does not have to match the bot ID.

    > [!IMPORTANT]
    > In the *Supported account types*, select the *Accounts in any organizational directory and personal Microsoft accounts (e.g. Skype, Xbox, Outlook.com)* radio button. If any of the other options are selected, **the bot will be unusable**.

    ![registration details](media/app-registration/registration-details.png)

4. Click **Register**

    After a few moments, the newly created app registration should open a blade. Copy the *Application (client) ID* in the Overview blade and paste it in to the App ID field.

    ![application id](media/app-registration/app-id.png)

If you’re creating your bot through the Bot Framework portal, then you’re done setting up your app registration; the secret will be generated automatically. 

If you’re making your bot in the Azure portal, you need to generate a secret for your app registration. 

1. Click on **Certificates & secrets** in the left navigation column of your app registration’s blade.
2. In that blade, click the **New client secret** button. In the dialog that pops up, enter an optional description for the secret and select **Never** from the Expires radio button group. 

    ![new secret](media/app-registration/new-secret.png)

3. Copy your secret’s value from the table under *Client secrets* and paste it into the *Password* field for your application, and click **OK** at the bottom of that blade. Then, proceed with the bot creation. 

    > [!NOTE]
    > The secret will only be visible while on this blade, and you won't be able to retreive it after you leave that page. Be sure to copy it somewhere safe.

    ![new app id](media/app-registration/create-app-id.png)


[DirectLineAPI]: https://docs.microsoft.com/azure/bot-service/rest-api/bot-framework-rest-direct-line-3-0-concepts
[Support]: bot-service-resources-links-help.md
[WebChat]: bot-service-channel-connect-webchat.md
