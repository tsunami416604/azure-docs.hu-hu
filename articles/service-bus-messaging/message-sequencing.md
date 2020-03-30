---
title: Az Azure Service Bus üzenetszekvenálása és időbélyegei | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan őrizheti meg az Azure Service Bus-üzenetek szekvenálását és rendelését (időbélyegekkel).
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261657"
---
# <a name="message-sequencing-and-timestamps"></a>Üzenetek előkészítése és időbélyegek

A szekvenálás és az időbélyegzés két olyan szolgáltatás, amely mindig engedélyezve van az összes Service Bus-entitáson, és a [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) és [a EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) tulajdonságokon keresztül a fogadott vagy a böngészett üzenetek.

Azokban az esetekben, amikor az üzenetek abszolút sorrendje jelentős és/vagy amikor a fogyasztónak megbízható egyedi azonosítóra van szüksége az üzenetekhez, a közvetítő a várólistához vagy a témakörhöz képest egyre növekvő sorszámmal jegyzi az üzeneteket. Particionált entitások esetén a sorszám a partícióhoz viszonyítva kerül kiadásra.

A **SequenceNumber** érték egy üzenethez rendelt egyedi 64 bites egész szám, mivel azt a közvetítő elfogadja és tárolja, és belső azonosítóként működik. Particionált entitások esetén a legfelső 16 bit a partícióazonosítót tükrözi. A sorozatszámok nullára tolódik, ha a 48/64 bites tartomány kimerül.

A sorszám egyedi azonosítóként megbízható, mivel központi és semleges hatóság rendeli hozzá, nem pedig az ügyfelek. Ez is képviseli a valódi érkezési sorrendben, és pontosabb, mint egy időbélyegző, mint a rendelési kritérium, mert az időbélyegek nem elég nagy felbontású extrém üzenet aránya, és ki lehet téve (azonban minimális) óra döntés olyan helyzetekben, amikor a bróker tulajdonosi átmenetek a csomópontok között.

Az abszolút érkezési megbízás számít például olyan üzleti forgatókönyvekesetében, amelyekben korlátozott számú felkínált árut szolgálnak fel érkezési sorrendben, amíg a szállítások tart; koncert jegyértékesítés egy példa.

Az időbélyegzési képesség semleges és megbízható hatóságként működik, amely pontosan rögzíti az üzenet ÉRKEZÉSi idejét, amely az **EnqueuedTimeUtc** tulajdonságban tükröződik. Az érték akkor hasznos, ha egy üzleti forgatókönyv határidőktől függ, például attól, hogy egy munkacikket egy bizonyos időpontban éjfél előtt küldtek-e el, de a feldolgozás messze elmarad a várólista hátralékátől.

## <a name="scheduled-messages"></a>Ütemezett üzenetek

Az üzeneteket késleltetett feldolgozásra is beadhatja az üzenetsorba vagy témakörbe például azért, hogy egy ütemezett feladattal az üzenetek elérhetővé váljanak egy másik rendszer általi feldolgozásra egy bizonyos időpontban. Ez a képesség megbízható elosztott időalapú ütemező megvalósítását valósítja meg.

Az ütemezett üzenetek csak a megadott várólista-idő után materializálódnak a várólistában. Ezt megelőzően az ütemezett üzenetek megszakíthatók. A törlés törli az üzenetet.

Az üzenetek ütemezése ütemezheti a [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) tulajdonságot, amikor üzenetet küld a normál küldési útvonalon keresztül, vagy explicit módon a [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API-val. Ez utóbbi azonnal visszaadja az ütemezett üzenet **SequenceNumber**értékét, amelyet később az ütemezett üzenet visszavonására használhat, ha szükséges. Az ütemezett üzenetek és sorszámuk [az üzenetek böngészésével](message-browsing.md)is felfedezhető.

Az ütemezett üzenetek **SequenceNumber száma** csak akkor érvényes, ha az üzenet ebben az állapotban van. Ahogy az üzenet az aktív állapotba kerül, az üzenet úgy lesz hozzáfűzve a várólistához, mintha az aktuális pillanatban lett volna várólistára, amely magában foglalja egy új **SequenceNumber**hozzárendelését is.

Mivel a szolgáltatás az egyes üzenetekhez van rögzítve, és az üzenetek csak egyszer várólistára sovárhatók, a Service Bus nem támogatja az üzenetek ismétlődő ütemezését.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)