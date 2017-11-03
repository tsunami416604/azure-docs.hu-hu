---
title: "Az Azure Service Bus message halasztás |} Microsoft Docs"
description: "A Service Bus üzenetek kézbesítés"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: sethm
ms.openlocfilehash: f84b870de4b79399d5edc90284c9c56222156b5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="message-deferral"></a>Üzenet halasztás

Ha a várólista vagy előfizetés ügyfél, hogy hajlandó feldolgozni, de a feldolgozás nincs jelenleg lehetőség miatt különleges körülmények belül az alkalmazás, "késleltető" lekérés az üzeneteket, ha később lehetősége van üzenetet kap. Az üzenet a várólistában vagy előfizetés marad, de fenntartott van beállítva.

Halasztás egy olyan szolgáltatás, kifejezetten létrehozott munkafolyamat-forgatókönyvek feldolgozása. Munkafolyamat keretrendszerek előfordulhat, hogy bizonyos műveletek végrehajtását, meghatározott sorrendben feldolgozásra, és előfordulhat, hogy a többi üzenet arról tájékoztatja előírt előzetes munka befejezéséig, halassza el néhány fogadott üzenetek feldolgozása.

Ez egy egyszerű szemléltető példa, amelyben egy külső fizetési szolgáltató fizetési értesítéseinek rendszerben jelenik meg, a rendszer előtt a megfelelő megrendelés propagált tároló elölről a teljesítése feldolgozási sorrendben. Ebben az esetben a teljesítése rendszer késleltető előfordulhat, hogy a fizetési értesítés feldolgozása addig, amíg nincs egy rendelés társítandó azt. Szinkronizálási forgatókönyvekben, ahol különböző forrásokból származó üzenetek meghajtó munkafolyamat előre, a valós idejű végrehajtási sorrendje valóban megfelelő lehet, de tükröző eredményeit az üzenetek előfordulhat, hogy nem megfelelő sorrendben érkeznek.

Végső soron halasztás támogatások, amelyben azok dolgozhatók, miközben az üzenetek biztonságosan mely feldolgozási igényeken napjainak üzenet áruházban rendeléshez a érkezési sorrendben üzeneteit újra sorrendben.

## <a name="message-deferral-apis"></a>Üzenet halasztás API-k

Az API [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) vagy [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) a .NET-keretrendszer ügyfél [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) a .NET-szabvány ügyfélprogram, és **mesageReceiver.defer** vagy **messageReceiver.deferSync** a Java-ügyfél. 

A fő várakozási sorba együtt az összes többi aktív üzenet (ellentétben a kézbesítetlen levelek egy alárendelt várólista élnek) késleltetett üzenetek megmaradnak, de azok már nem fogadhatók a rendszeres Receive/ReceiveAsync függvények használatával. Késleltetett üzenetek keresztül észlelhetők [üzenet böngészés](message-browsing.md) Ha egy alkalmazás elveszti nyomon őket.

Késleltetett üzenet beolvasása, a "tulajdonos" felelős, a jelszóelőzmények a [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , ez eltér az. A fogadó, hogy ismeri a **SequenceNumber** késleltetett üzenet később fogadhat kifejezetten Receive(sequenceNumber), az üzenet.

Ha egy üzenet feldolgozása sikertelen, mert egy adott erőforráshoz, az üzenet kezelése átmenetileg nem érhető el, de kell üzenet feldolgozása nem summarily felfüggeszteni, egy elegáns, amelyre az üzenetet a oldalon néhány percig módja jegyezze meg a **SequenceNumber** a egy [ütemezett üzenet](message-sequencing.md) közzé lehet tenni, néhány perc múlva, és újra letölteni a késleltetett üzenetet, az ütemezett üzenet érkezésekor. Vegye figyelembe, hogy egy üzenetkezelő minden műveletnél adatbázis függ, és, hogy az adatbázis átmenetileg nem érhető el, ha azt kell halasztás használja, de ahelyett, hogy az üzenetek fogadására felfüggesztése elemet, amíg az adatbázis újból elérhető lesz.

Üzenetek késleltető nem befolyásolja az üzenet lejárati, azaz késleltetett üzenetek továbbra is az eredetileg megadott időpontban lejár és áthelyezik a kézbesítetlen levelek várólistájára kerülő Ha így vannak konfigurálva.

## <a name="next-steps"></a>Következő lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)