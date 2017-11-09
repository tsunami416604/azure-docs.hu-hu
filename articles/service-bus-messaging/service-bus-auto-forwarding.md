---
title: "Automatikus-továbbítási Azure Service Bus üzenetküldési entitásokról |} Microsoft Docs"
description: "Hogyan tanúsítványlánc egy Service Bus-üzenetsorba vagy egy másik üzenetsor vagy témakör-előfizetéssel."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: sethm
ms.openlocfilehash: 6c92acee9d7609f4fedcddd40563b1a55fa08fac
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Automatikus-továbbítást a Service Bus-entitások láncolás

A Service Bus *automatikus-továbbítási* funkciója lehetővé teszi a tanúsítványlánc egy várólista vagy az előfizetés egy másik várólistához vagy témakör, amely ugyanazt a névteret része. Ha automatikus-továbbítás engedélyezve van, a Service Bus automatikusan kerülnek, az első sor vagy (forrás) előfizetés-kezelési üzenetek eltávolítja, és beírja a második üzenetsor vagy témakör (cél). Ne feledje, hogy továbbra is közvetlenül a cél entitáshoz elküldeni egy üzenetet. Ezenkívül nincs lehetőség a tanúsítványlánc egy al, például a kézbesítetlen levelek várólistájára, egy másik várólistához vagy témakör.

## <a name="using-auto-forwarding"></a>Automatikus-továbbítás használatával
Automatikus-továbbítási beállításával engedélyezheti a [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] vagy [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] tulajdonságainak a [QueueDescription] [ QueueDescription] vagy [SubscriptionDescription] [ SubscriptionDescription] objektumokat a forrás, az alábbi példában látható módon.

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A cél entitás léteznie kell a forrásentitás létrehozásakor. Ha a célként megadott entitás nem létezik, a Service Bus létrehozása a forrásentitás kérdésnél kivételt adja vissza.

Automatikus-továbbítás segítségével horizontális felskálázás témakör megjelenítéséhez. A Service Bus korlátok a [adott témában előfizetések száma](service-bus-quotas.md) 2000 számára. Hozzon létre a második szintű témakörök további előfizetések lehetővé teszi. Vegye figyelembe, hogy akkor is, ha Ön nem köti a Service Bus korlátozás előfizetések számát, a második szintű témakörök hozzáadása javíthatja a teljes átviteli képessége – a témakör.

![Automatikus-továbbítási forgatókönyv][0]

Automatikus-továbbítás segítségével is használata leválasztja a fogadók üzenetküldők. Vegye figyelembe például az ERP rendszer, amely három modulok alkotja: rendelés feldolgozása, a készletkezelést és a felhasználói kapcsolatok kezelése. Az egyes modulok hoz létre, amelyek megfelelő témakörbe várólistán lévő üzenetek. Alice, Bob és, amelyek az ügyfelek kapcsolódnak üzenetek érdekelt értékesítési képviselő. Az üzenetek fogadásához Alice és Bob minden hozzon létre egy személyes várólista és előfizetés egyes a ERP témakörök, amelyek a várólistájuk összes üzenetek automatikus továbbítása.

![Automatikus-továbbítási forgatókönyv][1]

Alice szabadsága, saját személyes várólista ahelyett, hogy a ERP témakör állapotba, ha kitölti-e. Ebben a forgatókönyvben értékesítési képviselőink nem érkezett üzeneteket, mert a ERP témakör sem minden eddiginél elérni kvótát.

## <a name="auto-forwarding-considerations"></a>Automatikus-továbbítási kapcsolatos szempontok

Ha a cél entitás túl sok üzenetek összesít és meghaladja a kvótát, vagy a cél entitás le van tiltva, a forrásentitás hozzáadja-e az üzenetek a [kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md) nincs hely a célként megadott (vagy az entitás újraengedélyezése). Az üzenetek továbbra is a kézbesítetlen levelek várólistájára live, explicit módon kell fogadni, és dolgozza fel őket a a kézbesítetlen levelek várólistájára.

Láncolás együtt az beszerzése egy összetett témakör számos előfizetések témakörök, esetén ajánlott, hogy rendelkezik-e az első szintű témakör előfizetések és a második szintű témakörök sok előfizetések mérsékelt száma. Például egy első szintű témakör 20 előfizetésekkel, azok a második szintű témakör 200 előfizetések kapcsolt lehetővé teszi nagyobb átviteli sebesség eléréséhez, mint 200 előfizetések első szintű témakör egyes kapcsolt a második szintű témakör 20 előfizetések.

A Service Bus egy művelet egyes a továbbított üzenet váltók stb. Például egy üzenetet küld egy témakör 20 előfizetésekkel, azok úgy konfigurálva, hogy egy másik üzenetsor vagy témakör, továbbító automatikus üzenetek terheli 21 műveletek Ha minden első szintű előfizetések fogják megkapni az üzenet másolatának elhelyezését.

Hozzon létre olyan előfizetést, amelyet egy másik üzenetsor vagy témakör legyen láncolva, valamint az előfizetés létrehozója rendelkeznie kell **kezelése** engedélyekkel a forrás- és a cél entitás. A forrás témakör üzenetküldésre csak szükséges **küldése** engedélyekkel a forrás témakör.

## <a name="next-steps"></a>Következő lépések

Automatikus-továbbítási kapcsolatos részletes információkért lásd a következő témaköröket:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

A Service Bus teljesítménnyel kapcsolatos fejlesztések kapcsolatos további információkért lásd: 

* [Ajánlott eljárások használatával a Service Bus üzenetkezelés teljesítménnyel kapcsolatos fejlesztések](service-bus-performance-improvements.md)
* [Particionált üzenetküldési entitások][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
