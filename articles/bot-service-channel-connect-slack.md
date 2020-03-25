---
title: Connect a bot to Slack - Bot Service
description: Learn how to configure a bot's connection to Slack.
keywords: connect a bot, bot channel, Slack bot, Slack messaging app, slack adapter
author: JonathanFingold
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 01/09/2019
---

# Connect a bot to Slack

There are two ways in which you can confgure Slack messaging app:
- Use Azure Bot Service portal to connect your bot
- Use the Slack adapter

## [Azure Bot Service Portal](#tab/abs)
## Create a Slack application for your bot

1. In your browser, log into [Slack](https://slack.com/signin).
1. Navigate to [create a Slack application](https://api.slack.com/apps) channel.

![Set up bot](~/media/channels/slack-NewApp.png)

## Create an app and assign a Development Slack team

1. Click the **Create New App** button.
1. Enter an App Name and select a Development Slack Team.
If you are not already a member of a Development Slack Team, [create or join one](https://slack.com/).

![Create app](~/media/channels/slack-CreateApp.png)

1. Click the **Create App** button. Slack will create your app and generate a **Client ID** and **Client Secret**.

## Add a new Redirect URL

1. In the left panel, click the **OAuth & Permissions** link.
2. In the right panel, click the **Add a new Redirect URL** button.
3. Enter the following value `https://slack.botframework.com`.
4. Click the **Add** button.
5. Click the **Save URLs** button.

![Add Redirect URL](~/media/channels/slack-RedirectURL.png)

## Add bot token scopes
<!-- Replaces add user -->
1. In the left panel, click the **OAuth & Permissions** link.
1. In the right panel, scroll down to **Scopes**.
1. In the **Bot Token Scopes** section, click the **Add an OAuth Scope** button.
1. Make sure to select the following scopes, also shown in the picture:

    - `app_mentions:read`
    - `channels:history`
    - `channels:read`
    - `chat:write`
    - `groups:history`
    - `groups:read`
    - `im:history`
    - `mpim:history`

    ![slack bot scopes](media/channels/slack-bot-scopes.PNG)

1. In the left panel select the **App Home** link.
1. In the right panel, scroll down and enable **Always Show My Bot as Online**.

    ![slack bot display](media/channels/slack-bot-display.PNG)

## Subscribe to Bot Events

Follow these steps to subscribe to six particular bot events. By subscribing to bot events, your app will be notified of user activities at the URL you specify.

> [!TIP]
> Your bot handle is the name of your bot. To find a bot's handle,
> visit [https://dev.botframework.com/bots](https://dev.botframework.com/bots),
> choose a bot, and record the name of the bot.

1. Select the **Event Subscriptions** tab.
2. Click **Enable Events** to **On**.
3. In **Request URL**, enter `https://slack.botframework.com/api/Events/{YourBotHandle}`, where `{YourBotHandle}` is your bot handle, without the braces. The bot handle used in this example is **ContosoBot**.

   ![Subscribe Events: top](~/media/channels/slack-SubscribeEvents-a.png)

4. In **Subscribe to Bot Events**, click **Add Bot User Event**.
5. In the list of events, select these six event types:

    - `member_joined_channel`
    - `member_left_channel`
    - `message.channels`
    - `message.groups`
    - `message.im`
    - `message.mpim`

   ![Subscribe Events: middle](~/media/channels/slack-SubscribeEvents-b.png)

6. Click **Save Changes**.

   ![Subscribe Events: bottom](~/media/channels/slack-SubscribeEvents-c.png)

## Add and Configure Interactive Messages (optional)

If your bot will use Slack-specific functionality such as buttons, follow these steps:

1. Select the **Interactive Components** tab and click **Enable Interactive Components**.
2. Enter `https://slack.botframework.com/api/Actions` as the **Request URL**.
3. Click the **Save changes** button.

![Enable messages](~/media/channels/slack-MessageURL.png)

## Gather credentials

Select the **Basic Information** tab and scroll to the **App Credentials** section.
The Client ID, Client Secret, and Verification Token required for configuration of your Slack bot are displayed.

![Gather credentials](~/media/channels/slack-AppCredentials.png)

## Submit credentials

In a separate browser window, return to the Bot Framework site at `https://dev.botframework.com/`.

1. Select **My bots** and choose the Bot that you want to connect to Slack.
2. In the **Channels** section, click the Slack icon.
3. In the **Enter your Slack credentials** section, paste the App Credentials from the Slack website into the appropriate fields.
4. The **Landing Page URL** is optional. You may omit or change it.
5. Click **Save**.

![Submit credentials](~/media/channels/slack-SubmitCredentials.png)

Follow the instructions to authorize your Slack app's access to your Development Slack Team.

## Enable the bot

On the Configure Slack page, confirm the slider by the Save button is set to **Enabled**.
Your bot is configured to communicate with users in Slack.

## Create an Add to Slack button

Slack provides HTML you can use to help Slack users find your bot in the
*Add the Slack button* section of [this page](https://api.slack.com/docs/slack-button).
To use this HTML with your bot, replace the href value (begins with `https://`) with the URL found in your bot's Slack channel settings.
Follow these steps to get the replacement URL.

1. On [https://dev.botframework.com/bots](https://dev.botframework.com/bots), click your bot.
2. Click **Channels**, right-click the entry named **Slack**, and click **Copy link**. This URL is now in your clipboard.
3. Paste this URL from your clipboard into the HTML provided for the Slack button. This URL replaces the href value provided by Slack for this bot.

Authorized users can click the **Add to Slack** button provided by this modified HTML to reach your bot on Slack.

## [Slack adapter](#tab/adapter)
## Connect a bot to Slack using the Slack adapter

As well as the channel available in the Azure Bot Service to connect your bot with Slack, you can also use the Slack adapter. In this article you will learn how to connect a bot to Slack using the adapter.  This article will walk you through modifying the EchoBot sample to connect it to a Slack app.

> [!NOTE]
> The instructions below cover the C# implementation of the Slack adapter. For instructions on using the JS adapter, part of the BotKit libraries, [see the BotKit Slack documentation](https://botkit.ai/docs/v4/platforms/slack.html).

## Prerequisites

* The [EchoBot sample code](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)

* Access to a Slack workspace with sufficient permissions to create and manage applications at  [https://api.slack.com/apps](https://api.slack.com/apps). If you do not have access to a Slack environment you can create a workspace for [free](https://www.slack.com).

## Create a Slack application for your bot

Log into [Slack](https://slack.com/signin) and then go to [create a Slack application](https://api.slack.com/apps) channel.

![Set up bot](~/media/channels/slack-NewApp.png)

Click the 'Create new app' button.

### Create an app and assign a development Slack team

Enter an **App Name** and select a **Development Slack Workspace**. If you are not already a member of a development Slack team, [create or join one](https://slack.com/).

![Create app](~/media/channels/slack-CreateApp.png)

Click **Create App**. Slack will create your app and generate a client ID and client secret.

### Gather required configuration settings for your bot

Once your app is created, collect the following information. You will need this to connect your bot to Slack.

1. Note the **Verification Token** and the **Signing Secret** from the **Basic Information** tab and keep them for later to configure your bot settings.

![Slack tokens](~/media/bot-service-adapter-connect-slack/slack-tokens.png)

2. Navigate to the **Install App** page under the **Settings** menu and follow the instructions to install your app into a Slack team.  Once installed, copy the **Bot User OAuth Access Token** and, again, keep this for later to configure your bot settings.

## Wiring up the Slack adapter in your bot

### Install the Slack adapter NuGet package

Add  the [Microsoft.Bot.Builder.Adapters.Slack](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Slack/) NuGet package. For more information on using NuGet, see [Install and manage packages in Visual Studio](https://aka.ms/install-manage-packages-vs)

### Create a Slack adapter class

Create a new class that inherits from the ***SlackAdapter*** class. This class will act as our adapter for the Slack channel and include error handling capabilities (similar to the ***BotFrameworkAdapterWithErrorHandler*** class already in the sample, used for handling other requests from Azure Bot Service).

```csharp
public class SlackAdapterWithErrorHandler : SlackAdapter
{
    public SlackAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
    : base(configuration, logger)
    {
        OnTurnError = async (turnContext, exception) =>
        {
            // Log any leaked exception from the application.
            logger.LogError(exception, $"[OnTurnError] unhandled error : {exception.Message}");

            // Send a message to the user
            await turnContext.SendActivityAsync("The bot encountered an error or bug.");
            await turnContext.SendActivityAsync("To continue to run this bot, please fix the bot source code.");

            // Send a trace activity, which will be displayed in the Bot Framework Emulator
            await turnContext.TraceActivityAsync("OnTurnError Trace", exception.Message, "https://www.botframework.com/schemas/error", "TurnError");
        };
    }
}
```

### Create a new controller for handling Slack requests

We create a new controller which will handle requests from your slack app, on a new endpoing 'api/slack' instead of the default 'api/messages' used for requests from Azure Bot Service Channels.  By adding an additional endpoint to your bot, you can accept requests from Bot Service channels, as well as from Slack, using the same bot.

```csharp
[Route("api/slack")]
[ApiController]
public class SlackController : ControllerBase
{
    private readonly SlackAdapter _adapter;
    private readonly IBot _bot;

    public SlackController(SlackAdapter adapter, IBot bot)
    {
        _adapter = adapter;
        _bot = bot;
    }

    [HttpPost]
    [HttpGet]
    public async Task PostAsync()
    {
        // Delegate the processing of the HTTP POST to the adapter.
        // The adapter will invoke the bot.
        await _adapter.ProcessAsync(Request, Response, _bot);
    }
}
```

### Add Slack app settings to your bot's configuration file

Add the 3 settings shown below to your appSettings.json file in your bot project, populating each one with the values gathered earlier when creating your Slack app.

```json
  "SlackVerificationToken": "",
  "SlackBotToken": "",
  "SlackClientSigningSecret": ""
```

### Inject the Slack adapter In your bot startup.cs

Add the following line to the ***ConfigureServices*** method within your startup.cs file. This will register your Slack adapter and make it available for your new controller class.  The configuration settings you added in the previous step will be automatically used by the adapter.

```csharp
services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();
```

Once added, your ***ConfigureServices*** method shold look like this.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Slack Adapter
    services.AddSingleton<SlackAdapter, SlackAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

## Complete configuration of your Slack app

### Obtain a URL for your bot

Now that you have created a Slack app and wired up the adapter in your bot project, the final step is to point the Slack app to the correct endpoint on your bot and subscribe your app to ensure your bot receives messages.  To do this your bot must be running, so that Slack can verify the URL to the endpoint is valid.

To complete this step, [deploy your bot to Azure](https://aka.ms/bot-builder-deploy-az-cli) and make a note of the URL to your deployed bot.

> [!NOTE]
> If you are not ready to deploy your bot to Azure, or wish to debug your bot when using the Slack adapter, you can use a tool such as [ngrok](https://www.ngrok.com) (which you will likely already have installed if you have used the Bot Framework emulator previously) to tunnel through to your bot running locally and provide you with a publicly accessible URL for this.
>
> If you wish create an ngrok tunnel and obtain a URL to your bot, use the following command in a terminal window (this assumes your local bot is running on port 3978, alter the port numbers in the command if your bot is not).
>
> ```
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

### Update your Slack app

Navigate back to the [Slack API dashboard]([https://api.slack.com/apps]) and select your app.  You now need to configure 2 URLs for your app and subscribe to the appropriate events.

1. In the **OAuth & Permissions** tab, the **Redirect URL** should be your bot's URL, plus the `api/slack` endpoint you specified in your newly created controller. For example, `https://yourboturl.com/api/slack`.

![Slack redirect URL](~/media/bot-service-adapter-connect-slack/redirect-url.png)

2. In the **Event Subscriptions** tab, fill in the **Request URL** with the same URL you used in step 1.

3. Enable events using the toggle at the top of the page.

4. Expand the **Subscribe to bot events** section and use the **Add Bot User Event** button to subscribe to the **im_created** and **message.im** events.

![Slack event subscriptions](~/media/bot-service-adapter-connect-slack/event-subscriptions.png)

## Test your bot with adapter in Slack

Your Slack app is now configured and you can now login to the Slack workspace you installed your app into. (You will see it listed under the 'Apps' section of the left hand menu.) Select your app and try sending a message. You should see it echoed back to you in the IM window.

You can also test this feature using the [sample bot for the Slack adapter](https://aka.ms/csharp-60-slack-adapter-sample) by populating the appSettings.json file with the same values described in the steps above. This sample has additional steps described in the README file to show examples of link sharing, receiving attachments, and sending interactive messages.

---
