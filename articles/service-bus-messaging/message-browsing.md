---
title: "Az Azure Service Bus message böngészés |} Microsoft Docs"
description: "Tallózással keresse meg és betekintés a Service Bus-üzenetek"
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
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 124c4592a41bf9f3e2a148ba5c3b928bb051d160
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="message-browsing"></a>Üzenet tallózása

Böngészés ("megtekintésekor") üzenet lehetővé teszi, hogy a Service Bus-ügyfélalkalmazást minden üzenet szereplő üzenetsor vagy előfizetés, általában a diagnosztikai és hibakeresési célra számbavétele.

A betekintés műveletek üzeneteket visszaküldeni, minden létező a várólista vagy az előfizetési üzenet napló, nem csak azokat a érhető el az azonnali beszerzési `Receive()` vagy a `OnMessage()` hurok. A `State` tulajdonság minden üzenet közli aktív-e az üzenet (csak akkor kapja elérhető), [késleltetett](message-deferral.md), vagy [ütemezett](message-sequencing.md).

Egy aszinkron által felhasznált és a lejárt üzenetek megtisztítva "szemétgyűjtés" futtatni, és nem feltétlenül pontosan amikor üzenetek lejár, és ezért `Peek` már lejárt, és eltávolítja üzenetek vagy kézbesítetlen lettered ha valóban vissza a fogadási művelet mellett meghívták a várólista vagy előfizetés.

Ez különösen fontos szem előtt tartani, amikor megpróbált helyreállítani késleltetett üzenetet az üzenetsorból. Egy üzenet, amelynek a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) azonnali megfelelt most már a rendszeres beolvasásához bármilyen más módon támogatható, akkor is, ha a betekintés vissza. Ezek az üzenetek visszaadó is szándékos, mivel a betekintés egy diagnosztikai eszköz, amely tükrözi a napló jelenlegi állapotában.

Betekintés is jelenít meg, amely zárolva volt és a többi fogadó által jelenleg feldolgozott, de még nem fejeződött. Azonban a betekintés leválasztott pillanatkép adja vissza, mert a zárolás állapotát egy üzenetet nem figyelhetők meg peeked üzeneteken, és a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) és [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) tulajdonságok throw egy [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) amikor az alkalmazás megkísérli beolvasni azokat.

## <a name="peek-apis"></a>Betekintés API-k

A [betekintés/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) mód létezik a .NET és a Java klienskódtárak segítségével, valamint az összes fogadó objektumok: **MessageReceiver**, **MessageSession**, **QueueClient**, és **SubscriptionClient**. Az összes várólistán és előfizetések és a megfelelő kézbesítetlen levelek várólistájának betekintés működik.

Ismételten meghívásakor a betekintés metódus enumerálása összes üzenet, amely szerepel a várólista vagy előfizetési napló sorrendben feladatütemezési szám, a legalacsonyabb elérhető sorszámot, amely a legmagasabb. Ez az, hogy a sorrendet, amelyben üzeneteket volt a várólistában levő; a sorrendet, amelyben üzenetek idővel előfordulhat, hogy olvassa nincs.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) beolvassa a több és enumerálást ad vissza. Nem érhetők el, ha a számbavételi objektum, üres, nem null értékű.

Is magtípusú leképezést túlterhelés a metódus egy [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , amellyel indítása, és akkor hívja meg a paraméter nélküli módszer túlterhelés további enumerálása. **PeekBatch** év működik, de egyszerre kéri le az üzenetek készleteit.

## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)