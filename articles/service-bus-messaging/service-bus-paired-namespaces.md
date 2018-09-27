---
title: Az Azure Service Bus párosított névterek |} A Microsoft Docs
description: Párosított névterek megvalósítási részletei és költség
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: spelluru
ms.openlocfilehash: ac663cc382fcacd4960843c25aa6c95191210116
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395202"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>A párosított névtérhez megvalósítási részletei, és optimalizálhatja a költségeket következmények

A [PairNamespaceAsync] [ PairNamespaceAsync] módszer használatával egy [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] példány, látható feladatokat hajt az Ön nevében. Ott vannak költségvetési szempontok a funkció használatakor, mivel, megismeréséhez ezeket a feladatokat, így még várt viselkedése. Az API-t az Ön nevében a következő automatikus viselkedés végez:

* Várakozó fájlok számát a üzenetsorok létrehozása.
* Létrehozását olyan [MessageSender] [ MessageSender] objektum, amely az üzenetsor vagy témakör ismerteti.
* Egy üzenetküldési entitásra elérhetetlenné válik, amikor küld annak észlelése, ha az adott entitás válik elérhetővé újra megpróbálja ping üzeneteket az entitáshoz.
* Választhatóan a "üzenet szivattyújának" áll, amelyek üzenetek áthelyezése a várakozó várólistákról az elsődleges üzenetsorok.
* Az elsődleges és másodlagos záró/hibás koordinálja [MessagingFactory] [ MessagingFactory] példányok.

Magas szinten, a szolgáltatást a következőképpen működik: amikor az elsődleges entitás állapota kifogástalan, viselkedésének módosítása nélkül történik. Ha a [FailoverInterval] [ FailoverInterval] időtartam eltelte, és az elsődleges entitás számára nem sikeres elküldése után nem átmeneti [Istransient] [ MessagingException] vagy egy [TimeoutException][TimeoutException], a következő történik:

1. Küldés az elsődleges entitásnál műveletek le vannak tiltva, és a rendszer az elsődleges entitásnál pingeli, amíg a pingelésre is lehet sikeresen kézbesíteni.
2. Egy véletlenszerű várakozó üzenetsor van kiválasztva.
3. [BrokeredMessage] [ BrokeredMessage] objektumok legyenek átirányítva a várakozó fájlok számát a kiválasztott várólistát.
4. A kiválasztott várakozó üzenetsor-küldési művelet meghiúsul, ha erre a várólistára lehívja a a rotációból, és a egy új sor van kijelölve. Az összes feladó a [MessagingFactory] [ MessagingFactory] példány ismerje meg a hiba.

A következő ábrán a feladatütemezési jelzik. Először is a küldő üzeneteket küld.

![Párosított névterek][0]

Az elsődleges üzenetsornak küldése hiba esetén a küldő megkezdi üzeneteket küld egy véletlenszerűen kiválasztott várakozó üzenetsor. Egy időben a ping feladat elindítása.

![Párosított névterek][1]

Ezen a ponton az üzeneteket továbbra is megtalálhatók a másodlagos üzenetsor, és az elsődleges üzenetsornak nem lett kézbesítve. Ha az elsődleges üzenetsornak ismét kifogástalan állapotban, legalább egy folyamat futnia kell a Szifonos. A Szifonos kézbesíti az üzenetet a különböző várakozó fájlok számát a várólistákat a megfelelő cél entitások (üzenetsorok és témakörök).

![Párosított névterek][2]

Ez a témakör további részében megtalálhatja működése részleteit ismerteti.

## <a name="creation-of-backlog-queues"></a>Várakozó fájlok számát a várólista létrehozása
A [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] objektumot a [PairNamespaceAsync] [ PairNamespaceAsync] metódus számát jelzi. várakozó fájlok számát a várólisták létrehozásához használni szeretne. Minden egyes várakozó fájlok számát a várólista létrejön az alábbi tulajdonságokkal rendelkező explicit módon beállítva (minden egyéb értékek vannak beállítva a [QueueDescription] [ QueueDescription] alapértelmezett):

| Útvonal | [az elsődleges névtér] / x-servicebus-átvitel / [index] [index] [0, BacklogQueueCount) érték esetén |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |: egész szám. MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 perc |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

Például az első várakozó üzenetsor létrehozott névtér **contoso** nevű `contoso/x-servicebus-transfer/0`.

Az üzenetsorok létrehozása, amikor a a kód először ellenőrzi, hogy létezik-e ilyen egy üzenetsorba. Ha az üzenetsor nem létezik, a várólista létrejön. A kód nem létezik távolítsa el a "plusz" mappájában várakozó fájlok számát üzenetsorok. Pontosabban Ha az alkalmazás az elsődleges névtérrel rendelkező **contoso** öt várakozó fájlok számát a várólisták, de az elérési úttal rendelkező várakozó üzenetsor-kérelmek `contoso/x-servicebus-transfer/7` létezik, extra várakozó üzenetsornak továbbra is megtalálható, de nem használatos. A rendszer explicit módon lehetővé teszi, hogy a várakozó fájlok számát a további üzenetsorok léteznie, amely nem használható. A névtér tulajdonosként Ön felelős a várakozó fájlok számát a fel nem használt vagy nemkívánatos üzenetsorokkal karbantartása. Ezt a döntést oka, hogy a Service Bus nem tudja, milyen célra léteznek, a névtérben lévő összes üzenetsor. Továbbá ha a várólista létezik a megadott névvel, de nem felel meg a feltételezett [QueueDescription][QueueDescription], majd a okai a következők a saját módosítható az alapértelmezett viselkedés. Nincs garancia a kód által a várakozó fájlok számát a várólisták módosításai legyenek módosítva. Győződjön meg arról, hogy alaposan tesztelheti a módosításokat.

## <a name="custom-messagesender"></a>Egyéni MessageSender
Küld, ha minden üzenetet meg egy belső [MessageSender] [ MessageSender] objektum, amely a szokásos módon viselkedik, ha minden rendben működik, és átirányítja a felhasználókat a várakozó fájlok számát az üzenetsorok, amikor dolgot "megszegheti." Nem átmeneti hiba fogadásakor az időmérő elindul. Miután egy [TimeSpan] [ TimeSpan] álló időszak a [FailoverInterval] [ FailoverInterval] tulajdonság értéke, amely során nem sikeres üzeneteket küldenek, a feladatátvétel be kell kapcsolni. Ezen a ponton az alábbiakat az egyes entitásokhoz fordulhat elő:

* A ping feladatot hajt végre minden [PingPrimaryInterval] [ PingPrimaryInterval] annak ellenőrzésére, ha az entitás érhető el. Ez a feladat sikeres, ha azonnal az entitást használó összes ügyfél kód elindul, új üzeneteket küld az elsődleges névtér.
* Bármely más feladóktól ugyanahhoz az entitáshoz való küldése a jövőbeni kérések eredményez a [BrokeredMessage] [ BrokeredMessage] módosítani szeretném a várakozó fájlok számát a várólistára küldött. A módosítás eltávolítja az egyes tulajdonságok a [BrokeredMessage] [ BrokeredMessage] objektumra, és a máshol tárolja azokat. A következő tulajdonságok nincs bejelölve, új alias, lehetővé téve a Service Bus és az SDK-üzenetek feldolgozása egységesen alatt:

| Régi tulajdonság neve | Új tulajdonság neve |
| --- | --- |
| munkamenet-azonosító |x-ms-munkamenet-azonosító |
| TimeToLive |az x-ms-élettartam |
| ScheduledEnqueueTimeUtc |x-ms-elérési útja |

Az eredeti elérési úthoz, x-ms-path nevű tulajdonságot is tárolja az üzenetet. Ez a kialakítás lehetővé teszi, hogy számos entitás egyetlen várakozó várólista létezhet tartozó üzenetek. A tulajdonságok a Szifonos vissza úgy van lefordítva.

Az egyéni [MessageSender] [ MessageSender] objektum problémák merülhetnek fel, amikor üzenetek készíthető elő a 256 KB-os korlátot, és a feladatátvételi bevonhat. Az egyéni [MessageSender] [ MessageSender] objektumot tárolja az üzeneteket az összes üzenetsort és témakört a várakozó fájlok számát a várólisták együtt. Ezt az objektumot együtt a várakozó fájlok számát a várólisták belül számos elsődleges üzeneteit eredményét. Sok ügyfél, amely nem tudja egymással közötti terheléselosztás kezeléséhez, az SDK véletlenszerűen választja ki egyik várakozó üzenetsor minden [QueueClient] [ QueueClient] vagy [TopicClient] [ TopicClient] hoz létre a kódban.

## <a name="pings"></a>Pingelésre.
Ping üzenetet az üres [BrokeredMessage] [ BrokeredMessage] a saját [ContentType] [ ContentType] tulajdonság értéke az alkalmazás/vnd.ms-servicebus-pingelésre és egy [TimeToLive] [ TimeToLive] értéke 1 másodperc. Ez a pingelés egy speciális jellemzők rendelkezik a Service Bus: a kiszolgáló soha nem biztosít egy pingelés, ha bármely hívó kér egy [BrokeredMessage][BrokeredMessage]. Így sosem kell fogadni, és figyelmen kívül hagyhatja ezeket az üzeneteket. Minden entitás (egyedi üzenetsor vagy témakör) / [MessagingFactory] [ MessagingFactory] ügyfél példányonként fog pingkérést küldött, amikor nem érhető el, minősülnek. Alapértelmezés szerint ez történik, percenként egyszer. Ping üzenetekre. a rendszer normál Service Bus-üzenetek, és eredményezhet a sávszélesség, és üzeneteket kell fizetni. Amint az ügyfelek észlelik, hogy a rendszer nem érhető el, állítsa le az üzeneteket.

## <a name="the-syphon"></a>A Szifonos
Legalább egy végrehajtható programot az alkalmazásban folyamatosan futnia kell a Szifonos. A Szifonos hajt végre egy hosszú lekérdezési kap, amely 15 percig tart. Ha minden entitás érhetők el, és 10 várakozó üzenetsorok rendelkezik, az alkalmazás, amelyen a Szifonos meghívja a receive műveletet 40 alkalommal óránként, naponta 960 többször, és 30 napban 28800 alkalommal. Amikor a Szifonos van aktív üzenetek áthelyezése a várakozó fájlok számát az elsődleges üzenetsornak, minden üzenetet során lép fel az alábbi költségekkel (az üzenet mérete és a sávszélesség standard díja minden fázisában vonatkozik):

1. A várakozó küldeni.
2. A várakozó fogad.
3. Az elsődleges küldeni.
4. Az elsődleges fogad.

## <a name="closefault-behavior"></a>Zárja be és tartalék viselkedés
Egy alkalmazásban, amelyen a Szifonos, és egyszer az elsődleges vagy másodlagos [MessagingFactory] [ MessagingFactory] hibákat, vagy le van zárva, anélkül is meghibásodott a partnerrel vagy lezárt és a Szifonos észlel, ebben az állapotban a Szifonos funkcionál. Ha a másik [MessagingFactory] [ MessagingFactory] nem zárt 5 másodpercen belül a Szifonos hibákat a továbbra is meg van nyitva [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>További lépések
Lásd: [aszinkron üzenetkezelési minták és magas rendelkezésre állás] [ Asynchronous messaging patterns and high availability] részletes leírásáért lásd a Service Bus aszinkron üzenetkezelés. 

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
