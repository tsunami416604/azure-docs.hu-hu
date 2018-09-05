---
title: Automatikus továbbítással Azure Service Bus-üzenetküldési entitások |} A Microsoft Docs
description: Hogyan összekapcsolja a Service Bus-üzenetsor vagy egy másik üzenetsor vagy témakör-előfizetés.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: f7060778-3421-402c-97c7-735dbf6a61e8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: spelluru
ms.openlocfilehash: 563fa6f38bb5baffb9a4ae86f944b7597d325d30
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698995"
---
# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Láncolás – a Service Bus-entitások automatikus továbbítással

A Service Bus *automatikus továbbítással* funkció lehetővé teszi az üzenetsor vagy egy másik üzenetsor vagy témakör, amely az azonos névtérhez tartozik előfizetés hozzákapcsolva. Ha az automatikus-továbbítás engedélyezve van, a Service Bus automatikusan törli azon üzeneteket, amelyek az első üzenetsorba vagy előfizetésbe (forrás) kerülnek, és áthelyezi őket a második üzenetsorba vagy témakörbe (cél). Fontos megjegyezni, hogy továbbra is lehetséges, egy üzenet küldéséhez a célentitás közvetlenül. Ezenkívül nincs lehetőség összekapcsolja alüzenetsor, például egy, a kézbesítetlen levelek várólistájára, egy másik üzenetsorba vagy témakörbe.

## <a name="using-auto-forwarding"></a>Használja az automatikus továbbítással

Automatikus továbbítással beállításával engedélyezheti a [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] vagy [SubscriptionDescription.ForwardTo] [ SubscriptionDescription.ForwardTo] a tulajdonságok a [QueueDescription] [ QueueDescription] vagy [SubscriptionDescription] [ SubscriptionDescription] objektumokat a forrás, mint a a Példa:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

A célentitás léteznie kell a forrásentitás létrehozásakor. Ha a célentitás nem létezik, a Service Bus kivételt adott vissza, ha a rendszer kéri a forrásentitás létrehozásához adja vissza.

Automatikus továbbítással segítségével horizontális felskálázás egy egyéni témakört. A Service Bus-korlátok a [száma egy adott témakör előfizetések](service-bus-quotas.md) a 2000. További előfizetéseket úgy tud megfelelni, hogy második szintű témakörök létrehozására. Akkor is, ha Ön nem köti a Service Bus-korlátozás előfizetések száma, hozzáadás, a második szintű témakörök javíthatja a témakör általános teljesítményét.

![A forgatókönyv automatikus továbbítással][0]

Automatikus továbbítással különítse el a fogadók üzenetküldők is használhatja. Vegyük példaként az ERP-rendszer, amely három modult áll: feldolgozási készlet kezelése és Ügyfélkapcsolat-kezelés érdekében. Az egyes modulok hoz létre, amelyek a megfelelő témakörben várólistán lévő üzenetek. Alice, Bob és az értékesítési munkatársak, amely kapcsolódik az ügyfelek üzenetek szeretne. Ezeket az üzeneteket fogadni, Alice és Bob egyes létre személyes üzenetsor és a egy előfizetést az egyes az ERP-témakörök, amelyek automatikusan továbbítja az összes üzenetet az üzenetsorba.

![A forgatókönyv automatikus továbbítással][1]

Ha Alice megfelelően a szabadság, saját személyes várólista ahelyett, hogy az ERP-témakörben, tölti. Ebben a forgatókönyvben egy értékesítési képviselő nem kapta meg az üzeneteket, mert az ERP-témakörök egyike minden eddiginél elérni kvótát.

## <a name="auto-forwarding-considerations"></a>Automatikus továbbítással szempontok

A célentitás gyűlnek túl sok üzenet van, és túllépi a kvótát, vagy a célentitás le van tiltva, ha a forrásentitás hozzáadja az üzeneteket a [kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md) a cél (vagy az entitás a rendelkezésre álló helyet újra engedélyezve van). Ezeket az üzeneteket továbbra is a kézbesítetlen levelek várólistájára vonatkozik, az élő kell explicit módon fogadni és feldolgozni azokat a kézbesítetlen levelek várólistájára vonatkozik.

Láncolás együtt egy összetett üzenettémához sok előfizetés beszerzése az egyes témaköröket, ajánlott, hogy az első szintű a témakör az előfizetések és a második szintű témakörök sok előfizetés közepes számú. Például egy első szintű üzenettémához 20 előfizetés, azok egy 200 előfizetések, a második szintű üzenettémához kapcsolt lehetővé teszi, hogy nagyobb teljesítményt nyújt, mint 200 előfizetések első szintű témakör egyes kapcsolt egy második szintű üzenettémához 20 előfizetés.

A Service Bus keresztül számláz az adott műveletnek az egyes továbbított üzenetekhez. Például egy üzenetet küld egy témakörhöz úgy konfigurálva, hogy egy másik üzenetsorba vagy témakörbe, automatikus továbbítása üzeneteket azok 20 előfizetés elszámolási 21 műveletként Ha minden első szintű előfizetés az üzenet másolatot kap.

Egy előfizetés, amely egy másik üzenetsor vagy témakör láncolva létrehozásához rendelkeznie kell az előfizetés létrehozója **kezelés** engedélyeit a forrás- és a célentitás. Üzenetek küldése az a forrás a témakör csak szükséges **küldése** engedélyeit a forrás-témakört.

## <a name="next-steps"></a>További lépések

Automatikus továbbítással kapcsolatos részletes információkért lásd a következő témaköröket:

* [A ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

A Service Bus teljesítménnyel kapcsolatos fejlesztések kapcsolatos további információkért lásd: 

* [Ajánlott eljárások a teljesítmény Service Bus-üzenetkezelés használatával](service-bus-performance-improvements.md)
* [Particionált üzenetküldési entitások][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
