---
title: Az Azure Service Bus message lejárati |} Microsoft Docs
description: Lejárat és az Azure Service Bus üzenetek élettartama
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
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196768"
---
# <a name="message-expiration-time-to-live"></a>Üzenet lejárati (Live idő)

A tartalom egy üzenetet, vagy parancsot vagy lekérdezést, amely egy üzenetet tartalmát egy fogadóra belül szinte mindig az alkalmazásszintű lejárati határidő működik valamilyen formában. A határidő után már nem a tartalom kézbesítésével, vagy már nem a kért művelet végrehajtása.

Fejlesztési és tesztkörnyezetek is, amelyekben üzenetsorok és témakörök gyakran használnak az alkalmazások és az alkalmazás részei részleges futtatása keretében is kívánatos történő automatikus szemétgyűjtő, hogy a következő vizsgálat futtatása is kimaradt teszt üzenetek Indítsa el a tiszta.

Minden egyes üzenet lejárati beállításával szabályozhatja a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) rendszer tulajdonság, amely egy relatív időtartamát határozza meg. A lejárati abszolút azonnali lesz, ha az üzenet a várólistában levő entitásban. Ekkor a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) tulajdonság időt vesz igénybe, az az érték [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Elmúlt a **ExpiresAtUtc** azonnali, üzenetek válnak a lekérdezés nem jogosult. A lejárati nem befolyásolja az üzeneteket, amelyek azonban jelenleg zárolva vannak kézbesítésre; az üzenetek továbbra is megfelelően kezeli. A zárolás lejár, vagy az üzenetet a rendszer félbehagyná, ha a lejárati azonnal érvénybe lép.

Bár az üzenetet a zárolási, az alkalmazás egy üzenetet, amely lejárt birtokában lehet. Hogy az alkalmazás hajlandó feldolgozási használatától, vagy úgy dönt, hogy az üzenet abandon esetén a végrehajtója.

## <a name="entity-level-expiration"></a>Entitásszintű lejárata

Az üzenetsor vagy témakör küldött összes üzenet lépnek egy alapértelmezett lejárati, hogy az entitás nem állítják a szint a [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonság, és amely is létrehozása a portálon beállított és később módosítani. Az alapértelmezett lejárati szolgál az entitás küldött összes üzenet ahol [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nincs megadva. Az alapértelmezett lejárati olyan funkciókkal, mint a felső határ az **TimeToLive** érték. Üzeneteket, amelyek hosszabb **TimeToLive** lejárati, mint az alapértelmezett érték csendes van igazítva a **defaultMessageTimeToLive** mielőtt a várólistán lévő érték.

A lejárt üzenetek szükség lehet áthelyezni egy [kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md) úgy, hogy a [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) tulajdonság, vagy jelölje be a portál a megfelelő négyzetet. Ha a beállítás le van tiltva, a rendszer eldobja a lejárt üzenetek. Lejárt a kézbesítetlen levelek várólistájára helyezett üzeneteket meg lehet különböztetni a többi kézbesítetlen lettered üzenet kiértékelésével a [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) , amely a broker tárolja a felhasználói tulajdonságok szakaszának; tulajdonság értéke [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) ebben az esetben.

A fent említett esetében, amelyben az üzenet lejárati közben a zárolást, és ha a jelző be van állítva az entitás védve az üzenet átkerül a kézbesítetlen levelek várólistájára rendszer félbehagyná a zárolást, illetve lejár. Azonban nem áthelyezés Ha az üzenet sikeresen rendezik, ami azt feltételezi, hogy az alkalmazás sikeresen kezelt, függetlenül a névleges lejárati.

A kombinációja [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) és automatikus (és tranzakciós) kézbesítetlen levelek kezelése lejártakor e egy feladatot, a kezelő vagy a határidőt a kezelők csoporthoz megadott lekérése az vetett bizalmat létrehozó értékes eszközt feldolgozásával a határidő elérésekor.

Vegye figyelembe például egy webhely, amely megbízhatóan végrehajtani a feladatokat a skála korlátozott háttérkiszolgálón van szüksége, és amely alkalmanként lép forgalom napra, vagy el akar ellen, hogy a háttér rendelkezésre állási részének elhelyezni. A normál esetben a elküldött adatokat a kiszolgálóoldali kezelőt leküldéses értesítések az adatokat a várólistán, és ezt követően erősítse meg a tranzakció egy válasz várólistára sikeres kezelésének válasz érkezik. Ha a forgalom csúcs, és a háttér-kezelő nem tudja feldolgozni a várakozó elemek időben, a lejárt feladatok vannak vissza a kézbesítetlen levelek várólistájára. Az interaktív felhasználó is értesíti, hogy a kért művelet kissé a szokásosnál hosszabb ideig tart, és a kérelem majd elhelyezhető egy másik várólistához feldolgozási út ahol végleges feldolgozási eredménye a felhasználó által küldött e-mailek. 

## <a name="temporary-entities"></a>Ideiglenes entitások

Service Bus-üzenetsorok, témakörök és előfizetések ideiglenes entitások, amely automatikusan törlődnek, amikor azok nem használtak egy adott időn hozhatók létre.
 
Az automatikus tisztítás akkor hasznos, a fejlesztési és tesztelési forgatókönyvek, amelyben entitások dinamikusan jönnek létre, és nem használja, a vizsgálat vagy a hibakeresési futtatja néhány megszakadásának miatt után törlődnek. Ez akkor hasznos, ha egy alkalmazás hoz létre a dinamikus entitások, például egy válasz várólista, a válaszok vissza egy webkiszolgáló-folyamat vagy egy másik viszonylag rövid élettartamú objektum nehéz entitásokból megbízhatóan karbantartása során az objektum megjelenik a példány.

A szolgáltatás engedélyezve van, használja a [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonság, amely az időtartam, amelyre egy entitás kell van beállítva üresjárati (nem használt), mielőtt a rendszer automatikusan törli. A minimális időtartam érték 5 perc.
 
A **autoDeleteOnIdle** tulajdonságot kell beállítani, a .NET-keretrendszer ügyfél keresztül vagy egy Azure Resource Manager művelet [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k. A portálon keresztül nem állítható be.


## <a name="next-steps"></a>További lépések

Tudhat meg többet a Service Bus üzenetkezelés, a következő témakörökben:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)