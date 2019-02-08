---
title: Az Azure Service Bus üzenetkezelés kivételei |} A Microsoft Docs
description: A Service Bus üzenetkezelési kivételek és javasolt műveletek listája.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: b90e87310bf6dec505176b7f4d4cb9e15ac57c20
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891810"
---
# <a name="service-bus-messaging-exceptions"></a>A Service Bus-alapú üzenetkezelés kivételei
Ez a cikk néhány a Microsoft Azure Service Bus-üzenetkezelés API-k által előállított kivételeket sorolja fel. Ez a hivatkozás változhatnak, ezért vissza frissítések keresése.

## <a name="exception-categories"></a>Kivétel kategóriák
Az üzenetkezelési API-k készítése a kivételeket, amelyek együtt őket megoldása érdekében a kapcsolódó művelet a következő kategóriákba esnek is. A jelentése és okait és a egy kivétel üzenetküldési entitás típusától függően eltérőek lehetnek:

1. User coding error ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Általános művelet: megpróbálja kijavítani a kódot, a folytatás előtt.
2. A telepítő vagy konfigurációs hibát ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: tekintse át a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Általános művelet: értesítse a felhasználókat, vagy próbálja megismételni a műveletet. Vegye figyelembe, hogy a `RetryPolicy` az ügyfél-SDK osztályában beállítható úgy, hogy automatikusan kezelheti az újrapróbálkozásokat. Lásd: [újrapróbálkozási útmutatás](/azure/architecture/best-practices/retry-service-specific#service-bus) további információt.
4. Kivételek ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Általános művelet: kivétel típusának; a tábla a következő szakaszban talál: 

## <a name="exception-types"></a>Kivételtípusok
A következő táblázat felsorolja az üzenetkezelési kivételtípusok, és azok okok, és a megjegyzések javasolt művelet is igénybe vehet.

| **Kivétel típusa** | **Leírás/OK és példák** | **Javasolt művelet** | **Vegye figyelembe az automatikus azonnali újrapróbálkozás** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kért művelet, amely szabályozza a meghatározott időn belül nem válaszol a kiszolgáló [így időtúllépés történt](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Előfordulhat, hogy a kiszolgáló befejezte a kért művelet. Ez akkor fordulhat elő, hálózati vagy más infrastruktúra késedelem miatt. |Ellenőrizze a rendszer állapotát, a konzisztencia, és szükség esetén próbálja megismételni. Lásd: [időtúllépési kivételeket](#timeoutexception). |Bizonyos esetekben; segíthet újrapróbálkozási újrapróbálkozási logika hozzáadása a kódot. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgáló vagy a szolgáltatás belül. Tekintse meg a részleteket a kivétel üzenetét. Ha például [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ehhez a kivételhez állít elő, ha az üzenet fogadása megtörtént a [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) mód. |Ellenőrizze a kód és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvénytelen. |Újrapróbálkozás nem működik. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Kísérlet történik egy olyan objektum, amely már zárt, megszakadt, vagy eldobva művelet meghívásához. Bizonyos ritkán előforduló esetekben a környezeti tranzakció már eldobva. |Ellenőrizze a kódot, és ellenőrizze, hogy nem lép működésbe műveletek nA smazaném objektu. |Újrapróbálkozás nem működik. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektumot nem sikerült megszerezni a jogkivonatot, a jogkivonat érvénytelen vagy a jogkivonat nem tartalmazza a jogcímeket, a művelet végrehajtásához szükséges. |Győződjön meg arról, hogy a jogkivonat-szolgáltató jön létre a megfelelő értékekkel. Ellenőrizze a konfigurációt, az Access Control Service szolgáltatást. |Bizonyos esetekben; segíthet újrapróbálkozási újrapróbálkozási logika hozzáadása a kódot. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[: ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |A metódushoz megadott egy vagy több argumentumok érvénytelenek.<br /> Az URI-t megadott [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozás](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) útvonalszegmensben elérési útját tartalmazza.<br /> A megadott URI-séma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozás](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatná. <br />A tulajdonság értéke 32 KB-nál nagyobb. |Ellenőrizze a hívó kód, és ellenőrizze, hogy az argumentumok megfelelőek. |Újrapróbálkozás nem működik. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A művelethez társított entitás nem létezik, vagy törölve lett. |Győződjön meg arról, hogy az entitás létezik. |Újrapróbálkozás nem működik. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Kísérlet történt az üzenet egy adott sorszáma. Ez az üzenet nem található. |Győződjön meg arról, hogy az üzenet már nem érkezett. Ellenőrizze, hogy az üzenet rendelkezik e deadlettered a kézbesítetlen levelek várólistájára. |Újrapróbálkozás nem működik. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Az ügyfél nem tud csatlakozni a Service Bus áll. |Ellenőrizze, hogy a megadott állomásnév helyességéről, valamint a gazdagép érhető el. |Újrapróbálkozási segíthetnek a nem állandó hálózati kapcsolat merül fel. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Szolgáltatás nem tudja feldolgozni a kérelmet jelenleg. |Ügyfél Várjon egy ideig, majd próbálja megismételni a műveletet. |Ügyfél bizonyos idő után újrapróbálkozik. Ha egy újra egy másik kivételt eredményez, ellenőrizze az újrapróbálkozási viselkedését, hogy a kivétel. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Az üzenethez társított zárolási jogkivonat érvényessége lejárt, vagy a zárolási jogkivonat nem található. |Az üzenet eldobásakor. |Újrapróbálkozás nem működik. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Ehhez a munkamenethez társított zárolási elvész. |Megszakítja a [lehívása](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektum. |Újrapróbálkozás nem működik. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Általános üzenetkezelés, amely a következő esetekben előfordulhat, hogy hibajelzést kivétel:<br /> Létrehozására tett kísérlet egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) neve vagy elérési útját, amelyhez tartozik egy másik entitás típusa (például egy témakör) segítségével.<br />  Egy kísérlet 256 KB-nál nagyobb méretű üzenet küldése. A kiszolgáló vagy a szolgáltatás hibába ütközött a kérés feldolgozása közben. Tekintse meg a részleteket a kivétel üzenetét. Ez általában átmeneti kivételként a. |Ellenőrizze a kódot elemre, és győződjön meg arról, hogy csak a szerializálható objektumok használhatók az üzenettörzs (vagy egy egyéni szerializáló használata). Ellenőrizze a dokumentációban a Tulajdonságok támogatott érték típusú, és csak a támogatott használati típusok. Ellenőrizze a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonság. Ha **igaz**, megismételheti a műveletet. |Újrapróbálkozási viselkedés nincs meghatározva, és nem segíthetnek. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Próbálja meg egy entitás létrehozása egy nevet, amely a szolgáltatás névtéren egy másik entitás már használatban van. |Törölje a létező entitásba, vagy válasszon másik nevet az entitás létrehozását. |Újrapróbálkozás nem működik. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Az üzenetkezelési entitás elérte a maximális megengedhető méretet, vagy a névtérhez kapcsolatok maximális száma túl lett lépve. |Üzenetek fogadása az entitást vagy a alvárólista létrehozásához az entitásban lévő terület. Lásd: [QuotaExceededException](#quotaexceededexception). |Újrapróbálkozási segíthet, ha üzenetek időközben eltávolították. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |A Service Bus ezt a kivételt ad vissza, ha megpróbál létrehozni egy érvénytelen szabályműveletet. A Service Bus a kivétel üzenet a szabály hatására végrehajtott művelet feldolgozásakor hiba esetén egy deadlettered üzenethez csatolja. |Ellenőrizze a szabály hatására végrehajtott művelet helyességét. |Újrapróbálkozás nem működik. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |A Service Bus ezt a kivételt ad vissza, ha megpróbál létrehozni egy érvénytelen szűrő. A Service Bus ehhez a kivételhez, ha a szűrőt, hogy az üzenet feldolgozása közben hiba történt egy deadlettered üzenethez csatolja. |Ellenőrizze a helyességét a szűrőt. |Újrapróbálkozás nem működik. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |A fogadja el a munkamenetet egy adott munkamenet-Azonosítót azonban a munkamenet egy másik ügyfél által jelenleg zárolva van. |Ellenőrizze, hogy a munkamenet zárolásának más ügyfelek által. |Újrapróbálkozási segíthet, ha a munkamenet kiadása addig. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Túl sok művelet a tranzakció részét képezik. |A tranzakció részét képező operations számának csökkentése. |Újrapróbálkozás nem működik. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Kérés letiltva entitás futásidejű művelet. |Az entitás aktiválása. |Újrapróbálkozási segíthetnek, ha az entitás addig aktiválva lett. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |A Service Bus ezt a kivételt ad vissza, egy üzenetet küld egy témakört, amely előre szűrés engedélyezve van, és a szűrők egyike sem felel meg. |Ellenőrizze, hogy legalább egy szűrő megfelel. |Újrapróbálkozás nem működik. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Üzenet hasznos adattartalmából meghaladja a 256 KB-os korlátját. A 256 KB-os korlátot a teljes üzenetmérete, amelyek magukban foglalhatják Rendszertulajdonságok és minden olyan .NET-terhelés. |Az üzenet hasznos adattartalmából méretének csökkentése, majd próbálja megismételni a műveletet. |Újrapróbálkozás nem működik. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A környezeti tranzakció (*Transaction.Current*) érvénytelen. Előfordulhat, hogy befejeződött vagy megszakítva. További információt tartalmaznak a belső kivételben találhatók. | |Újrapróbálkozás nem működik. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Egy megpróbálta tranzakciót, amelynek bizonytalan, vagy kísérlet történik a tranzakció véglegesítése és bizonytalan válik a tranzakciót. |Az alkalmazásnak kell kezelnie (mint egy különleges esetben), a kivétel, mert a tranzakció előfordulhat, hogy már véglegesített. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelzi, hogy egy adott entitáshoz tartozó kvóta túl lett lépve.

### <a name="queues-and-topics"></a>Üzenetsorok és témakörök
Az üzenetsoroktól és témaköröktől esetén ez gyakran a várólista méretét. A hiba üzenettulajdonság tartalmaz további részleteket az alábbi példában látható módon:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Az üzenet megállapítja, hogy a témakör túllépte a maximális méretét, ez megkülönbözteti a kis (az alapértelmezett méretkorlátját) 1 GB-ban. 

### <a name="namespaces"></a>Névterek

A névterek [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelzi, hogy egy alkalmazás meghaladta az egy névtérhez kapcsolatok maximális számát. Példa:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Gyakori okai
Ez a hiba két gyakori oka: a kézbesítetlen levelek várólistájára vonatkozik, és a nem működő üzenet fogadók.

1. **[Kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md)**  olvasó sikertelen üzenetek befejezéséhez és az üzeneteket az üzenetsor vagy témakör minősül információnak, ha a zárolás lejárta. Ez akkor fordulhat elő, ha az olvasó, ami megakadályozza az hívása kivétel lép fel [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Miután egy üzenetet elolvasták-10-szer, kerül át a kézbesítetlen levelek várólistájára alapértelmezés szerint. Ez a viselkedés vezérli a [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) tulajdonság és a egy alapértelmezett értéke 10. Üzenetet a kézbesíthetetlen levelek sorában lévő egymásra, mert helyet foglalnak azokat.
   
    A probléma megoldásához, olvassa el, és végezze el az üzeneteket, a kézbesítetlen levelek várólistájára vonatkozik, mint bármely más üzenetsorból. Használhatja a [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) formázhatja a kézbesítetlen levelek várólistájára elérési út segítségével módszert.
2. **Leállítva fogadó**. A fogadó leállt üzenetek fogadása egy üzenetsorból vagy előfizetést. Ezzel azonosíthatja módja, és tekintse meg a [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) tulajdonság, amely az üzenetek a teljes bontását mutatja. Ha a [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) tulajdonság magas vagy növekvő, akkor az üzenetek nem beolvasott gyors rendszer ír.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy egy felhasználó által kezdeményezett művelet a vártnál tovább tart, mint a művelet időkorlátja. 

Nézze meg az értékét a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság, ezt a korlátot lenyomásával is okozhatja egy [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Üzenetsorok és témakörök
Az üzenetsoroktól és témaköröktől az időkorlát van megadva vagy a [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) tulajdonság részeként a kapcsolati karakterláncot, vagy keresztül [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A hibaüzenet maga változhat, de az aktuális művelet számára megadott időtúllépési érték mindig tartalmaz. 



## <a name="next-steps"></a>További lépések

A Service Bus .NET API teljes referenciaként tekintse meg a [Azure .NET API-referencia](/dotnet/api/overview/azure/service-bus).

Tudjon meg többet a [a Service Bus](https://azure.microsoft.com/services/service-bus/), tekintse meg a következő cikkeket:

* [A Service Bus üzenetkezelésének áttekintése](service-bus-messaging-overview.md)
* [Service Bus-architektúra](service-bus-architecture.md)

