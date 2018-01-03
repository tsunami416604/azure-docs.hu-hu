---
title: "Az Azure Service Bus párosítva névterek |} Microsoft Docs"
description: "Megvalósítás részletei párosított névtér és a költséghatékonyság"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Névtér megvalósítás részletei megfeleltetni, ráadásul a költségek gyakorolt hatása

A [PairNamespaceAsync] [ PairNamespaceAsync] módszer használatával egy [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] példány, látható feladatokat végez az Ön nevében. Nincs vannak költség szempontok a funkció használata esetén, mert működésének megértése ezeket a feladatokat, hogy ez akkor fordul elő, amikor várt viselkedése. Az API-t a következő automatikus viselkedés az Ön nevében végez:

* Várakozó várólisták létrehozása.
* Létrehozása egy [MessageSender] [ MessageSender] objektum, amely az üzenetsorok és témakörök-kiszolgálóhoz.
* Amikor egy üzenetküldési entitásra nem érhető el, küld észlelés, ha az érintett entitásként válik elérhetővé újra megpróbálja ping üzeneteket az entitásban.
* Választhatóan a "üzenet szivattyúk" gyűjteménye, amelyek áthelyezését a várakozó várólistákról elsődleges várólistára.
* Az elsődleges és másodlagos bezárni vagy hibás koordinálja [MessagingFactory] [ MessagingFactory] példányok.

Magas szinten, a szolgáltatás a következőképpen működik: Ha az elsődleges entitás állapota kifogástalan, nincs viselkedés változások történnek. Ha a [FailoverInterval] [ FailoverInterval] időtartam eltelte, és az elsődleges entitás nem sikeres láthatják küldése után nem átmeneti [MessagingException] [ MessagingException] vagy egy [TimeoutException][TimeoutException], a következő történik:

1. Az elsődleges entitás műveletek le vannak tiltva, és a rendszer az elsődleges entitás pingeli, amíg a ping-üzenetek kézbesítése sikeresen küldése.
2. Egy véletlenszerű várakozó várólista van kiválasztva.
3. [BrokeredMessage] [ BrokeredMessage] objektumok legyenek átirányítva a kiválasztott várakozó várólista.
4. A kiválasztott várakozó várólistára küldési művelet sikertelen lesz, ha erre a várólistára lekért elforgatási van, és egy új sor van kiválasztva. Az összes feladó a [MessagingFactory] [ MessagingFactory] példány ismerje meg a hiba.

A következő ábra a feladatütemezési jelzik. A küldő először küld üzeneteket.

![Párhuzamos névterek][0]

Elsődleges queue küldendő hiba esetén a küldő megkezdi egy véletlenszerűen kiválasztott várakozó várólista üzenetküldésre. Egy időben a ping feladat elindítása.

![Párhuzamos névterek][1]

Ezen a ponton az üzenetek továbbra a másodlagos várólista és az elsődleges várólistára nem szállított. Az elsődleges queue újra állapota kifogástalan, ha legalább egy folyamat futnia kell a Szifonos. A Szifonos továbbítja az üzeneteket a különböző várakozó várólisták a megfelelő cél entitásokat (üzenetsorok és témakörök).

![Párhuzamos névterek][2]

Ez a témakör további része darabot működése részleteit ismerteti.

## <a name="creation-of-backlog-queues"></a>Várakozó várólista létrehozása
A [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] objektum lett átadva a [PairNamespaceAsync] [ PairNamespaceAsync] metódus számát jelzi. Várakozó várólisták szeretne használni. Minden tartalék várólista létrejön a következő tulajdonságokkal explicit módon beállítva (minden más érték beállítása a [QueueDescription] [ QueueDescription] alapértelmezett):

| Útvonal | [elsődleges névtér] / x-servicebus-átvitel / [index] [0, BacklogQueueCount) érték esetén a [index] |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| maxDeliveryCount |egész szám. MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 perc |
| EnableDeadLetteringOnMessageExpiration |igaz |
| EnableBatchedOperations |igaz |

Például az első várakozó várólistát létrehozott névtér **contoso** nevű `contoso/x-servicebus-transfer/0`.

Az üzenetsorok létrehozása, amikor a kód először ellenőrzi, hogy ilyen a várólista létezik-e. Ha a várólista nem létezik, a várólista létrejön. A kód nem "extra" Várakozó várólisták karbantartása. Pontosabban Ha az alkalmazás elsődleges névterű **contoso** öt várakozó várólistára, de az elérési úttal rendelkező várakozó várólista kérelmek `contoso/x-servicebus-transfer/7` létezik, várólistához további várakozó továbbra is megtalálható, de nem használatos. A rendszer explicit módon engedélyezi a felesleges várakozó várólisták is létezhetnek, amelyek nem használhatók. A névtér tulajdonosaként való telepítésért felelős törölje a nem használt vagy nemkívánatos várakozó üzenetsorokkal. Ez a döntés oka, hogy a Service Bus nem tudja, milyen célból létezik a következő névtérbeli a várólisták számára. Továbbá ha a várólista létezik-e a megadott névvel, de nem felel meg a feltételezett [QueueDescription][QueueDescription], majd az OK: a saját módosítható az alapértelmezett viselkedés. Nem garantálja a kód által a várakozó várólisták módosításai legyenek módosítva. Győződjön meg arról, hogy alaposan tesztelje a módosításokat.

## <a name="custom-messagesender"></a>Egyéni MessageSender
Küldésekor, az összes üzenet végighaladnia belső [MessageSender] [ MessageSender] objektum, amely a szokásos módon viselkedik, ha minden rendben működik, és átirányítja a felhasználókat a várakozó fájlok számát a várólisták, amikor dolgot "törés." Nem átmeneti hiba fogadását követően a felszabadításakor kezdődik. Után egy [TimeSpan] [ TimeSpan] álló időszak a [FailoverInterval] [ FailoverInterval] során, ami nem sikeres üzenetek küldése történik, tulajdonság értéke a feladatátvételi be kell kapcsolni. Ezen a ponton a következőket fordulhat elő, ha minden:

* A ping feladatot hajt végre minden [PingPrimaryInterval] [ PingPrimaryInterval] annak ellenőrzésére, ha az entitás érhető el. Ez a feladat sikeres, ha az entitás azonnal használó összes ügyfél-kódot elindul, új üzeneteket küld az elsődleges névtér.
* Bármely más feladóktól küldhet, hogy ugyanaz az entitás későbbi kérelmek eredményez a [BrokeredMessage] [ BrokeredMessage] küldi el a várakozó várólista elhelyezkedik módosítani. A módosítás eltávolítja az egyes tulajdonságok a [BrokeredMessage] [ BrokeredMessage] objektumra, és a máshol tárolja azokat. A következő tulajdonság nincs bejelölve, és hozzáadása az új aliast, a Service Bus és az SDK egységesen üzenetek feldolgozásához:

| Régi tulajdonság neve | Új tulajdonság neve |
| --- | --- |
| Munkamenet-azonosító |x-ms-munkamenet-azonosító |
| TimeToLive |az x-ms-élettartam |
| ScheduledEnqueueTimeUtc |x-ms-elérési út |

Az eredeti elérési utat célként is tárolja az üzenetben x-ms-path nevű tulajdonság. Ez a kialakítás lehetővé teszi, hogy az egyetlen várakozó várólista azonos sok entitásokhoz tartozó üzenetek. A tulajdonságok a Szifonos által van lefordítva.

Az egyéni [MessageSender] [ MessageSender] objektum is tapasztal, amikor üzenetek megközelíti a 256 KB-os korlát és feladatátvételi be kell kapcsolni. Az egyéni [MessageSender] [ MessageSender] objektum tárolja az üzeneteket az összes üzenetsorok és témakörök a várakozó várólisták együtt. Ez az objektum keveri együtt a várakozó várólistákon sok elsődleges üzeneteit. Sok ügyfél, amely nem tudja egymás közötti terheléselosztás kezeléséhez, az SDK-val véletlenszerűen választja ki egy várakozó várólista minden [QueueClient] [ QueueClient] vagy [TopicClient] [ TopicClient] hoz létre a kódban.

## <a name="pings"></a>Ping-üzenetek
A ping üzenetre egy üres [BrokeredMessage] [ BrokeredMessage] rendelkező a [ContentType] [ ContentType] alkalmazás/vnd.ms-szolgáltatásbusz-ping tulajdonsága és egy [TimeToLive] [ TimeToLive] értéke 1 másodperc. A ping rendelkezik egy különös jellemző a Service Bus: a kiszolgáló soha nem továbbítja a pingelés, ha a hívó kér egy [BrokeredMessage][BrokeredMessage]. Ebből kifolyólag sosem megtudhatja, hogyan kaphatnak, és figyelmen kívül. Minden entitás (egyedi üzenetsor vagy témakör) / [MessagingFactory] [ MessagingFactory] ügyfelenként példányt fog kell program ping üzenetet küld amikor-nek minősíti azokat nem érhető el. Alapértelmezés szerint ez akkor fordul elő percenként. Ping üzenetekre rendszeres Service Bus-üzenetek minősülnek, és sávszélesség és üzenetek eredményezhet. Amint az ügyfelek észlelik, hogy a rendszer nem érhető el, az üzenetek állítsa le.

## <a name="the-syphon"></a>A Szifonos
Az alkalmazás legalább egy végrehajtható program aktívan futnia kell a Szifonos. A Szifonos hajt végre egy hosszú lekérdezési kap, hogy 15 percig tart. Összes entitás érhető el, és 10 várakozó várólisták, amikor az alkalmazást, amelyen a Szifonos meghívja a fogadási művelet 40 alkalommal óránkénti, napi 960 időpontot és 30 napban 28800 alkalommal. Amikor a Szifonos van aktívan üzenetek a várakozó fájlok számát a elsődleges várólistára, a minden üzenet észlel a következő költségek (az üzenet mérete és a sávszélesség szabványos díja valamennyi szakaszában vonatkozik):

1. A várakozó fájlok küldése.
2. A várakozó kapott.
3. Az elsődleges küldeni.
4. Az elsődleges kapott.

## <a name="closefault-behavior"></a>Zárja be/tartalék viselkedése
Az alkalmazásban, amelyen a Szifonos, miután az elsődleges vagy másodlagos [MessagingFactory] [ MessagingFactory] hibákat, vagy le van zárva nélkül is jelzett a partnerrel vagy zárva, és a Szifonos észleli ebben az állapotban a Szifonos működik. Ha a másik [MessagingFactory] [ MessagingFactory] nem zárt 5 másodpercen belül a Szifonos hibákat a továbbra is meg van nyitva [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>További lépések
Lásd: [aszinkron üzenetkezelési mintákat és magas rendelkezésre állású] [ Asynchronous messaging patterns and high availability] részletes információt a Service Bus aszinkron üzenetkezelési. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
