---
title: Rendelkezésre állás és konzisztencia az Azure Event Hubs |} A Microsoft Docs
description: Adja meg a legnagyobb rendelkezésre állás és konzisztencia az Azure Event Hubs partíciók használatával hogyan lehet.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: shvija
ms.openlocfilehash: 67a7a701eb7700fab9aa9d0ec22354cc1618f856
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004694"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Rendelkezésre állás és konzisztencia az Event Hubs

## <a name="overview"></a>Áttekintés
Az Azure Event Hubs használ egy [modell particionálás](event-hubs-features.md#partitions) jobb rendelkezésre állás és ezerszer belül ugyanabba az eseményközpontba. Például egy eseményközpontba négy partícióval rendelkezik, és a egy adott partíciók kerül egyik kiszolgálóról a másikra terheléselosztási művelet, akkor is továbbra is küldhet és fogadhat három más partíciókból. Ezenkívül további partíció lehetővé teszi, hogy az összesített átviteli sebesség növelése az adatfeldolgozás, több egyidejű olvasók. Megoldásterv kritikus fontosságú tényezője ismertetése a particionálási és az elosztott rendszerekben rendezése következményei.

A rendezés és a rendelkezésre állási közötti kompromisszum magyarázatának elősegítésére, tekintse meg a [CAP-tétel](https://en.wikipedia.org/wiki/CAP_theorem), más néven sörgyár a tétel. Ez a tétel konzisztencia, a rendelkezésre állás és a partíció tolerancia közötti választás ismerteti. Megállapítja, hogy a hálózati dokumentumtárolási rendszerek esetén mindig van közötti konzisztencia és a rendelkezésre állás.

Sörgyár a tétel határozza meg konzisztencia és a rendelkezésre állás a következő:
* Tolerancia partícióazonosító: a képessége, adatfeldolgozási folytatja az adatok feldolgozását, még akkor is, ha a partíció hiba történik.
* Rendelkezésre állás: nem hibás csomópont választ ésszerű (a nem hibák vagy időtúllépés) ésszerű időn belül.
* Konzisztencia: a olvasási garantált, hogy a legújabb írása az adott ügyfél adja vissza.

## <a name="partition-tolerance"></a>Partíció tolerancia
Az Event Hubs egy particionált adatok modell épül. Konfigurálhatja a partíciók száma az eseményközpont telepítés során, de ezt az értéket később nem módosítható. Mivel a partíciók az Event hubs szolgáltatást kell használnia, akkor rendelkezésre állás és konzisztencia az alkalmazás kapcsolatos döntéseket.

## <a name="availability"></a>Rendelkezésre állás
Event Hubs használatának első lépései a legegyszerűbb módja, hogy használja az alapértelmezett viselkedést. Ha létrehoz egy új ** [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) ** objektumra, és használja a ** [küldése](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_) ** módot, az eseményeket a rendszer automatikusan terjeszt között a partíciók az eseményközpont. Ez a viselkedés lehetővé teszi, hogy a lehető legnagyobb mennyisége óta eltelt idő.

A maximális időt szentelhet igénylő használati esetek Ez a modell használata ajánlott.

## <a name="consistency"></a>Konzisztencia
Bizonyos esetekben az események sorrendjének befolyásolása fontos lehet. Például érdemes lehet a Törlés parancs előtt egy frissítés parancs végrehajtásához háttérrendszereknek. Ebben a példában a partíciókulcs meg egy eseményt, vagy is használja a `PartitionSender` objektum csak küldhet eseményeket egy adott partíció. Ez biztosítja, hogy ezek az események olvasása a partícióból, olvasott sorrendben.

Ezzel a konfigurációval vegye figyelembe, hogy ha az adott partíció, amelyhez küld nem érhető el, kapni fog egy hibaválasz. Összehasonlítási pontként Ha nem rendelkezik egy adott partíció kapcsolatot az Event Hubs szolgáltatás elküldi az esemény a következő elérhető partíció.

Egy lehetséges megoldást győződjön meg arról, rendezése, is növelhető a óta eltelt idő, az események összesítése az Eseményfeldolgozási alkalmazás részeként lenne. Ehhez a legegyszerűbb módja, hogy az esemény blokk: egy egyéni feladatütemezési szám tulajdonsággal. Az alábbi kód példa erre:

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

Ebben a példában az eseményt küld egy, a rendelkezésre álló partíciók az eseményközpont, és beállítja a megfelelő sorszám az alkalmazásból. Ez a megoldás állapota a feldolgozási alkalmazás által megőrizni igényel, de lehetővé teszi a feladók egy végpontot, amely nagyobb valószínűséggel érhető el.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
