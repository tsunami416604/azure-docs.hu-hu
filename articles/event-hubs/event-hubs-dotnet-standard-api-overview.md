---
title: Az Azure Event Hubs .NET Standard API-k áttekintése |} A Microsoft Docs
description: .NET standard API – áttekintés
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: f8cd167d8de738674939180e0d81fc9282434155
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450454"
---
# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET Standard API – áttekintés

Ez a cikk összefoglaltuk a kulcsot az Azure Event Hubs [.NET Standard ügyféloldali API-k](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Jelenleg két .NET Standard klienskódtárak Event hubs:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Minden modul alapszintű műveleteket biztosít.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Hozzáadja a további funkciókat, amely lehetővé teszi, hogy a feldolgozott események szerinti nyomon követést és legegyszerűbb módja egy eseményközpontba olvasni.

## <a name="event-hubs-client"></a>Event Hubs-ügyfél

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) az elsődleges objektum eseményeket küldeni, hozzon létre a fogadók és futásidejű adatokat lekérdezni. Ez az ügyfél kapcsolódik egy adott eseményközpontban, és létrehoz egy új kapcsolatot az Event Hubs-végpontra.

### <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

Egy [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) objektum létrehozása egy kapcsolati karakterláncból. Hozza létre az új ügyfél legegyszerűbb módja a következő példában látható:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

A kapcsolati karakterláncot programozott módon szerkeszteni, használhatja a [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) osztályt, és továbbíthatja azt a kapcsolati karakterlánc paramétereként [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Események küldése

Események küldése eseményközpontba, használja a [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) osztály. A szervezetnek kell lennie egy `byte` tömböt, vagy egy `byte` tömb szegmens.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Események fogadása

Események fogadása az Event Hubs javasolt módját használja a [Event Processor Host](#event-processor-host-apis), automatikusan nyomon követéséhez az event hub eltolás és a partíció információ funkciókat biztosítja. Vannak azonban bizonyos helyzetekben, amelyben az események fogadása az Event Hubs alap függvénytár rugalmasságát használatával érdemes.

#### <a name="create-a-receiver"></a>Fogadó létrehozása

Fogadók kötődnek, adott partíciókra, így minden események fogadásához az eseményközpontokban, több példányt kell létrehoznia. Tanácsos a partíciós adatok programozott módon, lekéréséhez fix kódolása a partíció azonosítók helyett. Ehhez használhatja a [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) metódust.

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

Mivel események soha nem törlődnek az eseményközpontok (és csak jár le), meg kell adnia a megfelelő kiindulási pont. Az alábbi példa bemutatja a lehetséges kombinációkra:

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

## <a name="event-processor-host-apis"></a>Event Processor állomás API-k

Ezen API-k rugalmasságot biztosítanak a munkavégző folyamatok, elérhetetlenné válhatnak a, partíciók osztja meg a rendelkezésre álló feldolgozók között:

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

A következő egy minta megvalósítását a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) felületen:

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

* [Mi az Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Elérhető Event Hubs API-k](event-hubs-api-overview.md)

A .NET API-referenciák jelenleg itt tart:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)