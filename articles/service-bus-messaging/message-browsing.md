---
title: Azure Service Bus – üzenetek tallózása
description: A Tallózás és betekintés Service Bus üzenetek lehetővé teszik, hogy egy Azure Service Bus ügyfél enumerálja az üzenetsor vagy előfizetés összes üzenetét.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 0f2d4ed1225aef4c28a5f3d841669c2e3122ba10
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341242"
---
# <a name="message-browsing"></a>Üzenetek tallózása

Az üzenetek böngészése vagy betekintése lehetővé teszi, hogy egy Service Bus ügyfél enumerálja az összes várólistán vagy előfizetésen belüli üzenetet, jellemzően diagnosztikai és hibakeresési célokra.

A betekintés művelet a várólista vagy az előfizetési üzenet naplójában található összes üzenetet visszaküldi, nem csak az azonnali beszerzéshez `Receive()` vagy a hurokhoz rendelkezésre álló üzeneteket `OnMessage()` . Az `State` egyes üzenetek tulajdonsága jelzi, hogy az üzenet aktív-e (elérhető), [késleltetve](message-deferral.md)vagy [ütemezve](message-sequencing.md).

A felhasznált és a lejárt üzeneteket egy aszinkron "Garbage Collection" futtatja, és nem feltétlenül pontosan az üzenetek lejárata után, ezért `Peek` Előfordulhat, hogy a már lejárt üzeneteket is visszaküldi, és a rendszer a várólistára vagy előfizetésre vonatkozóan a fogadási művelet következő meghívásakor eltávolítja vagy kézbesíti azt.

Ez különösen fontos a késleltetett üzenetek várólistából történő helyreállítására tett kísérlet során. Egy üzenet, amely esetében a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) Instant átadása már nem jogosult a rendszeres lekérésre bármilyen más módon, még akkor is, ha azt a betekintés is visszaadja. Az üzenetek visszaküldése szándékosan történik, mivel a betekintés egy diagnosztikai eszköz, amely a napló aktuális állapotát tükrözi.

A betekintés a zárolt és jelenleg más fogadók által feldolgozott üzeneteket is visszaadja, de még nem fejeződött be. Mivel azonban a betekintés egy leválasztott pillanatképet ad vissza, egy üzenet zárolási állapota nem figyelhető meg a beérkező üzenetekben, és a [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) és a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) tulajdonság eldobjon egy [InvalidOperationException](/dotnet/api/system.invalidoperationexception) , amikor az alkalmazás megkísérli a beolvasást.

## <a name="peek-apis"></a>Betekintési API-k

A [betekintés/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) és a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) metódus minden .net-és Java-ügyfél-függvénytárban és minden fogadó objektumon megtalálható: **MessageReceiver**, **MessageSession**. A betekintés az összes várólistán és előfizetésen, valamint a hozzájuk tartozó kézbesítetlen levelek várólistáján működik.

Ha többször is meghívják, a betekintés metódus a várólista vagy az előfizetési naplóban található összes üzenetet enumerálja sorszám sorrendben, a legalacsonyabb rendelkezésre álló sorozatszámtól a legmagasabb értékig. Ez az a sorrend, amelyben az üzenetek várólistán lévő, és nem az, hogy milyen sorrendben lehet az üzenetek lekérése.

A [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) több üzenetet kér le, és visszaadja őket enumerálásként. Ha nem áll rendelkezésre üzenet, a számbavételi objektum üres, nem null értékű.

Azt is megteheti, hogy túlterheli a metódust egy olyan [sorszám](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , amelyen el szeretné indítani, majd a paraméter nélküli metódus túlterhelésének meghívásával adja meg a további enumerálást. A **PeekBatch** függvények egyenértékűek, de egyszerre lekérik az üzenetek készletét.

## <a name="next-steps"></a>További lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
