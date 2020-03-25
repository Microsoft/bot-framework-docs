---
title: Using .NET v3 user state in a v4 bot - Bot Service
description: How to use v3 user state in a v4 bot example
keywords: Csharp, bot migration, v3 bot
ms.author: kamrani
manager: kamrani
ms.topic: article
ms.service: bot-service
ms.date: 08/21/2019
monikerRange: 'azure-bot-service-4.0'
---

# Using .NET v3 user state in a v4 bot

This article shows an example of how a v4 bot can perform read, write, and delete operations on v3 user state information.
The bot maintains conversation state using `MemoryStorage` to track and direct the conversation while asking the user questions.  It maintains the **user state** in the v3 format to track the user's answers by utilizing a custom `IStorage` class called `V3V4Storage`.  One of the arguments to this class is an `IBotDataStore`. The v3 SDK code base was copied into `Bot.Builder.Azure.V3V4` and contains all three v3 SDK storage providers (Azure Sql, Azure Table, and Cosmos Db).  The intent is to allow the existing v3 **user state** to be brought into a migrated v4 bot.

The code sample can be found [here](https://github.com/microsoft/BotBuilder-Samples/tree/master/MigrationV3V4/CSharp/V4StateBotFromV3Providers).

## Prerequisites

- [.NET Core SDK](https://dotnet.microsoft.com/download) version 2.1

    ```bash
    # determine dotnet version
    dotnet --version
    ```

## Setup

1. Clone the repository

    ```bash
    git clone https://github.com/microsoft/botbuilder-samples.git
    ```

1. In a terminal, navigate to `MigrationV3V4/CSharp/V4StateBotFromV3Providers`

    ```bash
    cd BotBuilder-Samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers
    ```

1. Run the bot from a terminal or from Visual Studio, choose option A or B.

    - From a terminal

        ```bash
        # run the bot
        dotnet run
        ```

    - Or from Visual Studio

        - Launch Visual Studio File -> Open -> Project/Solution
        - Navigate to `BotBuilder-Samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers` folder
        - Select `V4StateBot.sln` file
        - Press F5 to run the project


## Storage Provider setup

It is assumed that you have an existing v3 state store configured and in use. In this case, setting up this example to use the existing state store simply involve adding the storage provider's connection information to `web.config` file as shown next.

- Cosmos DB

```json
  "v3CosmosEndpoint": "https://yourcosmosdb.documents.azure.com:443/",
  "v3CosmosKey": "YourCosmosDbKey",
  "v3CosmosDatataseName": "v3botdb",
  "v3CosmosCollectionName": "v3botcollection",
```

- Azure Sql

```json
 "ConnectionStrings": {
    "SqlBotData": "Server=YourServer;Initial Catalog=BotData;Persist Security Info=False;User ID=YourUserName;Password=YourUserPassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=True;Connection Timeout=30;"
  },
```

- Azure Table

```json
 "ConnectionStrings": {
    "AzureTable": "DefaultEndpointsProtocol=https;AccountName=YourAccountName;AccountKey=YourAccountKey;EndpointSuffix=core.windows.net"
  },
```

- Set the bot's storage provider

    Open the `Startup.cs` file in the `V4V3StateBot` project root. Towards the middle of the file (lines ~52-76) you will see configurations for each storage provider. They read in the config values from the web.config. 

    [!code-csharp[Storage configuration](~/../botbuilder-samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers/V4V3StateBot/Startup.cs?range=52-76)]

    Specify which storage provider you want your bot to use by un-commenting the corresponding lines for the instance of your choice. Once the provider is properly configured, assure that the provider class is passed to `V3V4Storage` (lines ~72-75). 

    [!code-csharp[Storage provider](~/../botbuilder-samples/MigrationV3V4/CSharp/V4StateBotFromV3Providers/V4V3StateBot/Startup.cs?range=72-75)]

    Cosmos DB (formerly Document DB) is set by default. The possible values are:

    ```bash
    documentDbBotDataStore
    tableBotDataStore
    tableBotDataStore2
    sqlBotDataStore
    ```

- Start the application. 

## V3V4 Storage and State Classes

### V3V4Storage

The `V3V4Storage` class contains the main storage mapping functionality. It implements the v4 `IStorage` interface and maps the storage provider methods (read, write and delete) back to the v3 storage provider classes so that v3-formatted user state can be used from a v4 bot.

### V3V4State

This class inherits from the v4 `BotState` class, and uses a v3-style key (`IAddress`). This allows reads, writes & deletes to v3 storage in the same way V3 state storage has always worked.


## Testing the bot using Bot Framework Emulator

[Bot Framework Emulator][5] is a desktop application that allows bot developers to test and debug their bots on localhost or running remotely through a tunnel.

- Install the Bot Framework Emulator version 4.3.0 or greater from [here][6]


### Connect to the bot using Bot Framework Emulator

- Launch Bot Framework Emulator
- File -> Open Bot
- Enter a Bot URL of `http://localhost:3978/api/messages`


## Further reading

- [Azure Bot Service Introduction][21]
- [Bot State][7]
- [Write directly to storage][8]
- [Managing conversation and user state][9]

[3]: https://aka.ms/botframework-emulator-github
[5]: https://github.com/microsoft/botframework-emulator
[6]: https://github.com/Microsoft/BotFramework-Emulator/releases
[7]: https://docs.microsoft.com/azure/bot-service/bot-builder-storage-concept
[8]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-storage?tabs=csharp
[9]: https://docs.microsoft.com/azure/bot-service/bot-builder-howto-v4-state?tabs=csharp
[21]: https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0
[40]: https://aka.ms/azuredeployment
