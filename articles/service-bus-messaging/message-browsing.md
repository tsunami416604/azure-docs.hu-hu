---
title: Az Azure Service Bus üzenetek tallózása |} A Microsoft Docs
description: Keresse meg és betekintés a Service Bus-üzenetek
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: spelluru
ms.openlocfilehash: 7ce2e870be0178420d80682bd18adbef814c162f
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857465"
---
# <a name="message-browsing"></a>Üzenetek tallózása

Az üzenetek közötti böngészés vagy Bepillantás, lehetővé teszi, hogy a Service Bus-ügyfélalkalmazást minden üzenetek találhatók egy üzenetsorban vagy előfizetésben, diagnosztikai és hibakeresési célra általában számbavétele.

A betekintési műveletek vissza létező összes üzenetet az üzenetsor vagy előfizetés üzenetek naplóját, nem csak azokat a érhető el az azonnali beszerzési `Receive()` vagy a `OnMessage()` ciklus. A `State` egyes üzenetek tulajdonság jelzi, hogy az üzenet-e aktív (fogadását elérhető), [késleltetett](message-deferral.md), vagy [ütemezett](message-sequencing.md).

Aszinkron által felhasznált és a lejárt üzenetek törlődnek "szemétgyűjtés" futtassa, és nem feltétlenül pontosan mikor üzenetek lejárati idővel rendelkeznek, és ezért `Peek` már lejárt, és eltávolítja üzenetek vagy kézbesítetlen lettered akkor, ha valóban visszaadása a várólista vagy az előfizetés mellett hív fogadási művelet.

Ez különösen fontos, vegye figyelembe, amikor megpróbált helyreállítani a késleltetett üzeneteket az üzenetsorból. Egy üzenet, amelynek a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) azonnali megfelelt már nem jogosult a rendszeres beolvasásához más módon, még akkor is, ha a betekintési vissza. Ezeket az üzeneteket visszaadó szándékos, mivel betekintési egy diagnosztikai eszköz, a napló aktuális állapotát tükröző.

Belepillantás is üzeneteket adja vissza, amely a kizárt és a többi fogadó számára által jelenleg feldolgozott, de még nem fejeződött be. Azonban betekintési adja vissza a kapcsolat nélküli pillanatkép, mert a zárolás állapotát egy üzenet nebyly nalezeny betekinteni az üzenetek, és a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) és [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) tulajdonságok throw egy [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) amikor az alkalmazás megpróbálja beolvasni az őket.

## <a name="peek-apis"></a>API-k megtekintése

A [betekintési/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) mód létezik a klienskódtárak .NET, a Java és az összes fogadó objektumon: **MessageReceiver**, **Lehívása**, **QueueClient**, és **SubscriptionClient**. Belepillantás valamennyi üzenetsorok és -előfizetésekről és a megfelelő kézbesíthetetlen levelek sorai működik.

Ismételten meghívásakor a betekintési metódus enumerálása összes üzenetet, amelyek szerepelnek a várólista vagy előfizetési napló sorrendben feladatütemezési száma a legalacsonyabb elérhető sorszámot, amely a legmagasabb. Ez a sorrend, amelyben üzenetek várólistára helyezett is, és nem a sorrendet, amelyben üzenetek idővel előfordulhat, hogy olvassa.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) beolvassa a több, és a egy enumerálás adja vissza őket. Ha nincsenek üzenetek elérhetők, enumerálás objektum üres, nem null értékű.

A módszer a túlterhelés rendezik is egy [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , amellyel elindításához, és ezután hívja meg a paraméter nélküli metódus túlterhelését további számbavétele. **PeekBatch** év függvényekre, azonban egy üzenetkészletet egyszerre beolvasása.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
