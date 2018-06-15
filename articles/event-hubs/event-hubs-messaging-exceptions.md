---
title: Az Azure Event Hubs üzenetküldési kivételek |} Microsoft Docs
description: Azure Event Hubs üzenetküldési kivételeket és a javasolt műveletek listáját.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 964475ba8b42ac41707fa78468bfe551677c595f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783128"
---
# <a name="event-hubs-messaging-exceptions"></a>Az Event Hubs üzenetküldési kivételei

Ez a cikk egy Azure Service Bus üzenetküldési API könyvtár által létrehozott kivételek, többek között Event Hubs API-k sorolja fel. Ez a hivatkozás változhat, így biztonsági frissítések keresése.

## <a name="exception-categories"></a>Kivétel kategóriák

Az Event Hubs API-kat hoz létre a kivételeket, amelyek a következő kategóriákba, amelyekkel javító őket a társított művelet együtt tartalék is.

1. Kódolási hiba felhasználói: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Általános művelet: javító a kódot, a folytatás előtt.
2. A telepítő-konfigurációs hiba: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: Ellenőrizze a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Általános művelet: próbálja megismételni a műveletet, vagy értesítse a felhasználókat.
4. Más kivételek: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Általános művelet: kivétel típusának; Tekintse meg a tábla a következő szakaszban. 

## <a name="exception-types"></a>Kivétel típusa
A következő táblázat felsorolja az üzenetkezelési kivétel típusait, és az okait és a megjegyzések javasolt művelet is igénybe vehet.

| **Kivétel típusa** | **Leírás/OK/példák** | **Javasolt művelet** | **Jegyezze fel az automatikus azonnali újrapróbálkozási** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kért művelet, amely vezérli a meghatározott időn belül nem válaszol a kiszolgáló [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). A kiszolgáló a kért művelet befejeződött. Ez akkor fordulhat elő, hálózati vagy más infrastruktúra késleltetése miatt. |Ellenőrizze a rendszer, a konzisztencia, és szükség esetén próbálja megismételni.<br /> Lásd: [TimeoutException](#timeoutexception). |Újrapróbálkozási segíthet bizonyos esetekben; újrapróbálkozási logika hozzáadása a kódot. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgáló vagy a szolgáltatás belül. A kivétel üzenetét, a részletekért tekintse meg. Például [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) ezt a kivételt állít elő, ha az üzenet érkezett a [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód. |Ellenőrizze a kódot, és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvénytelen. |Nem nyújt az újra gombra. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Egy olyan objektum, amely már lezárták, megszakítva vagy használaton művelet meghívásához tett kísérlet. Ritka esetekben a környezeti tranzakció már eldobták. |Ellenőrizze a kódot, és győződjön meg arról, hogy nem lép működésbe műveleteket végez el egy teljesített objektum. |Nem nyújt az újra gombra. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektumot nem sikerült megszerezni a jogkivonatot, a token érvénytelen, vagy a jogkivonat nem tartalmazza a jogcímeket, a művelet végrehajtásához szükséges. |Győződjön meg arról, hogy a jogkivonat-szolgáltató jön létre a megfelelő értékekkel. A hozzáférés-vezérlés szolgáltatás konfigurációjának ellenőrzése. |Újrapróbálkozási segíthet bizonyos esetekben; újrapróbálkozási logika hozzáadása a kódot. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |A metódus számára megadott egy vagy több argumentumok érvénytelenek. Az URI számára megadott [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozása](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) elérési szegmenssel tartalmazza. A megadott URI-séma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozása](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) érvénytelen. A tulajdonság értéke nagyobb, mint 32 KB lehet. |Ellenőrizze a hívó kódot, és győződjön meg arról, hogy az argumentumok megfelelőek. |Nem nyújt az újra gombra. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |A művelethez társított entitás nem létezik, vagy már törölték. |Győződjön meg arról, hogy az entitás létezik. |Nem nyújt az újra gombra. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Az ügyfél nem képes kapcsolatot létrehozni az Event Hubs áll. |Ellenőrizze, hogy a megadott állomásnév helyes-e, és érhető el a gazdagép. |Újrapróbálkozási segíthet, ha időszakos csatlakozási hibák. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Szolgáltatás állapota nem tudja feldolgozni a kérést most. |Ügyfél Várjon egy ideig, majd próbálja megismételni a műveletet. <br /> Lásd: [ServerBusyException](#serverbusyexception). |Ügyfél bizonyos időköz után újrapróbálkozik. Ha ismételt próbálkozással egy másik kivétel eredményez, ellenőrizze az újrapróbálási viselkedése, hogy a kivétel. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Általános kivétel, amely a következő esetekben előfordulhat, hogy jelezni üzenetküldési: létrehozására tett kísérlet egy [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) neve vagy elérési útja, amelyhez tartozik egy másik entitás típusa (például egy témakör) segítségével. Üzenet küldése 256KB-nál nagyobb tett kísérlet. A kiszolgáló vagy a szolgáltatás hibát észlelt a kérelem feldolgozása során. Ellenőrizze a részleteket a kivétel üzenetét. Ez általában az átmeneti kivétel. |Ellenőrizze a kódot elemre, és győződjön meg arról, hogy csak a szerializálható objektumok használja az üzenettörzs (vagy egy egyéni szerializáló használja). Olvassa el a következő érték támogatott a tulajdonságok és csak használata támogatott típusainak dokumentációját. Ellenőrizze a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) tulajdonság. Ha **igaz**, is próbálja megismételni a műveletet. |Újrapróbálási viselkedése nincs definiálva, és nem segítséget. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Megpróbál létrehozni egy entitás neve már használatban van egy másik entitás az adott szolgáltatás névtere. |Törölje a meglévő entitás, vagy válasszon másik nevet a létrehozandó az entitás. |Nem nyújt az újra gombra. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Az üzenetküldési entitás elérte a megengedett maximális méretet. Ez akkor fordulhat elő, ha a fogadók maximális számát (amely 5) már meg van nyitva a fogyasztó-csoport szinten. |Üzenetek fogadása az entitás vagy az alárendelt várólisták létrehozásához az entitásban levő terület. <br /> Lásd: [QuotaExceededException](#quotaexceededexception) |Újrapróbálkozási segíthet, ha üzenetek időközben eltávolították. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Egy futtatási művelet a letiltott entitásra vonatkozó kérést. |Az entitás aktiválása. |Újrapróbálkozási segíthet, ha időközben aktív az entitás. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Üzenetadatokat meghaladja a 256 KB-os korlátozását. Vegye figyelembe, hogy a 256 KB-os korlát az teljes üzenet mérete, ami magában foglalhatja a Rendszertulajdonságok és bármely .NET terhelés mellett. |Csökkentse a üzenetadatokat méretét, majd próbálja megismételni a műveletet. |Nem nyújt az újra gombra. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitás kvóta túl lett lépve.

Ez akkor fordulhat elő, ha a fogadók (5) maximális száma már meg van nyitva a fogyasztó-csoport szinten.

### <a name="event-hubs"></a>Event Hubs
Az Event Hubs maximális hossza 20 felhasználói csoportot az Event Hubs egy rendelkezik. Hozzon létre több kísérli meg, amikor egy [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy egy felhasználó által kezdeményezett művelet a vártnál a művelet időkorlátja lejár. 

Az Event Hubs számára az időtúllépés van megadva, vagy részeként a kapcsolati karakterlánc, vagy keresztül [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A hibaüzenet a következő maga is eltérők lehetnek, de az az aktuális művelet a megadott időtúllépési érték mindig tartalmaz. 

### <a name="common-causes"></a>Általános okok
Két közös Ez a hiba oka: helytelen konfiguráció, vagy a átmeneti hibája.

1. **Helytelen konfiguráció** lehet, hogy a művelet időkorlátja túl kicsi a működési feltétel. A művelet időtúllépési értékét, az ügyfél SDK alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy vannak-e a kód az érték beállítása túl kicsi. Vegye figyelembe, hogy a feltétel a hálózat és a CPU-használat hatással lehet a szükséges idő egy adott művelet elvégzéséhez, így a művelet időtúllépési értékét nem állítható nagyon kis értékre.
2. **Szolgáltatások átmeneti hiba** néha az Event Hubs szolgáltatás fedezheti késlelteti a kérelmek feldolgozásának, például nagy forgalom idején. Ebben az esetben akkor is újra a művelettel késleltetés után, amíg a művelet sikeres nem lesz. Ha ugyanazt a műveletet több próbálkozást követően továbbra is sikertelen, látogasson el a [Azure szolgáltatás állapota hely](https://azure.microsoft.com/status/) , ha van-e bármely ismert szolgáltatás-kimaradások számát.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) vagy [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) azt jelzi, hogy a kiszolgáló túlterhelt. Ehhez a kivételhez két kapcsolódó hibakódok van.

### <a name="error-code-50002"></a>50002. Hibakód:

Ez a hiba akkor fordulhat elő, két okai:

1. A az eseményközpont összes partíciójára nem egyenletesen vannak elosztva a terhelés, és egy partíciót találatok a helyi átviteli egység korlátozás.
    
    Megoldás: A partíció terjesztési stratégia módosítása, vagy próbálja meg elérni [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) segítséget.

2. Az Event Hubs névtér nem rendelkezik elegendő átviteli egységek (ellenőrizheti a **metrikák** hubok névtér ablakában a képernyőn a [Azure-portálon](https://portal.azure.com) megerősítéséhez). Fontos megjegyezni, hogy a portál összesített (1 perc) kapcsolatos adatokat jeleníti meg, hogy csak egy becsült mérjük a valós idejű – átviteli sebesség.

    Megoldás: Az átviteli egységeket a névtér növelése segítségével. Ehhez a portálon, a a **méretezési** ablakot, az Event Hubs névtér képernyő.

### <a name="error-code-50001"></a>50001. Hibakód:

Ez a hiba a kell ritkán fordul elő. Akkor történik, ha a névtér kód futtatásával tárolója processzor alacsony – megkezdése előtt az Event Hubs terheléselosztó legfeljebb néhány másodpercen belül.


## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
