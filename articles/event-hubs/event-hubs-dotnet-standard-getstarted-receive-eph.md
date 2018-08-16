---
title: Események fogadása az Azure Event Hubsról a .NET Standard kódtár használatával | Microsoft Docs
description: Üzenetek fogadása az EventProcessorHosttal .NET Standardban – első lépések
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: shvija
ms.openlocfilehash: 40e81058b539b0ad6d642a6f7b22d75304fe5cdf
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002715"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Üzenetek fogadása az Event Processor Hosttal .NET Standardban – első lépések

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

Ez az oktatóanyag ismerteti, hogyan írható olyan .NET Core-konzolalkalmazás, amely egy eseményközpontból fogad üzeneteket az **Event Processor Host** kódtár használatával. Futtathatja a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) megoldást jelenlegi állapotában, kicserélve a sztringekat az eseményközpont és a Storage-fiók értékeire. Vagy létrehozhatja a saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio 2015 vagy 2017](http://www.visualstudio.com). Az oktatóanyag példái a Visual Studio 2017-et használják, de a Visual Studio 2015 is támogatott.
* [.NET Core Visual Studio 2015- vagy 2017-eszközök](http://www.microsoft.com/net/core).
* Azure-előfizetés.
* Azure Event Hubs-névtér és -eseményközpont.
* Egy Azure-tárfiók.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása  

Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az eseményközpont létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md) látható eljárást, majd folytassa az oktatóanyagot.  

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
2. A portál bal oldali navigációs ablaktábláján kattintson az **Erőforrás létrehozása**, a **Tároló**, végül a **Tárfiók** elemre.  
3. Töltse ki a mezőket a tárfiók ablakában, majd kattintson a **Létrehozás** lehetőségre.

    ![Storage-fiók létrehozása][1]

4. Miután megjelenik az **Az üzembe helyezések sikerültek** üzenet, kattintson az új tárfiók nevére. Az **Alapvető erőforrások** ablakban kattintson a **Blobok** elemre. Ha megnyílik a **Blob szolgáltatás** párbeszédpanel, kattintson a **+ Tároló** lehetőségre a tetején. Nevezze el a tárolót, majd zárja be a **Blob szolgáltatás** párbeszédpanelt.  
5. Kattintson a bal oldali ablakban található **Hozzáférési kulcsok** elemre, és másolja a tároló és a tárfiók nevét, valamint a **key1** értékét. Mentse ezeket az értékeket a Jegyzettömbbe vagy egy másik ideiglenes helyre.  

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre. Hozzon létre egy .NET Core-konzolalkalmazást.

![Új projekt][2]

## <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

A következő lépéseket végrehajtva adja hozzá a .NET Standard kódtár [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) és [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) NuGet-csomagjait a projekthez: 

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Tallózás** fülre, keressen rá a **Microsoft.Azure.EventHubs** kifejezésre, majd válassza ki a **Microsoft.Azure.EventHubs** csomagot. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
3. Ismételje meg az 1. és 2. lépést, és telepítse a **Microsoft.Azure.EventHubs.Processor** csomagot.

## <a name="implement-the-ieventprocessor-interface"></a>Az IEvent Processor felület implementálása

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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Az üzenetek fogadásához a SimpleEventProcessor osztályt használó fő konzolmetódus írása

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

Gratulálunk! Sikeresen fogadott üzeneteket egy eseményközpontból az Event Processor Host használatával.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
