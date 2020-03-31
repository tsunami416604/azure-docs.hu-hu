---
title: Azure Service Bus - üzenetböngészés
description: Tallózás és betekintés Service Bus üzenetek lehetővé teszi, hogy az Azure Service Bus-ügyfél számba sorolja az összes üzenetet, amely egy várólistában vagy előfizetésben található.
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
ms.openlocfilehash: 6156557d10210535b287aa516070c0b5da416512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539365"
---
# <a name="message-browsing"></a>Üzenetek tallózása

Az üzenetböngészés vagy a betekintés lehetővé teszi, hogy a Service Bus-ügyfél számba vesse a várólistában vagy előfizetésben található összes üzenetet, általában diagnosztikai és hibakeresési célokra.

A betekintő műveletek a várólistában vagy az előfizetési üzenetnaplóban lévő `Receive()` összes `OnMessage()` üzenetet visszaadják, nem csak azokat, amelyek azonnali beszerzésre vagy a ciklussal érhetők el. Az `State` egyes üzenetek tulajdonsága jelzi, hogy az üzenet aktív (fogadható, [elhalasztott](message-deferral.md)vagy [ütemezett](message-sequencing.md).

A felhasznált és lejárt üzeneteket egy aszinkron "szemétgyűjtési" futtatás tisztítja, és nem `Peek` feltétlenül pontosan akkor, amikor az üzenetek lejárnak, és ezért valóban visszaküldhetik a már lejárt és eltávolított üzeneteket, vagy kézbesítetlen betűkkel kerülnek, amikor a várólistán vagy az előfizetésben következő fogadási művelet meghívása következik.

Ez különösen fontos, hogy tartsa szem előtt, amikor megpróbálja helyreállítani a halasztott üzeneteket a várólistából. Egy üzenet, amelynek az [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) azonnali telt el már nem jogosult a rendszeres lekérés bármilyen más módon, még akkor is, ha ez a Peek által küldött. Ezeknek az üzeneteknek a visszaadása szándékos, mivel a Betekintés egy diagnosztikai eszköz, amely a napló aktuális állapotát tükrözi.

A betekintés olyan üzeneteket is visszaad, amelyek zárolva voltak, és amelyeket más fogadók jelenleg dolgoznak fel, de még nem fejeződtek be. Mivel azonban a Betekintés leválasztott pillanatképet ad vissza, az üzenet zárolási állapota nem figyelhető meg a betekintő üzeneteken, és a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) és a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) tulajdonságok [egy InvalidOperationException-et](/dotnet/api/system.invalidoperationexception) dobnak, amikor az alkalmazás megpróbálja olvasni őket.

## <a name="peek-apis"></a>Betekintés API-k

A [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és [peekbatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metódusok minden .NET és Java ügyfélkönyvtárban és az összes fogadóobjektumban léteznek: **MessageReceiver**, **MessageSession**. A betekintés minden várólistán és előfizetésen, valamint a hozzájuk tartozó kézbesítetlen levelek várólistáin működik.

Többszöri hívás esetén a Betekintés metódus számba veszi a várólistában vagy az előfizetési naplóban lévő összes üzenetet sorszámsorrendben, a legalacsonyabb elérhető sorszámtól a legmagasabbig. Ebben a sorrendben állították be az üzeneteket, és nem az a sorrend, amelyben az üzenetek végül beolvashatók.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) beolvas több üzenetet, és visszaadja azokat felsorolásként. Ha nem állnak rendelkezésre üzenetek, az enumerálási objektum üres, nem null.

A metódus túlterhelését [sequencenumber-mal](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) is elindíthatja, majd meghívhatja a paraméter nélküli metódus túlterhelését a további számbavételhez. **PeekBatch** működik egyenértékűen, de beolvassa egy sor üzenetet egyszerre.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
