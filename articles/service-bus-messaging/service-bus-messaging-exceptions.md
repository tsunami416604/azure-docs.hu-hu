---
title: "Az Azure Service Bus üzenetkezelés kivételek |} Microsoft Docs"
description: "A Service Bus üzenetküldési kivételeket és a javasolt műveletek listáját."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: sethm
ms.openlocfilehash: f927aa7a33a650354abd090b6280795875ab693f
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="service-bus-messaging-exceptions"></a>A Service Bus-alapú üzenetkezelés kivételei
Ez a cikk a Microsoft Azure Service Bus üzenetküldési API-k által létrehozott kivételek sorolja fel. Ez a hivatkozás változhat, így biztonsági frissítések keresése.

## <a name="exception-categories"></a>Kivétel kategóriák
A üzenettovábbítási API-kat hoz létre a kivételeket, amelyek a következő kategóriákba, amelyekkel javító őket a társított művelet együtt tartalék is. Vegye figyelembe, hogy jelentését és okok kivétel üzenetküldési entitásra (várólisták/témakörök vagy az Event Hubs) típusától függően változhat:

1. Kódolási hiba felhasználói ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Általános művelet: javító a kódot, a folytatás előtt.
2. A telepítő-konfigurációs hiba ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: Ellenőrizze a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Általános művelet: próbálja megismételni a műveletet, vagy értesítse a felhasználókat.
4. Más kivételek ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Általános művelet: kivétel típusának; Tekintse meg a tábla a következő szakaszban. 

## <a name="exception-types"></a>Kivétel típusa
A következő táblázat felsorolja az üzenetkezelési kivétel típusait, és az okait és a megjegyzések javasolt művelet is igénybe vehet.

| **Kivétel típusa** | **Leírás/OK/példák** | **Javasolt művelet** | **Jegyezze fel az automatikus azonnali újrapróbálkozási** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kért művelet által ellenőrzött megadott időn belül nem válaszol a kiszolgáló [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). A kiszolgáló a kért művelet befejeződött. Ez akkor fordulhat elő, hálózati vagy más infrastruktúra késleltetése miatt. |Ellenőrizze a rendszer, a konzisztencia, és szükség esetén próbálja megismételni. Lásd: [időtúllépési kivétel](#timeoutexception). |Újrapróbálkozási segíthet bizonyos esetekben; újrapróbálkozási logika hozzáadása a kódot. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgáló vagy a szolgáltatás belül. A kivétel üzenetét, a részletekért tekintse meg. Például [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ezt a kivételt állít elő, ha az üzenet érkezett a [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) mód. |Ellenőrizze a kódot, és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvénytelen. |Nem nyújt az újra gombra. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Egy olyan objektum, amely már lezárták, megszakítva vagy használaton művelet meghívásához tett kísérlet. Ritka esetekben a környezeti tranzakció már eldobták. |Ellenőrizze a kódot, és győződjön meg arról, hogy nem lép működésbe műveleteket végez el egy teljesített objektum. |Nem nyújt az újra gombra. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.azure.servicebus.tokenprovider) objektumot nem sikerült megszerezni a jogkivonatot, a token érvénytelen, vagy a jogkivonat nem tartalmazza a jogcímeket, a művelet végrehajtásához szükséges. |Győződjön meg arról, hogy a jogkivonat-szolgáltató jön létre a megfelelő értékekkel. A hozzáférés-vezérlés szolgáltatás konfigurációjának ellenőrzése. |Újrapróbálkozási segíthet bizonyos esetekben; újrapróbálkozási logika hozzáadása a kódot. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |A metódus számára megadott egy vagy több argumentumok érvénytelenek.<br /> Az URI számára megadott [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozása](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) elérési szegmenssel tartalmazza.<br /> A megadott URI-séma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozása](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) érvénytelen. <br />A tulajdonság értéke nagyobb, mint 32 KB lehet. |Ellenőrizze a hívó kódot, és győződjön meg arról, hogy az argumentumok megfelelőek. |Nem nyújt az újra gombra. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A művelethez társított entitás nem létezik, vagy már törölték. |Győződjön meg arról, hogy az entitás létezik. |Nem nyújt az újra gombra. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Kísérlet történt az üzenet egy adott sorszáma. Ez az üzenet nem található. |Győződjön meg arról, hogy az üzenet már nem érkezett. Ellenőrizze a kézbesítetlen levelek várólistájának megtekintéséhez, hogy az üzenet nem deadlettered. |Nem nyújt az újra gombra. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Az ügyfél nem képes kapcsolatot létrehozni a Service Bus áll. |Ellenőrizze, hogy a megadott állomásnév helyes-e, és érhető el a gazdagép. |Újrapróbálkozási segíthet, ha időszakos csatlakozási hibák. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Szolgáltatás állapota nem tudja feldolgozni a kérést most. |Ügyfél Várjon egy ideig, majd próbálja megismételni a műveletet. |Ügyfél bizonyos időköz után újrapróbálkozik. Ha ismételt próbálkozással egy másik kivétel eredményez, ellenőrizze az újrapróbálási viselkedése, hogy a kivétel. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Az üzenethez társított zárolási-token érvényessége lejárt, vagy a zárolás jogkivonat nem található. |Az üzenet eldobásakor. |Nem nyújt az újra gombra. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Ehhez a munkamenethez társított zárolási elvész. |Megszakítja a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektum. |Nem nyújt az újra gombra. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Általános üzenetküldési, amely a következő esetekben előfordulhat, hogy jelezni kivétel:<br /> Létrehozására tett kísérlet egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) neve vagy elérési útja, amelyhez tartozik egy másik entitás típusa (például egy témakör) segítségével.<br />  Üzenet küldése 256KB-nál nagyobb tett kísérlet. A kiszolgáló vagy a szolgáltatás hibát észlelt a kérelem feldolgozása során. Ellenőrizze a részleteket a kivétel üzenetét. Ez általában az átmeneti kivétel. |Ellenőrizze a kódot elemre, és győződjön meg arról, hogy csak a szerializálható objektumok használja az üzenettörzs (vagy egy egyéni szerializáló használja). Olvassa el a következő érték támogatott a tulajdonságok és csak használata támogatott típusainak dokumentációját. Ellenőrizze a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) tulajdonság. Ha **igaz**, is próbálja megismételni a műveletet. |Újrapróbálási viselkedése nincs definiálva, és nem segítséget. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Megpróbál létrehozni egy entitás neve már használatban van egy másik entitás az adott szolgáltatás névtere. |Törölje a meglévő entitás, vagy válasszon másik nevet a létrehozandó az entitás. |Nem nyújt az újra gombra. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Az üzenetküldési entitás elérte a megengedett maximális méretet, vagy egy névtér kapcsolatok maximális száma túl lett lépve. |Üzenetek fogadása az entitás vagy az alárendelt várólisták létrehozásához az entitásban levő terület. Lásd: [QuotaExceededException](#quotaexceededexception). |Újrapróbálkozási segíthet, ha üzenetek időközben eltávolították. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |A Service Bus ezt a kivételt adja vissza, ha megkísérli a művelet érvénytelen szabály létrehozása. A Service Bus csatolja ezt a kivételt deadlettered üzenet hiba esetén a szabály műveletet az üzenet feldolgozása közben. |Ellenőrizze a szabályművelet helyességét. |Nem nyújt az újra gombra. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |A Service Bus ezt a kivételt adja vissza, ha megpróbálja létrehozhat egy szűrőt, amely érvénytelen. A Service Bus ezt a kivételt csatolja a deadlettered üzenet, ha a szűrőt az üzenet feldolgozása közben hiba történt. |Ellenőrizze a szűrési helyességét. |Nem nyújt az újra gombra. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Kísérlet történt egy munkamenetet egy adott munkamenet-azonosító, de a munkamenet jelenleg zárolva van egy másik ügyfél fogadásához. |Győződjön meg arról, hogy a munkamenet más ügyfelek nincs zárolva. |Újrapróbálkozási segíthet, ha a munkamenet már elérhető az ideiglenes. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Túl sok művelet a tranzakció részét képezik. |A tranzakció részét képező műveletek számának csökkentése. |Nem nyújt az újra gombra. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Egy futtatási művelet a letiltott entitásra vonatkozó kérést. |Az entitás aktiválása. |Újrapróbálkozási segíthet, ha időközben aktív az entitás. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus ezt a kivételt adja vissza, ha üzenet küldése előre szűrés engedélyezve van, és a szűrők egyike sem felel meg a témakör. |Győződjön meg arról, hogy legalább egy szűrő, amely megfelel. |Nem nyújt az újra gombra. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Üzenetadatokat meghaladja a 256 KB-os korlátozását. Vegye figyelembe, hogy a 256 Kilobájtos korlátnál az teljes üzenet mérete, ami magában foglalhatja a Rendszertulajdonságok és bármely .NET terhelés mellett. |Csökkentse a üzenetadatokat méretét, majd próbálja megismételni a műveletet. |Nem nyújt az újra gombra. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A környezeti tranzakció (*Transaction.Current*) érvénytelen. Előfordulhat, hogy befejeződött vagy megszakadt. További információt tartalmaznak a belső kivételben találhatók. | |Nem nyújt az újra gombra. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Egy művelet kísérletet egy tranzakció kimenetele kétséges, vagy a tranzakció véglegesítése tett kísérlet, és a tranzakció válik kétséges. |Az alkalmazás kezelni a tranzakció már lehetett véglegesíteni kell (a egy különleges esetben), ezt a kivételt. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelzi, hogy egy adott entitás kvóta túl lett lépve.

### <a name="queues-and-topics"></a>Üzenetsorok és témakörök
Üzenetsorok és témakörök ez pedig gyakran a sor mérete. A hiba üzenettulajdonságot tartalmaz további részleteket az alábbi példában látható módon:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Az üzenet jelzi, hogy a témakör meghaladta a maximális méretét, a nagybetűk (az alapértelmezett méretkorlátját) 1 GB-ban. 

### <a name="namespaces"></a>Névterek

A névterek [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelezheti, hogy egy alkalmazás túllépte a névtérre kapcsolatok maximális számát. Példa:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Általános okok
Két közös Ez a hiba oka: a kézbesítetlen levelek várólistájára, és az üzenetet fogadó nem működik.

1. **[Kézbesítetlen levelek várólistájára](service-bus-dead-letter-queues.md)**  olvasó nem sikerült végrehajtani az üzenetek és az üzeneteket a rendszer visszairányítja a várólista/témakör a zárolás lejártakor. Ez akkor fordulhat elő, ha az olvasó tapasztal, amelyek megakadályozzák a hívása kivételt [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Miután egy olvasták 10-szer, átkerül a kézbesítetlen levelek várólistájára alapértelmezés szerint. Ez a viselkedés vezérli a [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) tulajdonság és alapértelmezett értéke 10. Üzenetek a kézbesítetlen levelek várólistájára a egymásra, mint helyet foglaljanak.
   
    A probléma megoldásához olvassa el, és végezze el a kézbesítetlen levelek várólistájára üzeneteinek, mint bármely más várólistából. Használhatja a [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) segítségével formázza a kézbesítetlen levelek várólistájára elérési útja.
2. **A fogadó leállt** A fogadó leállt üzenetek fogadása egy üzenetsorból vagy előfizetés. A azonosítása ez módja tekintse meg a [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) tulajdonság, amely az üzenetek teljes lebontása látható. Ha a [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) tulajdonság magas vagy növekvő, akkor az üzenetek nem olvas gyors azok ír.

### <a name="event-hubs"></a>Event Hubs
Az Event Hubs maximális hossza 20 felhasználói csoportot az Event Hubs egy rendelkezik. Hozzon létre több kísérli meg, amikor egy [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy egy felhasználó által kezdeményezett művelet a vártnál a művelet időkorlátja lejár. 

Ellenőrizni kell az értékét a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) is eredményezheti, hogy elérte-e ezt a határt, a tulajdonság egy [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Üzenetsorok és témakörök
Az üzenetsoroktól és témaköröktől megadott időkorlát vagy a [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) tulajdonság részeként a kapcsolati karakterlánc, vagy keresztül [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A hibaüzenet a következő maga is eltérők lehetnek, de az az aktuális művelet a megadott időtúllépési érték mindig tartalmaz. 



## <a name="next-steps"></a>Következő lépések

A Service Bus .NET API teljes referenciáért lásd: a [Azure .NET API-referencia](/dotnet/api/overview/azure/service-bus).

További részletek [Service Bus](https://azure.microsoft.com/services/service-bus/), a következő cikkekben talál:

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-architektúra](service-bus-architecture.md)

