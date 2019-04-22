---
title: .NET Core - az Azure Event Hubs használatával események küldéséhez és fogadásához |} A Microsoft Docs
description: Ez a cikk a forgatókönyv létrehozásához, amely elküldi az eseményeket az Azure Event Hubsba a .NET Core-alkalmazást.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: ce98502113e75aea8deef7dbc7363662827634f3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682236"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core"></a>Események küldése vagy események fogadása az Azure Event Hubsba a .NET Core használatával
Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT). Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan hozhat létre a .NET Core-alkalmazások C# eseményeket küldeni, vagy események fogadása az eseményközpontba. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

- [Microsoft Visual Studio 2015 vagy 2017](https://www.visualstudio.com). Az oktatóanyag példái a Visual Studio 2017-et használják, de a Visual Studio 2015 is támogatott.
- [.NET Core Visual Studio 2015- vagy 2017-eszközök](https://www.microsoft.com/net/core). 
- **Event Hubs-névtér és eseményközpont létrehozása**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md). Kérje le a **az eseményközpont-névtér kapcsolati karakterláncára** kövesse a cikkben szereplő: [Kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében használja a kapcsolati karakterláncot.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre egy .NET Core-konzolalkalmazást, küldhet eseményeket egy eseményközpontba. 

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre. Hozzon létre egy .NET Core-konzolalkalmazást.

![Új projekt](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

Adja hozzá a [ `Microsoft.Azure.EventHubs` ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET Core library NuGet-csomagot a projekthez az alábbi lépéseket: 

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Tallózás** fülre, keressen rá a „Microsoft.Azure.EventHubs” kifejezésre, majd válassza ki a **Microsoft.Azure.EventHubs** csomagot. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá az alábbi `using` utasításokat a Program.cs fájl elejéhez:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adjon állandókat a `Program` osztályhoz az Event Hubs kapcsolati sztringjével és entitásútvonalával (egyéni eseményközpontnév). Cserélje le a zárójelben lévő helyőrzőket az eseményközpont létrehozásakor beszerzett megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs connection string}` a névtérszintű kapcsolati sztring, és nem az eseményközpont sztringje. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Adjon hozzá egy új, `MainAsync` nevű metódust a `Program` osztályhoz az alábbiak szerint.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adjon hozzá egy új, `SendMessagesToEventHub` nevű metódust a `Program` osztályhoz az alábbiak szerint.

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adja hozzá a következő kódsort a `Main` metódushoz a `Program` osztályban:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   A Program.cs fájlnak így kell kinéznie.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.

## <a name="receive-events"></a>Események fogadása
Ez a szakasz bemutatja, hogyan írhat egy .NET Core-konzolalkalmazást, amely egy event hub használatával üzeneteket fogad a [Event Processor Host](event-hubs-event-processor-host.md). Az [Event Processor Host](event-hubs-event-processor-host.md) egy .NET-osztály, amely leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az adott Event Hubs-eseményközpontokból. Az Event Processor Host használatával több fogadó között oszthatja el az eseményeket, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az Event Processor Host egyetlen fogadóhoz. A [horizontálisan felskálázott Eseményfeldolgozási](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) minta bemutatja, hogyan használhatja az Event Processor Host több fogadóval.

> [!NOTE]
> Letöltheti ezt a rövid útmutatót mintaként a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), és miután lecserélte az `EventHubConnectionString`, `EventHubName`, `StorageAccountName`, `StorageAccountKey` és `StorageContainerName` sztringeket az eseményközpontja értékeire, futtathatja. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre. Hozzon létre egy .NET Core-konzolalkalmazást.

![Új projekt](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

A következő lépéseket végrehajtva adja hozzá a .NET Standard kódtár [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) és [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) NuGet-csomagjait a projekthez: 

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Tallózás** fülre, keressen rá a **Microsoft.Azure.EventHubs** kifejezésre, majd válassza ki a **Microsoft.Azure.EventHubs** csomagot. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
3. Ismételje meg az 1. és 2. lépést, és telepítse a **Microsoft.Azure.EventHubs.Processor** csomagot.

### <a name="implement-the-ieventprocessor-interface"></a>Az IEvent Processor felület implementálása

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson az **Add** (Hozzáadás) parancsra, majd kattintson a **Class** (Osztály) gombra. Az új osztály neve legyen **SimpleEventProcessor**.

2. Nyissa meg a SimpleEventProcessor.cs fájlt, és adja hozzá a következő `using` utasításokat a fájl elejéhez.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementálja az `IEventProcessor` felületet. Cserélje le a `SimpleEventProcessor` osztály teljes tartalmát a következő kódra:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>A fő metódus frissítése a SimpleEventProcessor használatára

1. Adja hozzá az alábbi `using` utasításokat a Program.cs fájl elejéhez.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Adjon állandókat a `Program` osztályhoz az eseményközpont kapcsolati sztringjével, az eseményközpont nevével, a tárfiók tárolójának nevével, a tárfiók nevével és a tárfiók kulcsával. Adja hozzá a következő kódot a helyőrzőket a megfelelő értékekre cserélve:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Adjon hozzá egy új, `MainAsync` nevű metódust a `Program` osztályhoz az alábbiak szerint.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Adja hozzá a következő kódsort a `Main` metódushoz:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    A Program.cs fájlnak így kell kinéznie:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.


## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkciók és az Azure Event Hubs terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)


