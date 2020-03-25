---
title: Connect a bot to Facebook Messenger - Bot Service
description: Learn how to configure a bot's connection to Facebook Messenger.
keywords: Facebook Messenger, bot channel, Facebook App, App ID, App Secret, Facebook bot, credentials
manager: kamrani
ms.topic: article
ms.author: kamrani
ms.service: bot-service
ms.date: 01/16/2020
---

# Connect a bot to Facebook

Your bot can be connected to both Facebook Messenger and Facebook Workplace, so that it can communicate with users on both platforms. The following tutorial shows how to connect a bot to these two channels.

> [!NOTE]
> The Facebook UI may appear slightly different depending on which version you are using.

## Connect a bot to Facebook Messenger

To learn more about developing for Facebook Messenger, see the [Messenger platform documentation](https://developers.facebook.com/docs/messenger-platform). You may wish to review Facebook's [pre-launch guidelines](https://developers.facebook.com/docs/messenger-platform/product-overview/launch#app_public), [quick start](https://developers.facebook.com/docs/messenger-platform/guides/quick-start), and [setup guide](https://developers.facebook.com/docs/messenger-platform/guides/setup).

To configure a bot to communicate using Facebook Messenger, enable Facebook Messenger on a Facebook page and then connect the bot.

### Copy the Page ID

The bot is accessed through a Facebook Page.

1. [Create a new Facebook Page](https://www.facebook.com/bookmarks/pages) or go to an existing Page.

1. Open the Facebook Page's **About** page and then copy and save the **Page ID**.

### Create a Facebook app

1. In your browser, navigate to [Create a new Facebook App](https://developers.facebook.com/quickstarts/?platform=web).
1. Enter the name of your app and click  the **Create New Facebook App ID** button.

    ![Create App](media/channels/fb-create-messenger-bot-app.png)

1. In the displayed dialog, enter your email address and click the **Create App ID** button.

    ![Create an App ID](media/channels/fb-create-messenger-bot-app-id.png)

1. Go through the wizard steps.

1. Enter the required check information, then click the **Skip Quick Start** button in the upper right.

1. In the left pane of the next displayed window, expand *Settings* and click **Basic**.

1. In the right pane, copy and save the **App ID** and **App Secret**.

    ![Copy App ID and App Secret](media/channels/fb-messenger-bot-get-appid-secret.png)

1. In the left pane, under *Settings*, click **Advanced**.

1. In the right pane, set **Allow API Access to App Settings** slider to **Yes**.

    ![Copy App ID and App Secret](media/channels/fb-messenger-bot-api-settings.png)

1. In the page bottom right, click the **Save Changes** button.

### Enable messenger

1. In the left pane, click **Dashboard**.
1. In the right pane, scroll down and in the **Messenger** box, click the **Set Up** button. The Messenger entry is displayed under the *PRODUCTS* section in the left pane.  

    ![Enable messenger](media/channels/fb-messenger-bot-enable-messenger.png)

### Generate a Page Access Token

1. In the left pane, under the Messenger entry, click **Settings**.
1. In the right pane, scroll down and in the **Token Generation** section, select the target page.

    ![Enable messenger](media/channels/fb-messenger-bot-select-messenger-page.png)

1. Click the **Edit Permissions** button to grant the app pages_messaging in order to generate an access token.
1. Follow the wizard steps. In the last step accept the default settings and click the **Done** button. At the end a **page access token** is generated.

    ![Messenger permissions](media/channels/fb-messenger-bot-permissions.png)

1. Copy and save the **Page Access Token**.

### Enable webhooks

In order to send messages and other events from your bot to Facebook Messenger, you must enable webhooks integration. At this point, let's leave the Facebook setting steps pending; will come back to them.

1. In your browser open a new window and navigate to the [Azure portal](https://portal.azure.com/).

1. In the Resource list, click on the bot resource registration and in the related blade click **Channels**.

1. In the right pane, click the **Facebook** icon.

1. In the wizard enter the Facebook information stored in the previous steps. If the information is correct, at the bottom of the wizard, you should see the **callback URL** and the **verify token**. Copy and store them.  

    ![fb messenger channel config](media/channels/fb-messenger-bot-config-channel.PNG)

1. Click the **Save** button.

1. Let's go back to the Facebook settings. In the right pane, scroll down and in the **Webhooks** section, click the **Subscribe To Events** button. This is to forward messaging events from Facebook Messenger to the bot.

    ![Enable webhooks](media/channels/fb-messenger-bot-webhooks.PNG)

1. In the displayed dialog, enter the **Callback URL** and **Verify Token** values stored previously. Under **Subscription Fields**, select *message\_deliveries*, *messages*, *messaging\_optins*, and *messaging\_postbacks*.

    ![Config webhooks](media/channels/fb-messenger-bot-config-webhooks.png)

1. Click the **Verify and Save** button.

1. Select the Facebook page to subscribe the webhook. Click the **Subscribe** button.

    ![Config webhooks page](media/channels/fb-messenger-bot-config-webhooks-page.PNG)

### Submit for review

Facebook requires a Privacy Policy URL and Terms of Service URL on its basic app settings page. The [Code of Conduct](https://investor.fb.com/corporate-governance/code-of-conduct/default.aspx) page contains third party resource links to help create a privacy policy. The [Terms of Use](https://www.facebook.com/terms.php) page contains sample terms to help create an appropriate Terms of Service document.

After the bot is finished, Facebook has its own [review process](https://developers.facebook.com/docs/messenger-platform/app-review) for apps that are published to Messenger. The bot will be tested to ensure it is compliant with Facebook's [Platform Policies](https://developers.facebook.com/docs/messenger-platform/policy-overview).

### Make the App public and publish the Page

> [!NOTE]
> Until an app is published, it is in [Development Mode](https://developers.facebook.com/docs/apps/managing-development-cycle). Plugin and API functionality will only work for admins, developers, and testers.

After the review is successful, in the App Dashboard under App Review, set the app to Public.
Ensure that the Facebook Page associated with this bot is published. Status appears in Pages settings.

## Connect a bot to Facebook Workplace

> [!NOTE]
> On December 16, 2019, Workplace by Facebook changed its security model for custom integrations. Prior integrations built using Microsoft Bot Framework v4 need to be updated to use the Bot Framework Facebook adapters per the instructions below prior to February 28, 2020.
>
> Facebook will only consider integrations with limited access to Workplace data (low sensitivity permissions) eligible for continued use until December 31, 2020 if such integrations have completed and passed Security RFI and if the developer reaches out before January 15, 2020 via [Direct Support](https://my.workplace.com/work/admin/direct_support) to request continued use of the app.
>
> Bot Framework adapters are available for [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) and [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter) bots.

Facebook Workplace is a business-oriented version of Facebook, which allows employees to easily connect and collaborate. It contains live videos, news feeds, groups, messenger, reactions, search, and trending posts. It also supports:

- Analytics and integrations. A dashboard with analytics, integration, single sign-on, and identity providers that companies use to integrate Workplace with their existing IT systems.
- Multi-company groups. Shared spaces in which employees from different organizations can work together and collaborate.

See the [Workplace Help Center](https://workplace.facebook.com/help/work/) to learn more about Facebook Workplace and [Workplace Developer Documentation](https://developers.facebook.com/docs/workplace) for guidelines about developing for Facebook Workplace.

To use Facebook Workplace with your bot, you must create a Workplace account and a custom integration to connect the bot.

### Create a Workplace Premium account

1. Submit an application to [workplace](https://www.facebook.com/workplace) on  behalf of your company.
1. Once your application has been approved, you will receive an email inviting you to join. The response may take a while.
1. From the e-mail invitation, click **Get Started**.
1. Enter your profile information.
    > [!TIP]
    > Set yourself as the system administrator. Remember that only system administrators can create custom integrations.
1. Click **Preview Profile** and verify the information is correct.
1. Access *Free Trial*.
1. Create **password**.
1. Click **Invite Coworkers** to invite employees to sign-in. The employees you invited will become members as soon as they sign. They will go through a similar sign-in process as described in these steps.

### Create a custom integration

Create a [custom integration](https://developers.facebook.com/docs/workplace/custom-integrations-new) for your Workplace following the steps described below. When you create a custom integration, an app with defined permissions and a page of type 'Bot' only visible within your Workplace community are created.

1. In the **Admin Panel**, open the **Integrations** tab.
1. Click on the **Create your own custom App** button.

    ![Workplace Integration](media/channels/fb-integration.png)

1. Choose a display name and a profile picture for the app. Such information will be shared with the page of type 'Bot'.
1. Set the **Allow API Access to App Settings** to "Yes".
1. Copy and safely store the App ID, App Secret and App Token that's shown to you.

    ![Workplace Keys](media/channels/fb-keys.png)

1. Now you have finished creating a custom integration. You can find the page of type 'Bot' in your Workplace community,as shown below.

    ![Workplace page](media/channels/fb-page.png)

### Update your bot code with Facebook adapter

Your bot's source code needs to be updated to include an adapter to communicate with Workplace by Facebook. Adapters are available for [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) and [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter) bots.

### Provide Facebook credentials

You will need to update appsettings.json of your bot with **Facebook App ID**, **Facebook App Secret** and **Page Access Token** values copied from the Facebook Workplace previously. Instead of a traditional pageID, use the numbers following the integrations name on its **About** page. Follow these instructions to update your bot source code in [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) or [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter).

### Submit for review

Please refer to the **Connect a bot to Facebook Messenger** section and [Workplace Developer Documentation](https://developers.facebook.com/docs/workplace) for details.

### Make the App public and publish the Page

Please refer to the **Connect a bot to Facebook Messenger** section for details.

### Setting the API version

If you receive a notification from Facebook about deprecation of a certain version of the Graph API, go to [Facebook developers page](https://developers.facebook.com). Navigate to your bot’s **App Settings** and go to **Settings > Advanced > Upgrade API version**, then switch **Upgrade All Calls** to 3.0.

![API version upgrade](media/channels/fb-version-upgrade.png)

## Connect a bot to Facebook using the Facebook adapter

Use the Bot Framework Facebook adapter to connect your bot with Facebook Workplace. To connect to Facebook messenger, you can use the Facebook channel or the Facebook adapter.
Facebook adapters are available for [JavaScript/Node.js](https://aka.ms/npm-botbuilder-adapter-facebook) and [C#/.NET](https://aka.ms/botbuilder-dotnet-facebook-adapter) bots.

In this article you will learn how to connect a bot to Facebook using the adapter.  This article will walk you through modifying the EchoBot sample to connect it to Facebook.

The instructions below cover the C# implementation of the Facebook adapter. For instructions on using the JavaScript adapter, part of the BotKit libraries, [see the BotKit Facebook documentation](https://botkit.ai/docs/v4/platforms/facebook.html).

### Prerequisites

- The [EchoBot sample code](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/02.echo-bot)
- A Facebook for Developers account. If you do not have an account, you can [create one here](https://developers.facebook.com).

### Create a Facebook app, page and gather credentials

1. Log into [https://developers.facebook.com](https://developers.facebook.com). Click **My Apps** in the main menu and click **Create App** from the drop down menu.

![Create app](media/bot-service-channel-connect-facebook/create-app-button.png)

1. In the pop-up window that appears, enter a name for your new app and then click **Create App ID**.

![Define app name](media/bot-service-channel-connect-facebook/app-name.png)

#### Set up Messenger and associate a Facebook page

1. Once your app has been created, you will see a list of products available to set up. Click the **Set Up** button next to the **Messenger** product.

1. You now need to associate your new app with a Facebook page (if you do not have an existing page you want to use, you can create one by clicking **Create New Page** in the **Access Tokens** section). Click **Add or Remove Pages**, select the page you want to associated with your app and click **Next**. Leave the **Manage and access Page conversations on Messenger** setting enabled and click **Done**.

![Set up messenger](media/bot-service-channel-connect-facebook/app-page-permissions.png)

1. Once you have associated your page, click the **Generate Token** button to generate a page access token.  Make a note of this token as you will need it in a later step when configuring your bot application.

#### Obtain your app secret

1. In the left hand menu, click **Settings** and then click **Basic** to navigate to the basic setting page for your app.

1. On the basic settings page, click the **Show** button next to your **App Secret**.  Make a note of this secret as you will need it in a later step when configuring your bot application.

### Wiring up the Facebook adapter in your bot

Now that you have your Facebook app, page and credentials, you need to configure your bot application.

#### Install the Facebook adapter NuGet package

Add  the [Microsoft.Bot.Builder.Adapters.Facebook](https://www.nuget.org/packages/Microsoft.Bot.Builder.Adapters.Facebook/) NuGet package. For more information on using NuGet, see [Install and manage packages in Visual Studio](https://aka.ms/install-manage-packages-vs).

#### Create a Facebook adapter class

Create a new class that inherits from the ***FacebookAdapter*** class. This class will act as our adapter for the Facebook channel and include error handling capabilities (similar to the ***BotFrameworkAdapterWithErrorHandler*** class already in the sample, used for handling other requests from Azure Bot Service).

```csharp
public class FacebookAdapterWithErrorHandler : FacebookAdapter
{
    public FacebookAdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
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

#### Create a new controller for handling Facebook requests

Create a new controller which will handle requests from Facebook, on a new endpoing 'api/facebook' instead of the default 'api/messages' used for requests from Azure Bot Service Channels.  By adding an additional endpoint to your bot, you can accept requests from Bot Service channels, as well as from Facebook, using the same bot.

```csharp
[Route("api/facebook")]
[ApiController]
public class FacebookController : ControllerBase
{
    private readonly FacebookAdapter _adapter;
    private readonly IBot _bot;

    public FacebookController(FacebookAdapter adapter, IBot bot)
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

#### Inject the Facebook adapter in your bot startup.cs

Add the following line to the ***ConfigureServices*** method within your startup.cs file. This will register your Facebook adapter and make it available for your new controller class.  The configuration settings you added in the previous step will be automatically used by the adapter.

```csharp
services.AddSingleton<FacebookAdapter, FacebookAdapterWithErrorHandler>();
```

Once added, your ***ConfigureServices*** method shold look like this.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);

    // Create the default Bot Framework Adapter (used for Azure Bot Service channels and emulator).
    services.AddSingleton<IBotFrameworkHttpAdapter, BotFrameworkAdapterWithErrorHandler>();

    // Create the Facebook Adapter
    services.AddSingleton<FacebookAdapter, FacebookAdapterWithErrorHandler>();

    // Create the bot as a transient. In this case the ASP Controller is expecting an IBot.
    services.AddTransient<IBot, EchoBot>();
}
```

#### Obtain a URL for your bot

Now that you have wired up the adapter in your bot project, you need to provide to Facebook the correct endpoint for your application, so that your bot will receive messages. You also need this URL to complete configuration of your bot application.

To complete this step, [deploy your bot to Azure](https://aka.ms/bot-builder-deploy-az-cli) and make a note of the URL of your deployed bot.

> [!NOTE]
> If you are not ready to deploy your bot to Azure, or wish to debug your bot when using the Facebook adapter, you can use a tool such as [ngrok](https://www.ngrok.com) (which you will likely already have installed if you have used the Bot Framework emulator previously) to tunnel through to your bot running locally and provide you with a publicly accessible URL for this.
>
> If you wish create an ngrok tunnel and obtain a URL to your bot, use the following command in a terminal window (this assumes your local bot is running on port 3978, alter the port numbers in the command if your bot is not).
>
> ```cmd
> ngrok.exe http 3978 -host-header="localhost:3978"
> ```

#### Add Facebook app settings to your bot's configuration file

Add the settings shown below to your appSettings.json file in your bot project. You populate **FacebookAppSecret** and **FacebookAccessToken** using the values you gathered when creating and configuring your Facebook App. **FacebookVerifyToken** should be a random string that you create and will be used to ensure your bot's endpoint is authenitic when called by Facebook.

```json
  "FacebookVerifyToken": "",
  "FacebookAppSecret": "",
  "FacebookAccessToken": ""
```

Once you have populated the settings above, you should redeploy (or restart if running locally with ngrok) your bot.

### Complete configuration of your Facebook app

The final step is to configure your new Facebook app's Messenger endpoint, to ensure your bot receives messages.

1. Within the dashboard for your app, click **Messenger** in the left hand menu and then click **Settings**.

1. In the **Webhooks** section click **Add Callback URL**.

1. In the **Callback URL** text box enter your bot's URL, plus the `api/facebook` endpoint you specified in your newly created controller. For example, `https://yourboturl.com/api/facebook`. In the **Verify Token** text box enter the verify token you created earlier and used in your bot application's appSettings.json file.

    ![Edit callback url](media/bot-service-channel-connect-facebook/edit-callback-url.png)

1. Click **Verify and Save**. Ensure you bot is running, as Facebook will make a request to your application's endpoint and verify it using your **Verify Token**.

1. Once your callback URL has been verified, click the **Add Subscriptions** button that is now shown.  In the pop-up window, select the following subscriptions and click **Save**.

    - **messages**
    - **messaging_postbacks**
    - **messaging_optins**
    - **messaging_deliveries**

    ![Webhook subscriptions](media/bot-service-channel-connect-facebook/webhook-subscriptions.png)

### Test your bot with adapter in Facebook

You can now test whether your bot is connected to Facebook correctly by sending a message via the Facebook Page you associated with your new Facebook app.  

1. Navigate to your Facebook Page.

1. Click **Add a Button** button.

    ![Add a button](media/bot-service-channel-connect-facebook/add-button.png)

1. Select **Contact You** and **Send Message** and click **Next**.

    ![Add a button](media/bot-service-channel-connect-facebook/button-settings.png)

1. When asked **Where would you like this button to send people to?** select **Messenger** and click **Finish**.

    ![Add a button](media/bot-service-channel-connect-facebook/button-settings-2.png)

1. Hover over the new **Send Message** button that is now shown on your Facebook Page and click **Test Button** from the pop-up menu.  This will start a new conversation with your app via Facebook Messenger, which you can use to test messaging your bot. Once the message is received by your bot, it will send a message back to you, echoing the text from your message.

You can also test this feature using the [sample bot for the Facebook adapter](https://aka.ms/csharp-61-facebook-adapter-sample) by populating the appSettings.json file with the same values described in the steps above.

## See also

- **Sample code**. Use the [Facebook-events](https://aka.ms/facebook-events) sample bot to explore the bot communication with Facebook Messenger.
