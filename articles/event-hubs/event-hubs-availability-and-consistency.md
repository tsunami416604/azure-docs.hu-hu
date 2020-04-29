---
title: Rendelkezésre állás és konzisztencia – Azure Event Hubs | Microsoft Docs
description: Hogyan biztosítható a maximális rendelkezésre állás és konzisztencia az Azure Event Hubs partíciók használatával.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2020
ms.author: shvija
ms.openlocfilehash: 0546adb6131479a8f5d2e7e31819483200586839
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80397339"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Rendelkezésre állás és konzisztencia az Event Hubsban

## <a name="overview"></a>Áttekintés
Az Azure Event Hubs [particionáló modellt](event-hubs-scalability.md#partitions) használ a rendelkezésre állás és a párhuzamos egyetlen Event hub-n belüli fejlesztéséhez. Ha például egy Event hub négy partícióval rendelkezik, és az egyik partíció az egyik kiszolgálóról a másikra kerül egy terheléselosztási műveletben, akkor továbbra is küldhet és fogadhat három további partíciót. Emellett a további partíciók lehetővé teszik, hogy több egyidejű olvasó dolgozza fel az adatokat, és ezzel javítva legyen az összesített átviteli sebesség. Az elosztott rendszerekben a particionálás és a rendezés következményeinek megértése a megoldás kialakításának kritikus aspektusa.

A rendezés és a rendelkezésre állás közötti kompromisszum elmagyarázása érdekében tekintse meg a [Cap-tétel](https://en.wikipedia.org/wiki/CAP_theorem)(más néven Brewer-tétel) című témakört. Ez a tétel a konzisztencia, a rendelkezésre állás és a partíciós tolerancia közötti választást ismerteti. Ez azt jelzi, hogy a hálózatban particionált rendszerek esetében mindig fennáll a konzisztencia és a rendelkezésre állás közötti kompromisszum.

A Brewer-tétel a következőképpen határozza meg a következetességet és a rendelkezésre állást:
* Partíciós tolerancia: az adatfeldolgozó rendszer azon képessége, hogy továbbra is dolgozza fel az adatfeldolgozást, még akkor is, ha a partíció meghibásodik.
* Rendelkezésre állás: egy nem meghibásodott csomópont ésszerű választ ad vissza ésszerű időn belül (hiba vagy időtúllépés nélkül).
* Konzisztencia: az olvasás garantált, hogy egy adott ügyfél legutóbbi írását adja vissza.

## <a name="partition-tolerance"></a>Partíciós tolerancia
A Event Hubs particionált adatmodellre épül. A telepítés során beállíthatja a partíciók számát az Event hub-ban, de később nem módosíthatja ezt az értéket. Mivel a partíciókat Event Hubs használatával kell használnia, döntéseket kell hoznia az alkalmazás rendelkezésre állásával és konzisztenciájával kapcsolatban.

## <a name="availability"></a>Rendelkezésre állás
A Event Hubs használatának legegyszerűbb módja az alapértelmezett viselkedés használata. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 vagy újabb)](#tab/latest)
Ha létrehoz egy új **[EventHubProducerClient](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** -objektumot, és a **[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** metódust használja, az események automatikusan el lesznek osztva az Event hub partíciói között. Ez a viselkedés lehetővé teszi a legnagyobb mennyiségű időt.

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 vagy korábbi)](#tab/old)
Ha létrehoz egy új **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objektumot, és a **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metódust használja, az események automatikusan el lesznek osztva az Event hub partíciói között. Ez a viselkedés lehetővé teszi a legnagyobb mennyiségű időt.

---

A maximális Felskálázási időt igénylő használati esetekben ez a modell javasolt.

## <a name="consistency"></a>Konzisztencia
Bizonyos helyzetekben fontos lehet az események rendezése. Előfordulhat például, hogy azt szeretné, hogy a háttérrendszer a DELETE parancs előtt dolgozza fel a frissítési parancsot. Ebben az esetben beállíthatja egy eseményen a partíciós kulcsot, vagy használhat egy `PartitionSender` objektumot (ha a régi Microsoft. Azure. Messaging függvénytárat használja), hogy csak egy adott partícióra küldjön eseményeket. Így biztosíthatja, hogy ha ezek az események beolvashatók a partícióból, a rendszer a sorrendben olvassa be őket. Ha az **Azure. Messaging. EventHubs** függvénytárat használja, és további információra van szüksége, olvassa el a [kód áttelepítése a PartitionSender-ről a EventHubProducerClient-re című témakört az események partícióba való közzétételéhez](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 vagy újabb)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 vagy korábbi)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

Ha ezt a konfigurációt használja, vegye figyelembe, hogy ha az adott partíció, amely számára a Küldés nem érhető el, hibaüzenetet fog kapni. Ha összehasonlítási pontként nem rendelkezik egyetlen partícióval, a Event Hubs szolgáltatás elküldi az eseményt a következő rendelkezésre álló partíciónak.

Az egyik lehetséges megoldás a rendezés biztosítására, ugyanakkor a maximális idő maximalizálása az eseményeknek az Event Processing-alkalmazás részeként való összesítése lenne. Ennek a legegyszerűbb módja, ha az eseményt egy egyéni sorszám tulajdonsággal pecsételi. Az alábbi kód példa erre:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 vagy újabb)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 vagy korábbi)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Ez a példa az eseményt az Event hub egyik elérhető partíciójában küldi el, és beállítja az alkalmazáshoz tartozó sorozatszámot. Ehhez a megoldáshoz a feldolgozó alkalmazásnak meg kell őriznie az állapotot, de a küldők számára nagyobb valószínűséggel elérhetőnek kell lennie.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
