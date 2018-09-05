---
title: Az Azure Service Bus-üzenetek lejáratkor |} A Microsoft Docs
description: Lejárat és az Azure Service Bus-üzenetek élettartama
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
ms.author: spelluru
ms.openlocfilehash: e8e5cfe4774334b470de2fbba00760126db71ddc
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696853"
---
# <a name="message-expiration-time-to-live"></a>Üzenetek lejárata (élettartama)

A belül egy üzenetet, vagy parancsot vagy lekérdezést, amely egy üzenetet, a fogadó ruház hasznos szinte mindig alkalmazásszintű lejárati határidő valamilyen vonatkoznak. A határidő után a tartalom már nem érkeznek, vagy már nem hajtja végre a kért művelet.

Fejlesztési és tesztelési környezetet, amelyben üzenetsorok és témakörök gyakran használják az alkalmazások és alkalmazás részei részleges futtatások környezetében is kívánatos automatikusan be lehet, hogy a következő vizsgálat futtatása szemétgyűjtő is kimaradt teszt üzenetek Indítsa el a tiszta.

Bármilyen egyéni üzenet a lejárati beállításával szabályozhatja a [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) rendszer tulajdonságot használja, amely meghatározza egy relatív időtartamát. A lejárati abszolút azonnali lesz, ha az üzenet sorba az entitásba. Ugyanakkor a [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) tulajdonság értékét veszi [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive).

Elmúlt a **ExpiresAtUtc** azonnali, üzenetek válnak lekérés nem lehet áttelepíteni. A lejárati nem befolyásolja az üzeneteket, amelyek jelenleg zárolva van, a tartalomkézbesítési; Ezeket az üzeneteket továbbra is megfelelően kezeli. Ha a zárolás lejárta vagy az üzenet van hagyva, a lejárati azonnal érvénybe lép.

Az üzenet zárolási alatt van, amíg az alkalmazás egy üzenet, amely lejárt birtokában lehet. Hogy az alkalmazás arra, hogy lépjen tovább, és a feldolgozás, vagy úgy dönt, hogy az üzenet zárolásának feloldása a végrehajtója esetén.

## <a name="entity-level-expiration"></a>Entitásszintű lejárata

Egy üzenetsorba vagy témakörbe küldött összes üzenet vonatkoznak rá egy alapértelmezett elévülési, amely az entitás nem állítják a szint a [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonságot, és amely is a portálon beállított létrehozásakor és később módosítani. Az alapértelmezett lejárati szolgál az entitás küldött összes üzenet ahol [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) nem állított be. Az alapértelmezett lejárati olyan funkciókkal, mint a felső határt a **TimeToLive** értéket. Üzeneteket, amelyek rendelkeznek egy hosszabb **TimeToLive** lejárati, mint az alapértelmezett érték csendes módban vannak igazítva a **defaultMessageTimeToLive** mielőtt a várólistán lévő érték.

Lejárt üzenetek igény szerint helyezhetők át egy [kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md) beállításával a [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) tulajdonság, vagy a portál a megfelelő négyzetet. Ha a beállítás le van tiltva, a rendszer elveti lejárt üzenetek. Lejárt üzenetek áthelyezése a kézbesíthetetlen levelek várólistájára lehet különböztetni a többi kézbesítetlen lettered üzenet kiértékelése a [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) , amely a közvetítő tárolja a felhasználói tulajdonságok szakaszának; tulajdonság értéke [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) ebben az esetben.

A fent említett helyzet, amelyben az üzenet védett a lejárati közben a zárolást, és ha a jelző be van állítva az entitást, az üzenet átkerül a kézbesítetlen levelek várólistájára vonatkozik, a zárolás van hagyva, vagy lejár. Azonban nem áthelyezés Ha az üzenet sikeresen rendezik, amely azt feltételezi, hogy az alkalmazás már sikeresen kezelte, lejárta után névleges költséget számítunk értéket.

Kombinációja [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) , és automatikus (és a tranzakciós) kézbesítetlen levelek kezelése lejártakor értékes eszköze a bizalom e egy feladatot, a kezelő vagy a határidőt a kezelők csoportja a rendszer lekéri a létrehozó feldolgozását a határidő elérésekor.

Vegyük példaként egy webhely, amely megbízhatóan futtathatnak feladatokat a méretezési csoport által korlátozott háttérkiszolgálón kell, és alkalmanként élményt adatforgalom hirtelen megugró kihasználtság, vagy el akar elhelyezni ellen rendelkezésre állási epizódját adott háttérrendszer, amely. A normál esetben a kiszolgálóoldali kezelő az elküldött felhasználói adatok leküldi az adatokat egy üzenetsorba, és ezt követően a sikeres kezelésére egy válasz üzenetsorba a tranzakció megerősítése válasz érkezik. Ha forgalmat ugrásszerű, és a háttér-kezelő nem tudja feldolgozni a várakozó elemek időben, a lejárt feladatok vannak vissza a kézbesítetlen levelek várólistájára vonatkozik. Az interaktív felhasználó értesítést kaphat, hogy a kért műveletet a szokásosnál kicsit tovább tart, és a kérés majd állítható. Ebben az egy feldolgozási elérési útja a másik üzenetsorba, a végső feldolgozási eredménye a felhasználó által küldött e-mailt. 

## <a name="temporary-entities"></a>Ideiglenes entitások

Service Bus-üzenetsorok, témakörök és előfizetések entitásokként ideiglenes, amely lesznek automatikusan eltávolítva, ha azok nem használták a megadott időtartam elteltéig hozható létre.
 
Az automatikus tisztítás hasznos fejlesztési és tesztelési forgatókönyvek, amelyben entitások dinamikusan létrehozott, és nem használja, a teszt vagy a hibakeresési futtatása néhány megszakítás miatt után törlődnek. Ez akkor hasznos, ha egy alkalmazást hoz létre dinamikus entitások, például egy válasz várólista, a válaszfogadás vissza egy webkiszolgáló-folyamat, vagy egy másik viszonylag rövid életű objektum nehéz ezeket az entitásokat is megbízhatóan karbantartása során az objektum példány eltűnik.

A szolgáltatás engedélyezve van, használja a [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) tulajdonság, amely az időtartam, amelyre egy entitás kell értékre van állítva üresjárati (nem használt), mielőtt a rendszer automatikusan törli azt. A minimális érték 5 perc.
 
A **autoDeleteOnIdle** tulajdonságot kell beállítani egy Azure Resource Manager-műveletet vagy a .NET-keretrendszer ügyfél keresztül [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API-k. A portálon keresztül nem állítható be.


## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)