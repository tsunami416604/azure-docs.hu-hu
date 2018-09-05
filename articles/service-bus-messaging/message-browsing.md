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
ms.date: 01/25/2018
ms.author: spelluru
ms.openlocfilehash: bafc08eae4a32f803f485097401a586a662f64e9
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700407"
---
# <a name="message-browsing"></a>Üzenetek tallózása

Üzenetek tallózása ("Bepillantás") lehetővé teszi, hogy egy Service Bus-ügyfélalkalmazást enumerálni az összes üzenetet a hozzá tartozó, egy üzenetsorban vagy előfizetésben, általában a diagnosztikai és hibakeresési célra.

A betekintési műveletek vissza létező összes üzenetet az üzenetsor vagy előfizetés üzenetek naplóját, nem csak azokat a érhető el az azonnali beszerzési `Receive()` vagy a `OnMessage()` ciklus. A `State` egyes üzenetek tulajdonság jelzi, hogy az üzenet-e aktív (fogadását elérhető), [késleltetett](message-deferral.md), vagy [ütemezett](message-sequencing.md).

Aszinkron által felhasznált és a lejárt üzenetek törlődnek "szemétgyűjtés" futtassa, és nem feltétlenül pontosan mikor üzenetek lejárati idővel rendelkeznek, és ezért `Peek` már lejárt, és eltávolítja üzenetek vagy kézbesítetlen lettered akkor, ha valóban visszaadása a várólista vagy az előfizetés mellett hív fogadási művelet.

Ez különösen fontos, vegye figyelembe, amikor megpróbált helyreállítani a késleltetett üzeneteket az üzenetsorból. Egy üzenet, amelynek a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) azonnali megfelelt már nem jogosult a rendszeres beolvasásához más módon, még akkor is, ha a betekintési vissza. Ezeket az üzeneteket visszaadó szándékos, mivel betekintési egy diagnosztikai eszköz, a napló aktuális állapotát tükröző.

Belepillantás is, amely a kizárt és a többi fogadó számára által jelenleg feldolgozott, de még nem töltött üzeneteket adja vissza. Azonban betekintési adja vissza a kapcsolat nélküli pillanatkép, mert a zárolás állapotát egy üzenet nebyly nalezeny betekinteni az üzenetek, és a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) és [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) tulajdonságok throw egy [ InvalidOperationException](/dotnet/api/system.invalidoperationexception) amikor az alkalmazás megpróbálja beolvasni az őket.

## <a name="peek-apis"></a>API-k megtekintése

A [betekintési/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) mód létezik a klienskódtárak .NET, a Java és az összes fogadó objektumon: **MessageReceiver**, **Lehívása**, **QueueClient**, és **SubscriptionClient**. Belepillantás valamennyi üzenetsorok és -előfizetésekről és a megfelelő kézbesíthetetlen levelek sorai működik.

Ismételten meghívásakor a betekintési metódus enumerálása összes üzenetet, amelyek szerepelnek a várólista vagy előfizetési napló sorrendben feladatütemezési száma a legalacsonyabb elérhető sorszámot, amely a legmagasabb. Ez az, hogy a sorrendet, amelyben üzenetek voltak a várólistán lévő; akkor sem, amelyben üzenetek idővel előfordulhat, hogy olvassa a sorrendben.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) beolvassa a több, és a egy enumerálás adja vissza őket. Ha nincsenek üzenetek elérhetők, enumerálás objektum üres, nem null értékű.

A módszer a túlterhelés rendezik is egy [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , amellyel elindításához, és ezután hívja meg a paraméter nélküli metódus túlterhelését további számbavétele. **PeekBatch** év függvényekre, azonban egy üzenetkészletet egyszerre beolvasása.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)