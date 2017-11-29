---
title: "Rendelkezésre állás és az Azure Event Hubs következetes |} Microsoft Docs"
description: "A legnagyobb rendelkezésre állását és konzisztencia biztosításához az Azure Event Hubs partíciók használatával hogyan."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: be1398e9b0a10efcd694e46d6322d5d7b9e7a843
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="availability-and-consistency-in-event-hubs"></a>Rendelkezésre állás és a konzisztencia az Event Hubs

## <a name="overview"></a>Áttekintés
Az Azure Event Hubs használ egy [modell particionálás](event-hubs-features.md#partitions) rendelkezésre állását és párhuzamos folyamatkezelést biztosítja belül egyetlen eseményközpont javítása érdekében. Például ha egy eseményközpontba négy partíciókkal rendelkezik, és ezek a partíciók egyik áthelyezése egyik kiszolgálóról a másikra terheléselosztási művelet, akkor is továbbra is küldhet és fogadhat három más partíciókból. Emellett több partíciót rendelkező lehetővé teszi a teljes átviteli sebesség növelése több egyidejű olvasók feldolgozni az adatokat. Particionálás és az elosztott rendszer rendelés következményeit megértése, egyik fontos szempontja, hogy a megoldás kialakításának.

Rendezés és a rendelkezésre állási közötti kompromisszum magyarázatának elősegítésére, tekintse meg a [CAP tétel](https://en.wikipedia.org/wiki/CAP_theorem), más néven sörgyár tartozó tétel. A tétel konzisztencia, a rendelkezésre állás és a partíció képesség közötti választás ismerteti.

Sörgyár tartozó tétel meghatározza, hogy konzisztencia és rendelkezésre állás az alábbiak szerint:
* Partícióazonosító tolerancia: a lehetősége az adatok feldolgozása rendszert adatainak feldolgozása még akkor is, ha a partíció hiba történik.
* Rendelkezésre állás: nem hibás csomópont választ ad vissza egy ésszerű (a nem hibák vagy időtúllépés) elfogadható időn belül.
* Konzisztencia: olvasási garantáltan vissza a legutóbbi írása az adott ügyfél.

## <a name="partition-tolerance"></a>Partíció tolerancia
Az Event Hubs egy particionált adatmodell épül. Konfigurálhatja a partíciók száma az eseményközpont a telepítés során, de nem ez az érték később módosíthatja. Az Event Hubs partíciók kell használnia, mert akkor rendelkezésre állási és az alkalmazás konzisztenciájának kapcsolatos döntést.

## <a name="availability"></a>Rendelkezésre állás
Az Event Hubs használatába legegyszerűbb módja, hogy az alapértelmezett viselkedés használja. Ha létrehoz egy új  **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)**  objektumra, és használja a  **[küldése](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)**  metódust, az eseményeket a rendszer automatikusan terjeszt között a partíciók az eseményközpont. Ez a viselkedés lehetővé teszi, hogy a legnagyobb mennyisége idő.

A maximális idő igénylő használat esetén ez a modell használata ajánlott.

## <a name="consistency"></a>Konzisztencia
Bizonyos esetekben az események sorrendje fontos lehet. Például érdemes lehet a háttér-rendszer egy frissítési parancs előtt a Törlés parancsot. Ebben a példában, vagy a partíciós kulcs egy olyan eseményre, vagy használhatja a `PartitionSender` objektum csak egy bizonyos partíció elküldje az eseményeket. Ezzel biztosítja, hogy ezek az események olvasása a partícióból, olvasott sorrendben.

Ezzel a konfigurációval vegye figyelembe, hogy az adott partíció, amelyhez küldendő nem érhető el, ha kapni fog egy hibaüzenetet. Összehasonlítási pontként Ha nem rendelkezik egyetlen partícióra kapcsolatot az Event Hubs szolgáltatás elküldi az esemény a következő elérhető partíció.

Győződjön meg arról, rendezés, idő, is Mindeközben az egyik lehetséges megoldás az alkalmazás feldolgozása az esemény részeként lenne az összegyűjtött eseményeket. A legegyszerűbb ehhez módja az esemény stamp egyéni feladatütemezési számú tulajdonsággal. A következő kód egy példát mutat be:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

Ebben a példában az esemény küld egy, a rendelkezésre álló partíciók az eseményközpont a, és beállítja a megfelelő sorszámmal az alkalmazásból. Ez a megoldás állapotot a feldolgozás alkalmazáshoz őrzi igényel, de lehetővé a feladók olyan végponttal, amely valószínűbb, hogy elérhető legyen.

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs szolgáltatás – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
