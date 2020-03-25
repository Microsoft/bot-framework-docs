---
title: Debug a bot - Bot Service
description: Learn how to debug a bot built using Bot Service.
author: v-ducvo
ms.author: kamrani
keywords: Bot Framework SDK, debug bot, test bot, bot emulator, emulator
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 2/26/2019
---

# Debug a bot

This article describes how to debug your bot using an integrated development environment (IDE) such as Visual Studio or Visual Studio Code and the Bot Framework Emulator. While you can use these methods to debug any bot locally, this article uses a [C# bot](~/dotnet/bot-builder-dotnet-sdk-quickstart.md) or [Javascript bot](~/javascript/bot-builder-javascript-quickstart.md) created in the quickstart.

> [!NOTE]
> In this article, we use the Bot Framework Emulator to send and receive messages from the bot during debugging. If you are looking for other ways to debug your bot using the Bot Framework Emulator, please read the [Debug with the Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator) article. 

## Prerequisites 
- Download and install the [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Download and install [Visual Studio Code](https://code.visualstudio.com) or [Visual Studio](https://www.visualstudio.com/downloads) (Community Edition or above).

<!-- ### Debug a JavaScript bot using command-line and emulator

To run a JavaScript bot using the command line and testing the bot with the emulator, do the following:
1. From the command line, change directory to your bot project directory.
1. Start the bot by running the command **node app.js**.
1. Start the emulator and connect to the bot's endpoint (e.g.: **http://localhost:3978/api/messages**). If this is the first time you are running 
the bot then click **File > New Bot** and follow the instructions on screen. Otherwise, click **File > Open Bot** to open an existing bot. 
Since this bot is running locally on your computer, you can leave the **MicrosoftAppId** and **MicrosoftAppPassword** fields blank. 
For more information, see [Debug with the Emulator](bot-service-debug-emulator.md).
1. From the emulator, send your bot a message (e.g.: send the message "Hi"). 
1. Use the **Inspector** and **Log** panels on the right side of the emulator window to debug your bot. For example, clicking on any of the messages bubble (e.g.: the "Hi" message bubble in the screenshot below) will show you the detail of that message in the **Inspector** panel. You can use it to view requests and responses as messages are exchanged between the emulator and the bot. Alternatively, you can click on any of the linked text in the **Log** panel to view the details in the **Inspector** panel.


   ![Inspector panel on the Emulator](~/media/bot-service-debug-bot/emulator_inspector.png) -->

## Debug a JavaScript bot using breakpoints in Visual Studio Code

In Visual Studio Code, you can set breakpoints and run the bot in debug mode to step through your code. To set breakpoints in VS Code, do the following:

1. Launch VS Code and open your bot project folder.
2. From the menu bar, click **Debug** and click **Start Debugging**. If you are prompted to select a runtime engine to run your code, select **Node.js**. At this point, the bot is running locally. 
3. Click the **.js** file and set breakpoints as necessary. In VS Code, you can set breakpoints by hovering your mouse over the column to the left of the line numbers. A small red dot will appear. If you click on the dot, the breakpoint is set. If you click the dot again, the breakpoint is removed.

   ![Set breakpoint in VS Code](~/media/bot-service-debug-bot/breakpoint-set.png)

4. Start the Bot Framework Emulator and connect to your bot as described in the [Debug with the Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator) article. 
5. From the emulator, send your bot a message (for example, send the message "Hi"). Execution will stop at the line where you place the breakpoint.

   ![Debug in VS Code](~/media/bot-service-debug-bot/breakpoint-caught.png)

## Debug a C# bot using breakpoints in Visual Studio

In Visual Studio (VS), you can set breakpoints and run the bot in debug mode to step through your code. To set breakpoints in VS, do the following:

1. Navigate to your bot folder and open the **.sln** file. This will open the solution in VS.
2. From the menu bar, click **Build** and click **Build Solution**.
3. In the **Solution Explorer**, click the **.cs** file and set breakpoints as necessary. This file defines your main bot logic. In VS, you can set breakpoints by hovering your mouse over the column to the left of the line numbers. A small red dot will appear. If you click on the dot the breakpoint is set. If you click the dot again the breakpoint is removed.
4. From the menu, click **Debug** and click **Start Debugging**. At this point, the bot is running locally. 

   ![Set breakpoint in VS](~/media/bot-service-debug-bot/breakpoint-set-vs.png)

<!--
   > [!NOTE]
   > If you get the "Value cannot be null" error, check to make sure your **Table Storage** setting is valid.
   > The **EchoBot** is default to using **Table Storage**. To use Table Storage in your bot, you need the table *name* and *key*. If you do not have a Table Storage instance ready, you can create one or for testing purposes, you can comment out the code that uses **TableBotDataStore** and uncomment the line of code that uses **InMemoryDataStore**. The **InMemoryDataStore** is intended for testing and prototyping only.
-->

5. Start the Bot Framework Emulator and connect to your bot as described in the section above. 
6. From the emulator, send your bot a message (e.g.: send the message "Hi"). Execution will stop at the line where you place the breakpoint.

   ![Debug in VS](~/media/bot-service-debug-bot/breakpoint-caught-vs.png)

::: moniker range="azure-bot-service-3.0"

## <a id="debug-csharp-serverless"></a> Debug a Consumption plan C\# Functions bot

The Consumption plan serverless C\# environment in Bot Service has more in common with Node.js than a typical C\# application because it requires a runtime host, much like the Node engine. In Azure, the runtime is part of the hosting environment in the cloud, but you must replicate that environment locally on your desktop. 

### Prerequisites

Before you can debug your Consumption plan C# bot, you must complete these tasks.

- Download the source code for your bot (from Azure), as described in [Set up continuous deployment](bot-service-continuous-deployment.md).
- Download and install the [Bot Framework Emulator](https://aka.ms/Emulator-wiki-getting-started).
- Install the <a href="https://www.npmjs.com/package/azure-functions-cli" target="_blank">Azure Functions CLI</a>.
- Install the <a href="https://github.com/dotnet/cli" target="_blank">DotNet CLI</a>.
  
If you want to be able to debug your code by using breakpoints in Visual Studio 2017, you must also complete these tasks.
  
- Download and install <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> (Community Edition or above).
- Download and install the <a href="https://visualstudiogallery.msdn.microsoft.com/e6bf6a3d-7411-4494-8a1e-28c1a8c4ce99" target="_blank">Command Task Runner Visual Studio Extension</a>.

> [!NOTE]
> Visual Studio Code is not currently supported.

### Debug a Consumption plan C# Functions bot using the emulator

The simplest way to debug your bot locally is to start the bot and then connect to it from Bot Framework Emulator. 
First, open a command prompt and navigate to the folder where the **project.json** file is located in your repository. Then, run the command `dotnet restore` to restore the various packages that are referenced in your bot.

> [!NOTE]
> Visual Studio 2017 changes how Visual Studio handles dependencies. 
> While Visual Studio 2015 uses **project.json** to handle dependencies, 
> Visual Studio 2017 uses a **.csproj** model when loading in Visual Studio. 
> If you are using Visual Studio 2017, download this [**.csproj** file](https://aka.ms/v3-dotnet-debug-csproj)
> to the **/messages** folder in your repository before you run the `dotnet restore` command.

![Command prompt](~/media/bot-service-debug-bot/csharp-azureservice-debug-envconfig.png)

Next, run `debughost.cmd` to load and start your bot. 

![Command prompt run debughost.cmd](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughost.png)

At this point, the bot is running locally. From the console window, copy the endpoint that debughost is listening on (in this example, `http://localhost:3978`). Then, start the Bot Framework Emulator and paste the endpoint into the address bar of the emulator. For this example, you must also append `/api/messages` to the endpoint. Since you do not need security for local debugging, you can leave the **Microsoft App ID** and **Microsoft App Password** fields blank. Click **Connect** to establish a connection to your bot using the specified endpoint.

![Configure emulator](~/media/bot-service-debug-bot/mac-azureservice-emulator-config.png)

After you have connected the emulator to your bot, send a message to your bot by typing some text into the textbox that is located at the bottom of the emulator window (i.e., where **Type your message...** appears in the lower-left corner). By using the **Log** and **Inspector** panels on the right side of the emulator window, you can view the requests and responses as messages are exchanged between the emulator and the bot.

![test via emulator](~/media/bot-service-debug-bot/mac-azureservice-debug-emulator.png)

Additionally, you can view log details in the console window.

![Console window](~/media/bot-service-debug-bot/csharp-azureservice-debug-debughostlogging.png)

::: moniker-end

## Debug a Python  bot using breakpoints in Visual Studio Code

In Visual Studio Code, you can set breakpoints and run the bot in debug mode to step through your code. See also [Create a bot with the Bot Framework SDK for Python](~/python/bot-builder-python-quickstart.md).

1. Launch VS Code and open your bot project folder.
1. Set breakpoints as necessary. You can set breakpoints by hovering your mouse over the column to the left of the line numbers. A small red dot will appear. If you click on the dot, the breakpoint is set. If you click the dot again, the breakpoint is removed.
1. Select the `app.py`.
1. From the menu bar, click **Debug** and click **Start Debugging**.
1. Select **Python File** to debug the currently selected file.

   ![Set breakpoints](~/media/bot-service-debug-bot/bot-debug-python-breakpoints.png)

1. Start the Bot Framework Emulator and connect to your bot as described in the [Debug with the Bot Framework Emulator](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator) article. 
1. From the emulator, send your bot a message (for example, send the message "Hi"). Execution will stop at the line where you place the breakpoint.

   ![Debug in VS Code](~/media/bot-service-debug-bot/bot-debug-python-breakpoint-caught.png)

For more information, see [Debug your Python code](https://aka.ms/bot-debug-python).

## Additional resources

- See [troubleshoot general problems](bot-service-troubleshoot-bot-configuration.md) and the other troubleshooting articles in that section.
- See the how to [Debug with the Emulator](bot-service-debug-emulator.md).

## Next steps

> [!div class="nextstepaction"]
> [Debug your bot using transcript files](v4sdk/bot-builder-debug-transcript.md).
