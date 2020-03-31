---
title: Elérhetőség és konzisztencia – Azure Event Hubs | Microsoft dokumentumok
description: Hogyan biztosíthat a maximális rendelkezésre állási és konzisztencia az Azure Event Hubs partíciók használatával.
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
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904483"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Rendelkezésre állás és konzisztencia az Event Hubsban

## <a name="overview"></a>Áttekintés
Az Azure Event Hubs [egy particionálási modell](event-hubs-scalability.md#partitions) t használ a rendelkezésre állás és a párhuzamosság javítása egyetlen eseményközponton belül. Ha például egy eseményközpont négy partícióval rendelkezik, és az egyik partíció tátva van az egyik kiszolgálóról a másikra egy terheléselosztási műveletben, akkor is küldhet és fogadhat három másik partícióról. Emellett több partíciót lehetővé teszi, hogy több egyidejű olvasók az adatok feldolgozása, az összesített átviteli. A particionálás és a rendezés következményeinek megértése elosztott rendszerben a megoldástervezés kritikus szempontja.

A megrendelés és a rendelkezésre állás közötti kompromisszum magyarázatához lásd a [CAP-tételt](https://en.wikipedia.org/wiki/CAP_theorem), más néven Brewer-tételt. Ez a tétel a konzisztencia, a rendelkezésre állás és a partíciótolerancia közötti választást tárgyalja. Azt állítja, hogy a hálózatok által particionált rendszerek mindig van kompromisszum a konzisztencia és a rendelkezésre állás között.

A Brewer-tétel a következőképpen határozza meg a konzisztenciát és a rendelkezésre állást:
* Partíciótolerancia: az adatfeldolgozó rendszer azon képessége, hogy partícióhiba esetén is folytassa az adatok feldolgozását.
* Elérhetőség: a nem hibás csomópont ésszerű időn belül ésszerű választ ad vissza (hibák és időtúllépések nélkül).
* Konzisztencia: az olvasás garantáltan visszaadja a legutóbbi írást egy adott ügyfélhez.

## <a name="partition-tolerance"></a>Partíció tűréshatára
Az Event Hubs egy particionált adatmodellre épül. A telepítés során konfigurálhatja az eseményközpontban lévő partíciók számát, de ezt az értéket később nem módosíthatja. Mivel az Event Hubs partíciókat kell használnia, döntést kell hoznia az alkalmazás rendelkezésre állásáról és konzisztenciájáról.

## <a name="availability"></a>Rendelkezésre állás
Az Event Hubs használatának legegyszerűbb módja az alapértelmezett viselkedés használata. Ha új **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** objektumot hoz létre, és a **[Küldés](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** metódust használja, az események automatikusan ellesznek osztva az eseményközpontban lévő partíciók között. Ez a viselkedés lehetővé teszi a legnagyobb mennyiségű fel időben.

A maximális felszakítási időt igénylő használati esetekesetében ez a modell ajánlott.

## <a name="consistency"></a>Konzisztencia
Bizonyos esetekben az események rendezése fontos lehet. Előfordulhat például, hogy azt szeretné, hogy a háttérrendszer a delete parancs előtt feldolgozzon egy frissítési parancsot. Ebben az esetben beállíthatja a partíciókulcsot egy eseményen, `PartitionSender` vagy egy objektum használatával csak eseményeket küldhet egy bizonyos partícióra. Ezzel biztosítja, hogy amikor ezeket az eseményeket a partícióról olvassa be, azok sorrendben olvashatók.

Ezzel a konfigurációval ne feledje, hogy ha az adott partíció, amelyre küld, nem érhető el, hibaüzenetet fog kapni. Összehasonlításként, ha nem rendelkezik affinitásegyetlen partícióhoz, az Event Hubs szolgáltatás elküldi az eseményt a következő elérhető partícióra.

Az egyik lehetséges megoldás a rendelés biztosítására, miközben maximalizálja az időt, az események összesítése az eseményfeldolgozó alkalmazás részeként. Ennek legegyszerűbb módja az esemény lebélyegzése egyéni sorszám-tulajdonsággal. Az alábbi kód példa erre:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (5.0.0 vagy újabb)](#tab/latest)

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

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (4.1.0 vagy korábbi)](#tab/old)
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

Ebben a példában elküldi az eseményt az eseményközpont egyik elérhető partíciójára, és beállítja az alkalmazás megfelelő sorszámát. Ez a megoldás megköveteli, hogy a feldolgozási alkalmazás megtartsa az állapotot, de olyan végpontot ad a feladóknak, amely nagyobb valószínűséggel érhető el.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Az Eseményközpontok szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
