---
title: Using JavaScript v3 user state in a v4 bot - Bot Service
description:  How to use v3 user state in a v4 bot example
keywords: JavaScript, bot migration, v3 bot
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/14/2019
monikerRange: 'azure-bot-service-4.0'
---

<!-- This article is on hold -->

# Using JavaScript v3 user state in a v4 bot

This article shows an example of how a v4 bot can perform read, write, and delete operations on v3 user state information.

The code sample can be found [here](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot).

> [!NOTE]
> A bot maintains **conversation state** to track and direct the conversation and ask questions to the user. It maintains **user state** to track the user's answers.

## Prerequisites

- npm version 6.9.0 or higher (needed to support package aliasing).

- Node.js version 10.14.1 or higher.

    To check the version installed on your computer, in a terminal, execute the following command:

    ```bash
    # determine node version
    node --version
    ```

## Setup

1. Clone the repository

    ```bash
    git clone https://github.com/microsoft/botbuilder-samples.git
    ```

1. In a terminal, navigate to `BotBuilder-Samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot`

    ```bash
    cd BotBuilder-Samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot
    ```

1. Run `npm install` in the following locations:

    ```bash
    root
    /V4V3StorageMapper
    /V4V3UserState
    ```

1. Execute ``npm run build`` or ``tsc`` to build the `StorageMapper` and `UserState` modules in the following locations:

    ```bash
    /V4V3StorageMapper
    /V4V3UserState
    ```

1. Configure the data base

    1. Copy the content od the `.env.example` file.
    1. Create a new file called `.env` and past the previous content into it. 
    1. Fill in the values for your storage provider(s).
        Notice that *Username*, *password* and *host information* can be found in the Azure portal under the section for your particular storage provider such as *Cosmos DB*, *Table storage* or *SQL database*. Table and collection names are user-defined.
  
1. Set the bot's storage provider

    1. Open the `index.js` file in the project root. Towards the beginning of the file (lines ~38-98) you will see configurations for each storage provider, as noted in the comments. They read in the configuration values from the `.env` file via Node `process.env`. The following code snippet shows how to configure the SQL Database.

        [!code-javascript[Storage configuration](~/../botbuilder-samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot/index.js?range=77-92)]

    1. Specify which storage provider you want your bot to use by passing the storage client instance of your choice to the `StorageMapper` adapter (~line 107).  

        [!code-javascript[StorageMapper](~/../botbuilder-samples/MigrationV3V4/Node/V4V3-user-state-adapter-sample-bot/index.js?range=105-107)]

        The default setting is *Cosmos DB*. The possible values are:

        ```bash
            cosmosStorageClient
            tableStorage
            sqlStorage
        ```

1. Start the application. From the project root, execute the following command:

    ```bash
    npm run start
    ```

## Adapter Classes

### V4V3StorageMapper

The `StorageMapper` class contains the main adapter functionality. It implements the v4 Storage interface and maps the storage provider methods (read, write and delete) back to the v3 storage provider classes so that v3-formatted user state can be used from a v4 bot.

### V4V3UserState

This class extends the v4 `BotState` class (`botbuilder-core`) so that it uses a v3-style key, allowing read, write & delete to v3 storage.

## Testing the bot using Bot Framework Emulator

[Bot Framework Emulator][5] is a desktop application that allows to test and debug a bot on localhost or running remotely through a tunnel.

- Install the Bot Framework Emulator version 4.3.0 or greater from [here][6]

### Connect to the bot using Bot Framework Emulator

1. Launch Bot Framework Emulator.
1. Enter the following URL (end point): `http://localhost:3978/api/messages`.

### Testing steps

1. Open bot in emulator and send a message. Provide your name when prompted.
1. After the turn is over, send another message to the bot.
1. Assure that you are not prompted again for your name. The bot should be reading it from the storage and recognize that it already prompted you.
1. The bot should echo back your message.
1. Go to your storage provider in Azure and verify that your name is stored as user data in the database.

## Deploy the bot to Azure

To learn more about deploying a bot to Azure, see [Deploy your bot to Azure][40] for a complete list of deployment instructions.

## Further reading

- [Azure Bot Service Introduction][21]
- [Bot State][7]
- [Write directly to storage][8]
- [Managing conversation and user state][9]
- [Restify][30]
- [dotenv][31]

[3]: https://aka.ms/botframework-emulator-github
[5]: https://github.com/microsoft/botframework-emulator
[6]: https://github.com/Microsoft/BotFramework-Emulator/releases
[7]: https://docs.microsoft.com/azure/bot-service/bot-builder-storage-concept
[8]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-storage?tabs=javascript
[9]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-state?tabs=javascript
[21]: https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[30]: https://www.npmjs.com/package/restify
[31]: https://www.npmjs.com/package/dotenv
[40]: https://aka.ms/azuredeployment
