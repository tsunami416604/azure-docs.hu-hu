---
title: Az Azure Service Bus üzenetkezelő entitásai automatikus továbbítása
description: Ez a cikk ismerteti, hogyan láncolegy Azure Service Bus-várólistát vagy előfizetést egy másik várólistára vagy témakörre.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 8b8883b579233962de61e7247e6ac1cbcb2a6d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761049"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Szolgáltatásbusz-entitások láncolása automatikus továbbítással

A Service Bus *automatikus továbbítási* szolgáltatáslehetővé teszi, hogy egy várólistát vagy előfizetést egy másik várólistához vagy témakörhöz láncoljon, amely ugyanannak a névtérnek a része. Ha az automatikus továbbítás engedélyezve van, a Service Bus automatikusan eltávolítja az első várólistába vagy előfizetésbe (forrásba) helyezett üzeneteket, és a második várólistába vagy témakörbe (célba) helyezi őket. Továbbra is lehetséges, hogy közvetlenül a célentitásnak küldjön üzenetet.

## <a name="using-autoforwarding"></a>Automatikus továbbítás használata

Az automatikus továbbítást a [Forrás QueueDescription][QueueDescription.ForwardTo] vagy [SubscriptionDescription.ForwardTo][SubscriptionDescription.ForwardTo] tulajdonságainak beállításával engedélyezheti a forrás [QueueDescription][QueueDescription] vagy [SubscriptionDescription][SubscriptionDescription] objektumain, ahogy az a következő példában látható:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A célentitásnak léteznie kell a forrásentitás létrehozásakor. Ha a célentitás nem létezik, a Service Bus egy kivételt ad vissza, amikor a forrásentitás létrehozására kérik.

Az automatikus továbbítás segítségével egy adott témakört horizontálisfelskálázhat. A Service Bus [egy adott témakörelőfizetések számát](service-bus-quotas.md) 2000-re korlátozza. További előfizetések elhelyezésére második szintű témakörök létrehozásával. Még akkor is, ha nem köti a Service Bus korlátozása az előfizetések száma, egy második szintű témakörök hozzáadása javíthatja a témakör teljes átviteli.

![Automatikus továbbítási forgatókönyv][0]

Az automatikus továbbítás segítségével leválaszthatja az üzenetfeladókat a fogadóktól. Vegyünk például egy ERP-rendszert, amely három modulból áll: rendelésfeldolgozás, készletkezelés és ügyfélkapcsolatok kezelése. Ezek a modulok mindegyike olyan üzeneteket hoz létre, amelyek a megfelelő témakörbe kerülnek. Alice és Bob olyan értékesítési képviselők, akik érdeklődnek az ügyfelekkel kapcsolatos összes üzenet iránt. Az üzenetek fogadásához Alice és Bob mindegyike létrehoz egy személyes várólistát és egy előfizetést minden olyan ERP-témakörben, amely automatikusan továbbítja az összes üzenetet a várólistájukba.

![Automatikus továbbítási forgatókönyv][1]

Ha Alice megy nyaralni, a személyes sorban, nem pedig az ERP téma, megtelik. Ebben az esetben, mivel egy értékesítési képviselő nem kapott semmilyen üzenetet, az ERP-témakörök egyike sem éri el a kvótát.

> [!NOTE]
> Ha az automatikus továbbítás beállítás van beállítva, az AutoDeleteOnIdle értéke **mind a forráson, mind a célon** automatikusan az adattípus maximális értékére lesz állítva.
> 
>   - A Forrás oldalon az automatikus továbbítás fogadási műveletként működik. Tehát a forrás melyik birtokol autoforwarding beállít van soha igazán " tétlen".
>   - A céloldalon ez annak érdekében történik, hogy mindig legyen cél, ahhoz, hogy továbbítsa az üzenetet.

## <a name="autoforwarding-considerations"></a>Az automatikus továbbítással kapcsolatos szempontok

Ha a célentitás túl sok üzenetet halmoz fel, és túllépi a kvótát, vagy a célentitás le van tiltva, a forrásentitás addig adja hozzá az üzeneteket a [kézbesítetlen levelek várólistájába,](service-bus-dead-letter-queues.md) amíg nincs hely a célban (vagy az entitás újra engedélyezve van). Ezek az üzenetek továbbra is a kézbesítetlen levelek várólistájában élnek, ezért explicit módon kell fogadnia és feldolgoznia őket a kézbesítetlen levelek várólistájából.

Egyes témakörök láncolása, hogy egy összetett témakör sok előfizetéssel, ajánlott, hogy az első szintű témakörben, és számos előfizetések a második szintű témakörökben. Például egy első szintű témakör 20 előfizetéssel, mindegyik láncolva egy második szintű témakör 200 előfizetéssel, lehetővé teszi a nagyobb átviteli, mint egy első szintű témakör 200 előfizetéssel, mindegyik láncolva egy második szintű témakör 20 előfizetéssel.

A Service Bus minden egyes továbbított üzenethez egy műveletet számláz. Például egy 20 előfizetéssel rendelkező témakörnek küldött üzenet, amelyek mindegyike úgy van beállítva, hogy automatikusan továbbítsa az üzeneteket egy másik várólistába vagy témakörbe, 21 műveletként kerül számlázásra, ha az összes első szintű előfizetés megkapja az üzenet másolatát.

Egy másik várólistához vagy témakörhöz láncolt előfizetés létrehozásához az előfizetés létrehozójának kezelési engedéllyel kell **rendelkeznie** mind a forrás, mind a célentitás számára. Az üzenetek nek csak a forrástémakörbe való **küldéséhez a forrástémakörküldési** engedélyek szükségesek.

## <a name="next-steps"></a>További lépések

Az automatikus továbbításról az alábbi témakörökben olvashat részletesen:

* [Továbbítás][QueueDescription.ForwardTo]
* [Várólistaleírása][QueueDescription]
* [Előfizetésleírása][SubscriptionDescription]

Ha többet szeretne megtudni a Service Bus teljesítményének javításáról, olvassa el a következő témakört: 

* [Ajánlott eljárások a teljesítmény javításához a Service Bus-üzenetkezelés használatával](service-bus-performance-improvements.md)
* [Particionált üzenetkezelő entitások][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
