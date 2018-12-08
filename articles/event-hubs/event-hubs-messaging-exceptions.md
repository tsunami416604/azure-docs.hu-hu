---
title: Üzenetkezelési kivételek – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk az Azure Event Hubs üzenetküldési kivételei és a javasolt műveletek listáját tartalmazza.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 013386d86d29b75591a938b2805bde25ecf176d3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104468"
---
# <a name="event-hubs-messaging-exceptions"></a>Az Event Hubs üzenetküldési kivételei

Ez a cikk az Azure Service Bus üzenetkezelési API szalagtárban, tartalmazza a .NET Framework Event Hubs API-k által előállított kivételeket mutatja be. Ez a hivatkozás változhatnak, ezért vissza frissítések keresése.

## <a name="exception-categories"></a>Kivétel kategóriák

Az Event Hubs API-k készítése a kivételeket, amelyek együtt őket megoldása érdekében a kapcsolódó művelet a következő kategóriákba esnek is.

1. Kódolási hiba felhasználói: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Általános művelet: megpróbálja kijavítani a kódot, a folytatás előtt.
2. A telepítő vagy konfigurációs hiba: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: tekintse át a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Általános művelet: értesítse a felhasználókat, vagy próbálja megismételni a műveletet.
4. Kivételek: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Általános művelet: kivétel típusának; Tekintse meg a tábla a következő szakaszban. 

## <a name="exception-types"></a>Kivételtípusok
A következő táblázat felsorolja az üzenetkezelési kivételtípusok, és azok okok, és a megjegyzések javasolt művelet is igénybe vehet.

| Kivétel típusa | Leírás/OK és példák | Javasolt művelet | Vegye figyelembe az automatikus azonnali újrapróbálkozás |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kért művelet, amely szabályozza a meghatározott időn belül nem válaszol a kiszolgáló [így időtúllépés történt](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Előfordulhat, hogy a kiszolgáló befejezte a kért művelet. Ez a kivétel hálózati vagy más infrastruktúra késedelem miatt fordulhat elő. |Ellenőrizze a rendszer állapotát, a konzisztencia, és szükség esetén próbálja megismételni.<br /> Lásd: [TimeoutException](#timeoutexception). | Bizonyos esetekben; segíthet újrapróbálkozási újrapróbálkozási logika hozzáadása a kódot. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgáló vagy a szolgáltatás belül. Tekintse meg a részleteket a kivétel üzenetét. Például [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) ehhez a kivételhez állít elő, ha az üzenet fogadása megtörtént a [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód. | Ellenőrizze a kód és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvénytelen. | Újrapróbálkozás nem segít. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Kísérlet történik egy olyan objektum, amely már zárt, megszakadt, vagy eldobva művelet meghívásához. Bizonyos ritkán előforduló esetekben a környezeti tranzakció már eldobva. | Ellenőrizze a kódot, és ellenőrizze, hogy nem lép működésbe műveletek nA smazaném objektu. | Újrapróbálkozás nem segít. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektumot nem sikerült megszerezni a jogkivonatot, a jogkivonat érvénytelen vagy a jogkivonat nem tartalmazza a jogcímeket, a művelet végrehajtásához szükséges. | Győződjön meg arról, hogy a jogkivonat-szolgáltató jön létre a megfelelő értékekkel. Ellenőrizze a konfigurációt, az Access Control Service szolgáltatást. | Bizonyos esetekben; segíthet újrapróbálkozási újrapróbálkozási logika hozzáadása a kódot. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[: ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | A metódushoz megadott egy vagy több argumentumok érvénytelenek. Az URI-t megadott [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozás](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) útvonalszegmensben elérési útját tartalmazza. A megadott URI-séma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozás](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) je neplatná. A tulajdonság értéke 32 KB-nál nagyobb. | Ellenőrizze a hívó kód, és ellenőrizze, hogy az argumentumok megfelelőek. | Újrapróbálkozás nem segít. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A művelethez társított entitás nem létezik, vagy törölve lett. | Győződjön meg arról, hogy az entitás létezik. | Újrapróbálkozás nem segít. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Ügyfél nem sikerül egy Eseményközpont kapcsolatot létesíteni. |Ellenőrizze, hogy a megadott állomásnév helyességéről, valamint a gazdagép érhető el. | Újrapróbálkozási segíthetnek a nem állandó hálózati kapcsolat merül fel. |
| [Microsoft.ServiceBus.Messaging ServerBusyException ](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Szolgáltatás nem tudja feldolgozni a kérelmet jelenleg. | Ügyfél Várjon egy ideig, majd próbálja megismételni a műveletet. <br /> Lásd: [ServerBusyException](#serverbusyexception). | Ügyfél bizonyos idő után újrapróbálkozik. Ha egy újra egy másik kivételt eredményez, ellenőrizze az újrapróbálkozási viselkedését, hogy a kivétel. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Általános üzenetkezelés, amely a következő esetekben előfordulhat, hogy hibajelzést kivétel: létrehozására tett kísérlet egy [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) neve vagy elérési útját, amelyhez tartozik egy másik entitás típusa (például egy témakör) segítségével. Egy kísérlet 256 KB-nál nagyobb méretű üzenet küldése. A kiszolgáló vagy a szolgáltatás hibába ütközött a kérés feldolgozása közben. Tekintse meg a részleteket a kivétel üzenetét. Ez a kivétel általában átmeneti kivételként. | Ellenőrizze a kódot elemre, és győződjön meg arról, hogy csak a szerializálható objektumok használhatók az üzenettörzs (vagy egy egyéni szerializáló használata). Ellenőrizze a dokumentációban a Tulajdonságok támogatott érték típusú, és csak a támogatott használati típusok. Ellenőrizze a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) tulajdonság. Ha **igaz**, megismételheti a műveletet. | Újrapróbálkozási viselkedés nincs meghatározva, és nem segíthetnek. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Próbálja meg egy entitás létrehozása egy nevet, amely a szolgáltatás névtéren egy másik entitás már használatban van. | Törölje a létező entitásba, vagy válasszon másik nevet az entitás létrehozását. | Újrapróbálkozás nem segít. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Az üzenetkezelési entitás elérte a maximális megengedett méretét. Ehhez a kivételhez akkor fordulhat elő, ha a fogadók maximális számát (ez 5) már meg van nyitva egy fogyasztó-csoport szintjén. | Üzenetek fogadása az entitást vagy a alvárólista létrehozásához az entitásban lévő terület. <br /> Lásd: [QuotaExceededException](#quotaexceededexception) | Újrapróbálkozási segíthet, ha üzenetek időközben eltávolították. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Kérés letiltva entitás futásidejű művelet. |Az entitás aktiválása. | Újrapróbálkozási segíthetnek, ha az entitás addig aktiválva lett. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Üzenet hasznos adattartalmából meghaladja a 256 KB-os korlát. A 256 KB-os korlát van a teljes üzenet, amely Rendszertulajdonságok és minden olyan .NET-terhelést is tartalmazhat. | Az üzenet hasznos adattartalmából méretének csökkentése, majd próbálja megismételni a műveletet. |Újrapróbálkozás nem segít. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitáshoz tartozó kvóta túl lett lépve.

Ehhez a kivételhez akkor fordulhat elő, ha a fogadók (5) maximális száma már meg van nyitva egy fogyasztó-csoport szintjén.

### <a name="event-hubs"></a>Event Hubs
Az Event Hubs az Eseményközpont száma 20 felhasználói csoportot esetében. Ha tárolórétegeket próbál létrehozni további, kap egy [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy egy felhasználó által kezdeményezett művelet a vártnál tovább tart, mint a művelet időkorlátja. 

Az Event Hubs, az időkorlát van megadva, vagy részeként a kapcsolati karakterláncot, vagy keresztül [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A hibaüzenet maga változhat, de az aktuális művelet számára megadott időtúllépési érték mindig tartalmaz. 

### <a name="common-causes"></a>Gyakori okai
Ez a hiba két gyakori oka: nem megfelelő konfiguráció vagy egy átmeneti szolgáltatáshiba.

1. **Helytelen konfiguráció** lehet, hogy a művelet időkorlátja túl kicsi a műveleti feltétel. A művelet időkorlátja, az ügyfél-SDK az alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy rendelkezik-e a kód értékét állítsa túl kicsi. Vegye figyelembe, hogy a feltétel, a hálózat és a CPU-használat hatással lehet a szükséges idő egy adott művelet elvégzéséhez, ezért a művelet időkorlátja nem állítható egy kisebb értékre.
2. **Átmeneti szolgáltatáshiba** néha az Event Hubs szolgáltatás az késleltetések kérelmek feldolgozása; például a nagy forgalom időszakokban tapasztalhatnak. Ezekben az esetekben, is újra a művelettel némi késéssel, amíg a művelet sikeres. Ha ugyanazt a műveletet több kísérlet után is sikertelen, látogasson el a [Azure-szolgáltatások](https://azure.microsoft.com/status/) , van-e ismert szolgáltatáskimaradások.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) vagy [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) azt jelzi, hogy a kiszolgáló túl van terhelve. Nincsenek ehhez a kivételhez két kapcsolódó hibakódok.

### <a name="error-code-50002"></a>50002-es hibakód:

Ez a hiba akkor fordulhat elő, egy vagy két oka:

1. A terhelés nem egyenlően oszlik el az eseményközpont összes partíciójára, és a egy partíciót eléri a helyi átviteli egység korlátozás.
    
    Megoldás: A partíció terjesztési stratégia indítják, vagy próbálja meg elérni [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) segíthetnek.

2. Az Event Hubs-névtér nem rendelkezik elegendő átviteli egységek (ellenőrizheti a **metrikák** képernyőn az eseménynaplóban a Hubs-névtér ablakában a [az Azure portal](https://portal.azure.com) megerősítéséhez). A portál összesített (1 perces) információkat jelenít meg, de a valós idejű – az átviteli sebességet, hogy csak egy becsült legyen mérjük.

    Megoldás: Növelje a névtéren az átviteli egységek segítségével. A teheti ezt a műveletet a portálon a **méretezési** ablak az Event Hubs-névtér képernyőn. Vagy használhat [automatikus feltöltésről](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>50001-es hibakód:

Ez a hiba a kell ritkán fordul elő. Ez történik, ha a tároló futtatását névteréhez kód processzor alacsony – az Event Hubs terheléselosztó nem több, mint néhány másodpercet kezdődik.


## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
