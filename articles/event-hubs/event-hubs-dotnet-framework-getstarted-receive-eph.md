---
title: "Események fogadása az Azure Event Hubsból a .NET-keretrendszer használatával | Microsoft Docs"
description: "Események az Azure Event Hubsból .NET-keretrendszer segítségével történő fogadásához kövesse az oktatóanyag lépéseit."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6c309a14e00324a9335bde61fe175ec3906c066d
ms.lasthandoff: 04/18/2017


---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Események fogadása az Azure Event Hubsból a .NET-keretrendszer használatával

## <a name="introduction"></a>Introduction (Bevezetés)
Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT).

Ez az oktatóanyag ismerteti, hogyan írható olyan .NET-keretrendszerbeli konzolalkalmazás, amely egy eseményközpontból fogad üzeneteket az **[Event Processor Host][EventProcessorHost]** használatával. Az események a .NET-keretrendszerrel való küldéséről lásd a [Események küldése az Azure Event Hubsba a .NET-keretrendszer használatával](event-hubs-dotnet-framework-getstarted-send.md) című cikket, vagy kattintson a megfelelő küldőnyelvre a bal oldalon található tartalomjegyzékben.

Az [Event Processor Host][EventProcessorHost] egy .NET-osztály, amely leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az adott Event Hubs -eseményközpontokból. Az [Event Processor Host][Event Processor Host] használatával több fogadó között oszthatja el az eseményeket, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az [Event Processor Host][EventProcessorHost] egyetlen fogadóhoz. A [horizontálisan felskálázott eseményfeldolgozási][Scale out Event Processing with Event Hubs] minta megmutatja, hogyan használható az [Event Processor Host][EventProcessorHost] több fogadóval.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

* [Microsoft Visual Studio 2015 vagy újabb](http://visualstudio.com). A jelen oktatóanyag képernyőképei a Visual Studio 2017-et használják.
* Aktív Azure-fiók. Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása

Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az eseményközpont létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md) látható eljárást, majd folytassa a következő lépésekkel.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Az [Event Processor Host][EventProcessorHost] használatához egy [Azure Storage-fiók][Azure Storage account] szükséges:

1. Jelentkezzen be az [Azure Portalra][Azure portal], és kattintson az **Új** gombra a képernyő bal felső részén.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. A **Tárfiókok létrehozása** panelen írja be a tárfiók nevét. Válassza ki azt az Azure-előfizetést, erőforráscsoportot és helyet, amellyel az erőforrást létre kívánja hozni. Ezt követően kattintson a **Create** (Létrehozás) gombra.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. A tárfiókok listáján kattintson az újonnan létrehozott tárfiókra.
5. A tárfiók panelen kattintson a **Hívóbetűk** elemre. Másolja ki a **key1** értékét – erre később lesz szükség az oktatóprogramban.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)
6. Hozzon létre egy új Visual C# asztalialkalmazás-projektet a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek a **Receiver** (Fogadó) nevet.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
7. A Megoldáskezelőben kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, majd kattintson a **Manage NuGet Packages for Solution** (Megoldás NuGet-csomagjainak kezelése) parancsra.
8. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    A Visual Studio letölti és telepíti az [Azure Service Bus Event Hub - EventProcessorHost NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) minden függőségével együtt, és hozzáad egy rá mutató hivatkozást is.
9. Kattintson a jobb gombbal a **Receiver** (Fogadó) projektre, kattintson az **Add** (Hozzáadás) lehetőségre, majd a **Class** (Osztály) elemre. Nevezze el az új osztályt **SimpleEventProcessor** névre, és kattintson az **Add** (Hozzáadás) gombra az osztály létrehozásához.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
10. Adja hozzá a következő utasításokat a SimpleEventProcessor.cs fájl elejéhez:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     using System.Diagnostics;
     ```
    
     Ezután helyettesítse be a következő kódot az osztály törzseként:
    
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
    
     Ezt az osztályt fogja meghívni az **EventProcessorHost** az eseményközpontból fogadott események feldolgozásához. Vegye figyelembe, hogy a `SimpleEventProcessor` osztály stoppert használ az ellenőrzőpont-módszer hívásához az **EventProcessorHost** környezetben. Ez biztosítja, hogy ha a fogadó újraindul, nem vész el öt percnél több feldolgozási munka.
11. A **Program** osztályban adja hozzá a következő `using` utasítást a fájl elejéhez:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     ```
    
     Ezután cserélje le a `Main` metódust a `Program` osztályban a következő kódra, behelyettesítve az eseményközpont nevét, a korábban mentett névtérszintű kapcsolati karakterláncot, valamint a tárfiókot és a kulcsot, amelyeket a korábbi szakaszokban lemásolt. 
    
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

12. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.
  
Gratulálunk! Sikeresen fogadott üzeneteket egy eseményközpontból az Event Processor Host használatával.


> [!NOTE]
> Ez az oktatóprogram az [EventProcessorHost][EventProcessorHost] egyetlen példányát használja. Az átviteli sebesség növelése érdekében ajánlott az [EventProcessorHost][EventProcessorHost] több példányának futtatása, amelyre a horizontálisan felskálázott eseményfeldolgozási mintában láthat példát. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló nevet adni az [EventProcessorHost][EventProcessorHost]-példányoknak. További információt ezekről a témákról az [Event Hubs áttekintésében][Event Hubs Overview] és az [Event Hubs programozási útmutatójában][Event Hubs Programming Guide] találhat.
> 
> 

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]: ../storage/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Következő lépések
Létrehozott egy működő alkalmazást, amely létrehoz egy Event Hubot, valamint adatokat fogad és küld. További információkért kövesse az alábbi hivatkozásokat:

* [Event Processor Host](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs – áttekintés][Event Hubs overview]
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
