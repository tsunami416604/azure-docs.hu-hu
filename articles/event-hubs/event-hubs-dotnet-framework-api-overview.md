---
title: Az Azure Event Hubs .NET-keretrendszer API-k áttekintése |} A Microsoft Docs
description: Néhány kulcsfontosságú Event Hubs .NET-keretrendszer ügyfél API-k összegzése.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: dd915671c8b7d51595fea520187b917005f5dc53
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006443"
---
# <a name="event-hubs-net-framework-api-overview"></a>Event Hubs .NET-keretrendszer API – áttekintés

Ez a cikk összefoglaltuk a kulcsot az Azure Event Hubs [.NET-keretrendszer ügyfél API-k](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Két kategóriába sorolhatók: felügyeleti és a futtatókörnyezeti API-k. Futási idő API-k állnak küldeni és fogadni, egy üzenet szükséges összes műveletet. Felügyeleti műveletek lehetővé teszik az Event Hubs-entitás állapot létrehozása, frissítése és törlése entitások kezelését.

[Figyelési forgatókönyvek](event-hubs-metrics-azure-monitor.md) felügyeleti és a futásidejű is kiterjednek. Részletes útmutató a .NET API-k dokumentációjáért lásd: a [.NET-keretrendszer](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs), és [EventProcessorHost API](/dotnet/api/microsoft.azure.eventhubs.processor) hivatkozik.

## <a name="management-apis"></a>Felügyeleti API-k

A következő felügyeleti műveletek végrehajtásához rendelkeznie kell **kezelés** engedélyek az Event Hubs-névtér:

### <a name="create"></a>Létrehozás

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Frissítés

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Törlés

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Futási idő API-k
### <a name="create-publisher"></a>Közzétevő létrehozása

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Üzenet közzététele

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Fogyasztói létrehozása

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Üzenet használata

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Event Processor állomás API-k

Ezen API-k rugalmasságot biztosítanak a munkavégző folyamatokat, amelyek a partíciók osztja meg a rendelkezésre álló feldolgozók között nem érhető el, válhat.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

A [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) kapcsolat a következőképpen van meghatározva:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Mi az Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)

A .NET API-referenciák jelenleg itt tart:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
