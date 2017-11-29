---
title: "Események fogadása az Azure Event Hubs .NET szabványos függvénytár használatával |} Microsoft Docs"
description: "Ismerkedés az EventProcessorHost üzenetek fogadása a .NET-szabvány"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: a88b5da8fa504e0528caa7fa212d4cec26d1cf66
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Az Event Processor Host üzenetek fogadása a .NET-szabvány első lépései

> [!NOTE]
> Ez a minta érhető el a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

Ez az oktatóanyag bemutatja, hogyan írhat egy .NET Core-konzolalkalmazást, amely üzeneteket fogad az eseményközpontban lévő használatával a **Event Processor Host** könyvtár. Futtathatja a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) megoldás szerint-van, a karakterláncok cseréje a event hub és a tárolási fiók értékek. Vagy a lépésekkel ebben az oktatóanyagban saját.

## <a name="prerequisites"></a>Előfeltételek

* [A Microsoft Visual Studio 2015-öt vagy 2017](http://www.visualstudio.com). A példák a Visual Studio 2017 oktatóanyag használja, de a Visual Studio 2015-öt is támogatott.
* [A .NET core Visual Studio 2015-öt vagy 2017 eszközök](http://www.microsoft.com/net/core).
* Azure-előfizetés.
* Az Azure Event Hubs névtér.
* Egy Azure Storage-fiókot.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása  

Az első lépés az, hogy használja a [Azure-portálon](https://portal.azure.com) az Event Hubs típus névtér létrehozása, és szerezze be a felügyeleti hitelesítő adatokat az alkalmazásban az event hubs folytatott kommunikációhoz szükséges. Egy névtér és az event hub létrehozásához kövesse a [Ez a cikk](event-hubs-create.md), majd folytassa az oktatóanyag.  

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. A portál bal oldali navigációs ablaktábláján kattintson **új**, kattintson a **tárolási**, és kattintson a **Tárfiók**.  
3. Töltse ki a mezőket a tárolási fiók ablakban, és kattintson a **létrehozása**.

    ![Tárfiók létrehozása][1]

4. Miután meggyőződött arról a **központi telepítések sikeres** üzenetet, kattintson az új tárfiók neve. Az a **Essentials** ablak, kattintson a **Blobok**. Ha a **Blob szolgáltatás** párbeszédpanel, kattintson a **+ tároló** tetején. Nevezze el a tárolót, és zárja be a **Blob szolgáltatás**.  
5. Kattintson a **hívóbetűk** a bal oldali ablak és a példány nevét és a tároló, a tárfiók, értékének **key1**. Ezeket az értékeket a Jegyzettömb vagy más ideiglenes helyre mentse.  

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre. A .NET Core Konzolalkalmazás létrehozása.

![Új projekt][2]

## <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

Adja hozzá a [ **Microsoft.Azure.EventHubs** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) és [ **Microsoft.Azure.EventHubs.Processor** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET-szabvány library NuGet-csomagok a projekt a következő lépések végrehajtásával: 

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Tallózás** lapra, keressen **Microsoft.Azure.EventHubs**, majd válassza ki a **Microsoft.Azure.EventHubs** csomag. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
3. Ismételje meg az 1. és 2, és telepítse a **Microsoft.Azure.EventHubs.Processor** csomag.

## <a name="implement-the-ieventprocessor-interface"></a>A IEventProcessor illesztőfelület megvalósítása

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, és kattintson a **osztály**. Az új osztály neve **SimpleEventProcessor**.

2. Nyissa meg a SimpleEventProcessor.cs fájl, és adja hozzá a következő `using` utasítást, hogy a fájl elejéhez.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Alkalmazzon a `IEventProcessor` felületet. Cserélje le a teljes tartalmát a `SimpleEventProcessor` osztály az alábbi kódra:

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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>A fő konzolon metódus írása, amely a SimpleEventProcessor osztály üzenetek fogadásához használt

1. Adja hozzá az alábbi `using` utasításokat a Program.cs fájl elejéhez.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Adja hozzá a állandók a `Program` osztály a event hub kapcsolati karakterlánc, a eseményközpont neve, a tárfiók tároló neve, a tárfiók neve és a tárfiók kulcsa. Adja hozzá a következő kódot, a helyőrzők cseréje a hozzájuk tartozó értékek.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Nevű új módszer `MainAsync` számára a `Program` osztály, az alábbiak szerint:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
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

3. Adja hozzá a következő kódsort a `Main` módszert:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    A Program.cs fájlnak így kell kinéznie:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
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
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
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

Gratulálunk! Most kapott üzenetek eseményközpontban az Event Processor Host használatával.

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
