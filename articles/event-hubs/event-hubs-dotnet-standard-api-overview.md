---
title: "Az Azure Event Hubs .NET-szabvány API-k áttekintése |} Microsoft Docs"
description: ".NET-szabvány API – áttekintés"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: eea682c40cd415b383a8b2f0004a5f3648e2f01f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET-szabvány API – áttekintés
Ez a cikk foglal össze néhányat a kulcs Event Hubs .NET szabványos ügyfél API-k. Jelenleg két .NET-szabvány klienskódtárak segítségével:
* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
  *  Ezt a szalagtárat biztosít minden alapvető futásidejű műveletet.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
  * Ebben a könyvtárban, amely lehetővé teszi, hogy nyomon követése céljából feldolgozott események, és a legegyszerűbb módja az eseményközpontban lévő olvasható további funkciókat ad hozzá.

## <a name="event-hubs-client"></a>Event Hubs-ügyfél
[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) küldi az eseményeket, a fogadók létrehozása, és futásidejű adatokat lekérni a elsődleges objektum. Ez az ügyfél kapcsolódik egy adott eseményközpont, és új kapcsolatot hoz létre az Event Hubs-végponthoz.

### <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása
Egy [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) objektum létrehozása egy kapcsolati karakterláncból. A legegyszerűbben úgy hozható létre egy új ügyfél az alábbi példában látható:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hubs connection string}");
```

Programozott módon szerkessze a kapcsolati karakterláncot, használja a [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) osztály, és adja át a kapcsolati karakterlánc paramétereként [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hubs connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Események küldése
Események küldése az eseményközpont, használja a [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) osztály. A szervezetnek kell lennie egy `byte` tömb, vagy egy `byte` tömb szegmens.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Események fogadása
Az ajánlott módszer a események fogadásához az Event Hubs-t használja a [Event Processor Host](#event-processor-host-apis), amely olyan funkciókat biztosít, automatikusan nyomon követheti, eltolás és a partíciónak az adatait. Vannak azonban olyan helyzetekben, ahol lehetséges, hogy használni kívánt az Event Hubs Alapkönyvtár rugalmasan események fogadásához.

#### <a name="create-a-receiver"></a>Fogadó létrehozása
Fogadók vannak társítva adott partíciókra, így minden események fogadásához az eseményközpontban, több példány létrehozásához szüksége lesz. Általánosan fogalmazva akkor a partícióazonosító adatainak megszerzése programozottan, ahelyett, hogy a rögzített megadás a partíciók azonosítóit bevált gyakorlat. Ehhez használhatja a [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) metódust.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition ids
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Mivel események soha nem törlődnek az eseményközpontok (és csak lejár), meg kell adnia a megfelelő kiindulási pontot. A következő példa bemutatja kombináció.

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Egy esemény felhasználása
```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>Esemény processzor állomás API-k
Ezen API-k munkavégző folyamatokat, amelyek egyaránt elérhetetlenné válhatnak, partíciók keresztül elérhető munkavállalók elosztásával rugalmasságot biztosítanak.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes of the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Az alábbiakban egy minta végrehajtását a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

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

## <a name="next-steps"></a>Következő lépések
Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Mi az Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Rendelkezésre álló Event Hubs API-k](event-hubs-api-overview.md)

A .NET API-hivatkozások jelenleg itt:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)