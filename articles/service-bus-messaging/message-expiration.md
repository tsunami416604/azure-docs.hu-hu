---
title: Azure Service Bus – üzenet lejárata
description: Ez a cikk ismerteti az Azure Service Bus-üzenetek lejárati idejét és élettartamát. Egy ilyen határidő után az üzenet már nem kézbesítve.
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
ms.openlocfilehash: e86c92fa1cfb13929d5617502224f479709efdd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756334"
---
# <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (élettartama)

Az üzenetben lévő hasznos adatra, illetve az üzenet fogadóhoz továbbított parancsra vagy lekérdezésre szinte mindig vonatkozik valamilyen alkalmazásszintű lejárati határidő. Egy ilyen határidő után a tartalom már nem lesz kézbesítve, vagy a kért művelet már nem hajtva végre.

Olyan fejlesztési és tesztelési környezetekben, amelyekben a várólistákat és témaköröket gyakran használják az alkalmazások vagy alkalmazásrészek részleges futtatásának összefüggésében, kívánatos, hogy a sodort tesztüzenetek automatikusan összegyűjtve legyenek, hogy a következő tesztsorozat kezdje tiszta.

Bármely egyes üzenet lejárata a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) rendszertulajdonság beállításával szabályozható, amely meghatározza a relatív időtartamot. A lejárat abszolút azonnalivá válik, amikor az üzenet várólistára kerül az entitásba. Ekkor az [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) tulajdonság átveszi az értéket [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive). A közvetített üzenet en az élő (TTL) beállítás nem érvényesíthető, ha nincsenek aktívan figyelő ügyfelek.

Az **ExpiresAtUtc** azonnali után az üzenetek nem lesznek lehívhatók. A lejárat idáig nem érinti a jelenleg kézbesítésre zárolt üzeneteket; ezeket az üzeneteket továbbra is a szokásos módon kezelik. Ha a zárolás lejár, vagy az üzenet megszűnik, a lejárat azonnali hatállyal érvénybe lép.

Amíg az üzenet zárolva van, előfordulhat, hogy az alkalmazás birtokában van egy lejárt üzenet. Hogy az alkalmazás hajlandó-e folytatni a feldolgozást, vagy úgy dönt, hogy hagyjon fel az üzenet akár a kivitelező.

## <a name="entity-level-expiration"></a>Entitásszintű lejárat

A várólistába vagy témakörbe küldött összes üzenet relevanciái egy [alapértelmezettMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonsággal rendelkező entitás szinten beállított alapértelmezett lejárati időnek vannak kitéve, amely a létrehozás során és később is beállítható a portálon. Az alapértelmezett lejárati rendszer az összes olyan üzenethez használatos, amelyet a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nincs explicit módon beállítva. Az alapértelmezett lejárati idő a **TimeToLive** érték felső határaként is működik. Azok az üzenetek, amelyek nek hosszabb **a TimeToLive** lejárata, mint az alapértelmezett érték, a várólistára kerülés előtt csendben a **defaultMessageTimeToLive** értékre módosulnak.

> [!NOTE]
> A közvetített üzenetek alapértelmezett [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) értéke [a TimeSpan.Max,](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) ha nincs másképp megadva.
>
> Az üzenetkezelő entitások (várólisták és témakörök) esetében az alapértelmezett lejárati idő is [TimeSpan.Max](https://docs.microsoft.com/dotnet/api/system.timespan.maxvalue) a Service Bus standard és a prémium szintek.  Az alapszintű szint esetében az alapértelmezett lejárati idő 14 nap.

A lejárt üzenetek tetszés szerint áthelyezhetők egy [kézbesítetlen levelek várólistájába](service-bus-dead-letter-queues.md) az [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) tulajdonság beállításával, vagy a megfelelő jelölőnégyzet bejelölésével a portálon. Ha a beállítás le van tiltva, a lejárt üzenetek eldobásra kerülnek. A kézbesítetlen levelek várólistájába áthelyezett lejárt üzenetek megkülönböztethetők a többi kézbesítetlen levelektől, ha kiértékeli a [kézbesítetlen levélok](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) tulajdonságát, amelyet a bróker a felhasználói tulajdonságok szakaszban tárol; Az érték ebben az esetben [TTLExpiredException.](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq)

A fent említett esetben, amikor az üzenet zárolt ként védett a lejárattól, és ha a jelző az entitáson van beállítva, az üzenet a kézbesítetlen levelek várólistájába kerül, mivel a zárolás elvan hagyva vagy lejár. Azonban nem kerül áthelyezésre, ha az üzenet sikeresen kiegyenlített, amely azt feltételezi, hogy az alkalmazás sikeresen kezelte azt, annak ellenére, hogy a névleges lejárati.

A [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) és az automatikus (és tranzakciós) kézbesítési kézbesítési idő kombinációja értékes eszköz a bizalom megteremtésére abban, hogy a határidő lejártakor egy kezelőnek vagy kezelők egy csoportjának adott feladat feldolgozásra kerül-e.

Például fontolja meg egy olyan webhelyet, amelynek megbízhatóan kell végrehajtania a feladatokat egy méretezési kényszerített háttérrendszeren, és amely időnként forgalmi csúcsokat tapasztal, vagy szigetelni szeretne az adott háttérrendszer rendelkezésre álló epizódjaitól. Normál esetben a benyújtott felhasználói adatok kiszolgálóoldali kezelője az adatokat egy várólistába küldi, és ezt követően a tranzakció sikeres kezelését megerősítő választ kap egy válaszvárólistába. Ha van egy forgalmi csúcs, és a háttér-kezelő nem tudja feldolgozni a hátralék elemek időben, a lejárt feladatok a kézbesítetlen levelek várólistán. Az interaktív felhasználó értesítést kaphat arról, hogy a kért művelet a szokásosnál kicsit tovább fog tartani, és a kérés egy másik várólistára helyezhető egy feldolgozási útvonalhoz, ahol a végleges feldolgozási eredményt e-mailben küldik el a felhasználónak. 


## <a name="temporary-entities"></a>Ideiglenes entitások

A Service Bus-várólisták, -témakörök és -előfizetések ideiglenes entitásként hozhatók létre, amelyek automatikusan törlődnek, ha egy adott ideig nem használták őket.
 
Az automatikus karbantartás olyan fejlesztési és tesztelési forgatókönyvekben hasznos, amelyekben az entitások dinamikusan jönnek létre, és használat után nem törlődnek a teszt vagy a hibakeresés néhány megszakítása miatt. Akkor is hasznos, ha egy alkalmazás dinamikus entitásokat hoz létre, például egy válaszvárólistát, hogy válaszokat kapjon egy webkiszolgálói folyamatba, vagy egy másik, viszonylag rövid életű objektumba, ahol nehéz megbízhatóan megtisztítani ezeket az entitásokat, amikor az objektum mint eltűnik.

A szolgáltatás engedélyezve van az [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonság használatával. Ez a tulajdonság arra az időtartamra van beállítva, amelyalatt az entitásnak automatikustörlés előtt tétlennek (nem használtnak) kell lennie. A tulajdonság minimális értéke 5.
 
Az **autoDeleteOnIdle** tulajdonságot egy Azure Resource Manager-művelettel vagy [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) a . Nem állíthatja be a portálon.

## <a name="idleness"></a>Semmittevés

Az entitások (várólisták, témakörök és előfizetések) tétlenségét a következőknek tekintik:

- Üzenetsorok
    - Nincs küldés  
    - Nem kap  
    - Nincs frissítés a várólistán  
    - Nincsenek ütemezett üzenetek  
    - Nincs böngészés/betekintés 
- Témakörök  
    - Nincs küldés  
    - Nincs frissítés a témához  
    - Nincsenek ütemezett üzenetek 
- Előfizetések
    - Nem kap  
    - Nincs frissítés az előfizetéshez  
    - Nincs új szabály hozzáadva az előfizetéshez  
    - Nincs böngészés/betekintés  
 


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Service Bus üzenetküldéséről, olvassa el az alábbi témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
