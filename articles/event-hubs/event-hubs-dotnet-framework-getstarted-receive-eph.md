---
title: Események fogadása az Azure Event Hubsból a .NET-keretrendszer használatával | Microsoft Docs
description: Események az Azure Event Hubsból .NET-keretrendszer segítségével történő fogadásához kövesse az oktatóanyag lépéseit.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 9e94357216690438446a738400c979d12f387df6
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471084"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Események fogadása az Azure Event Hubsból a .NET-keretrendszer használatával

## <a name="introduction"></a>Introduction (Bevezetés)

Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT). Az Event hubs részletes ismertetőt talál [Event Hubs – áttekintés](event-hubs-about.md) és [Event Hubs-szolgáltatások](event-hubs-features.md).

Ez az oktatóanyag bemutatja, hogyan Konzolalkalmazás létrehozása .NET-keretrendszer, amely egy event hub használatával üzeneteket fogad a [Event Processor Host](event-hubs-event-processor-host.md). A [Event Processor Host](event-hubs-event-processor-host.md) egy .NET-osztály, amely leegyszerűsíti az események fogadását az event hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az adott event hubs eseményközpontokból. Az Event Processor Host használatával, akkor is feloszthatja az eseményeket több fogadóra, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az Event Processor Host egyetlen fogadóhoz. A [horizontálisan felskálázott Eseményfeldolgozási] [ Scale out Event Processing with Event Hubs] minta bemutatja, hogyan használhatja az Event Processor Host több fogadóval.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [A Microsoft Visual Studio 2017-es vagy újabb](http://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket ebben az oktatóanyagban.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Hozzon létre egy új Visual C# asztalialkalmazás-projektet a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek a **Receiver** (Fogadó) nevet.
   
![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, majd kattintson a **Manage NuGet Packages for Solution** (Megoldás NuGet-csomagjainak kezelése) parancsra.
2. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    A Visual Studio letölti és telepíti az [Azure Service Bus Event Hub - EventProcessorHost NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) minden függőségével együtt, és hozzáad egy rá mutató hivatkozást is.

## <a name="implement-the-ieventprocessor-interface"></a>Az IEvent Processor felület implementálása

1. Kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, kattintson az **Add** (Hozzáadás) lehetőségre, majd a **Class** (Osztály) elemre. Nevezze el az új osztályt **SimpleEventProcessor** névre, és kattintson az **Add** (Hozzáadás) gombra az osztály létrehozásához.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Adja hozzá a következő utasításokat a SimpleEventProcessor.cs fájl elejéhez:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Helyettesítse be a szervezet az osztály alábbi kódjában:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Az **EventProcessorHost** ezt az osztályt hívja meg az eseményközpontból fogadott események feldolgozásához. A `SimpleEventProcessor` osztály stoppert használ az ellenőrzőpont-metódus **EventProcessorHost** környezetben való rendszeres időközönkénti hívásához. Ez az eljárás biztosítja, hogy ha a fogadó újraindul, ne vesszen el öt percnél több feldolgozási munka.

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Frissítés a Main metódushoz SimpleEventProcessor használata

1. A **Program** osztályban adja hozzá a következő `using` utasítást a fájl elejéhez:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Cserélje le a `Main` metódus az a `Program` osztályt a következő kódra, és cserélje le az eseményközpont neve és a korábban mentett névtérszintű kapcsolati karakterlánccal és a storage-fiók és a korábbi szakaszokban ismertetett másolt kulcsot. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.
  
Gratulálunk! Sikeresen fogadott üzeneteket egy eseményközpontból az Event Processor Host használatával.


> [!NOTE]
> Ez az oktatóprogram az [EventProcessorHost](event-hubs-event-processor-host.md) egyetlen példányát használja. Átviteli sebesség növelése érdekében azt javasoljuk, hogy több példányának futtatása [EventProcessorHost](event-hubs-event-processor-host.md), ahogyan az a [horizontálisan felskálázott Eseményfeldolgozási](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) minta. Ezekben az esetekben a több példányok automatikusan koordinálnak egymással terheléselosztás a fogadott események. 

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban létrehozott .NET-keretrendszer alkalmazás, amely a fogadott üzeneteket egy eseményközpontból. Ismerje meg, hogyan küldhet eseményeket egy eseményközpontba, .NET-keretrendszer használatával, lásd: [események küldése az event hubs – .NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
