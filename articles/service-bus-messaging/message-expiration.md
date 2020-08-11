---
title: Azure Service Bus – üzenet lejárata
description: Ez a cikk a Azure Service Bus üzenetek érvényességének lejáratát és időpontját ismerteti. Az ilyen határidő lejárta után az üzenet már nem érkezik meg.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 41711428711533a6ecac449f59d415e86474545b
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064723"
---
# <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (élettartama)

Az üzenetben szereplő hasznos adatok, illetve az üzenetek fogadóknak küldött utasításait vagy lekérdezéseit szinte mindig az alkalmazási szintű lejárati határidő valamilyen formája adja. Az ilyen határidő lejárta után a rendszer már nem továbbítja a tartalmat, vagy a kért művelet már nem lesz végrehajtva.

Olyan fejlesztési és tesztelési környezetekben, amelyekben gyakran használják a várólistákat és a témaköröket az alkalmazások és az alkalmazások részleges futtatásának kontextusában, azt is érdemes használni, hogy a rendszer az átállási teszteket automatikusan begyűjtse, hogy a következő teszt futtatása megtisztítsa.

Az egyes üzenetek elévülését a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) rendszertulajdonság beállításával szabályozhatja, amely a relatív időtartamot határozza meg. Ha az üzenetet várólistán lévő az entitásba, a lejárati idő abszolút lesz. Ekkor a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) tulajdonság a [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc)  +  [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)értéket veszi igénybe. A felügyelt üzenetekben az élettartam (TTL) beállítása nem lép érvénybe, ha nincsenek aktívan figyelt ügyfelek.

A **ExpiresAtUtc** azonnali lekérése után az üzenetek nem lesznek jogosultak a beolvasásra. A lejárat nem befolyásolja a kézbesítéshez jelenleg zárolt üzeneteket. ezeket az üzeneteket a rendszer továbbra is szabályosan kezeli. Ha a zárolás lejár, vagy az üzenet el lett hagyva, a lejárat azonnal érvénybe lép.

Amíg az üzenet zárolva van, előfordulhat, hogy az alkalmazás lejárt üzenettel rendelkezik. Azt jelzi, hogy az alkalmazás készen áll-e a feldolgozásra, vagy úgy dönt, hogy elhagyja az üzenetet a végrehajtónak.

## <a name="entity-level-expiration"></a>Entitások szintjének lejárata

A várólistába vagy témakörbe küldött összes üzenet egy alapértelmezett lejáratra van beállítva, amely az entitás szintjén van megadva a [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonsággal, amelyet később a portálon is beállíthat a létrehozás és a módosítás során. Az alapértelmezett lejáratot a rendszer az entitásba küldött összes üzenethez használja, ahol a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nincs explicit módon beállítva. Az alapértelmezett lejárat a **TimeToLive** értékének felső határa is. Az alapértelmezett értéknél hosszabb **TimeToLive** lejáratú üzeneteket a rendszer a várólistán lévő előtt csendben igazítja a **defaultMessageTimeToLive** értékre.

> [!NOTE]
> A felügyelt üzenetek alapértelmezett [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) értéke [TimeSpan. max](/dotnet/api/system.timespan.maxvalue) , ha nincs másképp megadva.
>
> Az üzenetküldési entitások (várólisták és témakörök) esetében az alapértelmezett lejárati idő a [TimeSpan. Max](/dotnet/api/system.timespan.maxvalue) Service Bus a standard és a prémium szint esetében is.  Az alapszintű csomag esetében az alapértelmezett lejárati idő 14 nap.

A lejárt üzenetek a [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) tulajdonság beállításával, vagy a portál megfelelő mezőjének megadásával is áthelyezhetők egy [kézbesítetlen levelek várólistába](service-bus-dead-letter-queues.md) . Ha a beállítás le van tiltva, a rendszer elveti a lejárt üzeneteket. A kézbesítetlen levelek várólistára áthelyezett lejárt üzenetek megkülönböztetni más kézbesítetlen üzenetektől, ha kiértékeli a [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) tulajdonságot, amelyet a közvetítő tárol a felhasználó tulajdonságai szakaszban; Ebben az esetben az érték [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) .

Abban az esetben, amikor az üzenet védett a lejárati idő alatt a zárolás alatt, és ha a jelző be van állítva az entitásra, a rendszer áthelyezi az üzenetet a kézbesítetlen levelek várólistába, mivel a zárolás elhagyható vagy lejár. Azonban a rendszer nem helyezi át, ha az üzenet sikeres rendezése megtörtént, ami azt feltételezi, hogy az alkalmazás sikeresen kezelte azt a névleges lejárati idő ellenére.

A [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) és az automatikus (és tranzakciós) lejárati idő kombinációja olyan értékes eszköz, amellyel biztos lehet abban, hogy egy kezelőnek vagy kezelői csoportnak adott feladatot a határidő elérésekor lekéri a rendszer.

Tegyük fel például, hogy egy olyan webhelynek, amely megbízhatóan hajtja végre a feladatokat egy méretezéssel korlátozott háttérrendszer esetében, és amely esetenként a forgalmi csúcsokat tapasztalja, vagy az adott háttérrendszer rendelkezésre állási epizódjában szeretne szigetelni. A normál esetben az elküldött felhasználói adatok kiszolgálóoldali kezelője leküldi az adatokat egy várólistába, és később a tranzakció sikeres kezelését a válasz-várólistába fogadja. Ha van forgalmi tüske, és a háttér-kezelő nem tudja időben feldolgozni a várakozó elemeket, a rendszer a lejárt feladatokat a kézbesítetlen levelek várólistáján adja vissza. Az interaktív felhasználó értesítést kaphat arról, hogy a kért művelet a szokásosnál valamivel hosszabb ideig tart, és a kérést egy másik várólistára helyezheti a feldolgozási útvonalon, ahol a végleges feldolgozási eredményeket a rendszer e-mailben küldi el a felhasználónak. 


## <a name="temporary-entities"></a>Ideiglenes entitások

Service Bus várólisták, témakörök és előfizetések ideiglenes entitásként hozhatók létre, amelyeket a rendszer automatikusan eltávolít, ha azokat nem használták megadott ideig.
 
Az automatikus tisztítás olyan fejlesztési és tesztelési helyzetekben hasznos, amelyekben az entitások dinamikusan jönnek létre, és a használat után nem törlődnek, mert a teszt vagy a hibakeresés futtatása megszakad. Akkor is hasznos, ha egy alkalmazás dinamikus entitásokat (például egy válasz-várólistát) hoz létre a válaszok fogadásához egy webkiszolgálói folyamatba, vagy egy másik viszonylag rövid életű objektumba, ahol nehéz megbízhatóan megtisztítani ezeket az entitásokat, amikor az objektum példánya eltűnik.

A szolgáltatás engedélyezve van a [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonság használatával. Ez a tulajdonság arra az időtartamra van beállítva, ameddig az entitásnak tétlennek kell lennie (használaton kívül), mielőtt automatikusan törölné. A tulajdonság minimális értéke 5.
 
A **autoDeleteOnIdle** tulajdonságot egy Azure Resource Manager művelettel vagy a .NET-keretrendszer ügyféloldali [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-kon keresztül kell beállítani. Nem állítható be a portálon.

## <a name="idleness"></a>Semmittevés

Az entitások (várólisták, témakörök és előfizetések) tétlensége az alábbiak szerint történik:

- Üzenetsorok
    - Nincs küldés  
    - Nincs fogadás  
    - Nincsenek frissítések a várólistához  
    - Nincsenek ütemezett üzenetek  
    - Nincs Tallózás/betekintés 
- Témakörök  
    - Nincs küldés  
    - Nincsenek frissítések a témakörhöz  
    - Nincsenek ütemezett üzenetek 
- Előfizetések
    - Nincs fogadás  
    - Nincs frissítés az előfizetéshez  
    - Nincs új szabály hozzáadva az előfizetéshez  
    - Nincs Tallózás/betekintés  
 


## <a name="next-steps"></a>További lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)