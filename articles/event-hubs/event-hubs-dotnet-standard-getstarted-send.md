---
title: Események küldése és fogadása az Azure Event Hubs-tól a .NET (régi) használatával
description: Ez a cikk egy forgatókönyvet tartalmaz egy .NET Core alkalmazás létrehozásához, amely eseményeket küld/fogad az Azure Event Hubs-ba a régi Microsoft.Azure.EventHubs csomag használatával.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: a8124e9860ba5703e96f96c90bc0cf37e42baf9c
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398358"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Események küldése az Azure Event Hubs-ba vagy fogadhat eseményeket a .NET Core (Microsoft.Azure.EventHubs) használatával
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontba, és hogyan fogadhat eseményeket egy eseményközpontból a **Microsoft.Azure.EventHubs** .NET Core könyvtár használatával.

> [!WARNING]
> Ez a rövid útmutató a régi **Microsoft.Azure.EventHubs** csomagot használja. A legújabb **Azure.Messaging.EventHubs-könyvtárat** használó rövid útmutatót az [Események küldése és fogadása az Azure.Messaging.EventHubs-könyvtár használatával](get-started-dotnet-standard-send-v2.md)című témakörben található. Ha át szeretné helyezni az alkalmazást a régi könyvtárról az újkönyvtárra, olvassa el a [Microsoft.Azure.EventHubs-ról az Azure.Messaging.EventHubs szolgáltatásba való áttelepítés útmutatóját.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)

## <a name="prerequisites"></a>Előfeltételek
Ha most írja be az Azure Event Hubs, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót végezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [.NET Core Visual Studio 2015- vagy 2017-eszközök](https://www.microsoft.com/net/core). 
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az **eseményközpont névterének kapcsolati karakterláncát a** cikk utasításainak követésével kapja meg: [Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A kapcsolati karakterláncot később használja ebben a rövid útmutatóban.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre .NET Core konzolalkalmazást események et egy eseményközpontba. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Másik lehetőségként kövesse az ebben a rövid útmutatóban leírt lépéseket a saját létrehozásához.


### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. A **Fájl** **menüben**kattintson az Új menü **Projekt**parancsára. Hozzon létre egy .NET Core-konzolalkalmazást.

![Új projekt](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

Adja [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) hozzá a .NET Core könyvtár NuGet csomagját a projekthez az alábbi lépésekkel: 

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
    // Uses the event hub client to send 100 messages to the event hub.
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

            // Uses the event hub client to send 100 messages to the event hub.
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
Ez a szakasz azt mutatja be, hogy miként írható meg egy .NET Core konzolalkalmazás, amely üzeneteket fogad egy eseményközpontból az [Eseményprocesszor-állomás](event-hubs-event-processor-host.md)használatával. Az [Eseményfeldolgozó-állomás](event-hubs-event-processor-host.md) egy .NET osztály, amely az állandó ellenőrzőpontok és az ilyen eseményközpontokból érkező párhuzamos beérkezések kezelésével egyszerűsíti az események fogadását. Az Event Processor Host használatával több fogadó között oszthatja el az eseményeket, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az Event Processor Host egyetlen fogadóhoz.
> [!NOTE]
> Ezt a rövid útmutatót mintaként töltheti `EventHubConnectionString` le `EventHubName` `StorageAccountName`a `StorageAccountKey` `StorageContainerName` [GitHubról,](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)replace and , , , és karakterláncokat az eseményközpont értékeivel, és futtathatja azt. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. A **Fájl** **menüben**kattintson az Új menü **Projekt**parancsára. Hozzon létre egy .NET Core-konzolalkalmazást.

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

- [Szerepköralapú hozzáférés-vezérlési (RBAC) minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft.Azure.EventHubs-könyvtárat** használják, de könnyedén frissítheti azt a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával. Ha át szeretné helyezni a mintát a régi könyvtár használatából az újkönyvtárba, olvassa el a [Microsoft.Azure.EventHubs szolgáltatásból az Azure.Messaging.EventHubs szolgáltatásba való áttelepítés útmutatóját.)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)


