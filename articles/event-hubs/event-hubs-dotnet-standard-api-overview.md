---
title: Az Azure Event Hubs .NET standard API-k áttekintése | Microsoft dokumentumok
description: .NET Standard API – áttekintés
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60821909"
---
# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET Standard API – áttekintés

Ez a cikk összefoglalja az Azure Event Hubs [.NET Standard ügyfélAPI-k](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)néhány kulcsfontosságú szolgáltatását. Jelenleg két .NET Standard ügyféltár van az Event Hubs-hoz:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Az összes alapvető futásidejű műveletet biztosítja.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): További funkciókat ad hozzá, amelyek lehetővé teszik a feldolgozott események nyomon követését, és a legegyszerűbb módja az eseményközpontból történő olvasásnak.

## <a name="event-hubs-client"></a>Eseményközpontok ügyfele

[Az EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) az elsődleges objektum, amelyet események küldésére, fogadók létrehozására és futásidejű információk leésre használ. Ez az ügyfél egy adott eseményközponthoz kapcsolódik, és új kapcsolatot hoz létre az Event Hubs végponthoz.

### <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

[Az EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) objektum kapcsolati karakterláncból jön létre. Az új ügyfél példányosításlegegyszerűbb módja a következő példában látható:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

A kapcsolati karakterlánc programozott szerkesztéséhez használhatja az [EventHubsConnectionStringBuilder osztályt,](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) és paraméterként adhatja át a kapcsolati karakterláncot az [EventHubClient.CreateFromConnectionString paraméternek.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Események küldése

Események eseményközpontba küldéséhez használja az [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) osztályt. A törzsnek `byte` tömbnek vagy `byte` tömbszegmensnek kell lennie.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Események fogadása

Az Event Hubs-tól érkező események fogadásának ajánlott módja az [Eseményprocesszor-állomás](#event-processor-host-apis)használata, amely az eseményközpont eltolási és partícióinformációinak automatikus nyomon követésére szolgál. Vannak azonban bizonyos helyzetek, amelyekben érdemes lehet használni a rugalmasságot az alapvető Event Hubs könyvtár események fogadására.

#### <a name="create-a-receiver"></a>Fogadó létrehozása

A fogadók adott partíciókhoz vannak kötve, így ahhoz, hogy egy eseményközpontban az összes eseményt megkapja, több példányt kell létrehoznia. Ez egy jó gyakorlat, hogy a partíció adatait programozott módon, ahelyett, hogy a partíció azonosítók kódolása. Ehhez használhatja a [GetRuntimeInformationAsync metódust.](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Mivel az események soha nem törlődnek az eseményközpontból (és csak lejárnak), meg kell adnia a megfelelő kiindulási pontot. A következő példa a lehetséges kombinációkat mutatja be:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Esemény felhasználása

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

## <a name="event-processor-host-apis"></a>Eseményprocesszor-gazda API-k

Ezek az API-k rugalmasságot biztosítanak a rendelkezésre álló dolgozók közötti partíciók elosztásával elérhetetlenné vált munkavégző folyamatok számára:

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

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Az alábbiakban az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felület mintaimplementációja látható:

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

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Mi az az Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Elérhető eseményközpontok apis](event-hubs-api-overview.md)

A .NET API-hivatkozások itt találhatók:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
