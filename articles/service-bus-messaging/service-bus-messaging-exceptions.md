---
title: Azure Service Bus – üzenetküldési kivételek | Microsoft dokumentumok
description: Ez a cikk az Azure Service Bus üzenetkezelési kivételeinek listáját és a kivétel bekövetkezésekor végrehajtandó javasolt műveleteket tartalmazza.
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
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880788"
---
# <a name="service-bus-messaging-exceptions"></a>A Service Bus üzenetkezelési kivételei
Ez a cikk a . 

## <a name="exception-categories"></a>Kivételkategóriák
Az üzenetkezelési API-k kivételeket hoznak létre, amelyek a következő kategóriákba sorolhatók, valamint a kapcsolódó műveletet, amelyet megpróbálhat javukra végrehajtani. A kivétel jelentése és okai az üzenetkezelő entitás típusától függően változhatnak:

1. Felhasználói kódolási hiba ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException ).](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx) Általános művelet: próbálja meg kijavítani a kódot, mielőtt folytatná.
2. Telepítési/konfigurációs hiba ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: tekintse át a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Általános művelet: próbálja meg újra a műveletet, vagy értesítse a felhasználókat. Az `RetryPolicy` ügyfél SDK-ban lévő osztály beállítható úgy, hogy automatikusan kezelje az újrapróbálkozásokat. További információt az [Újraútmutató című](/azure/architecture/best-practices/retry-service-specific#service-bus)témakörben talál.
4. Egyéb kivételek ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Általános intézkedés: a kivételtípusra jellemző; lásd a következő szakasztáblázatát: 

## <a name="exception-types"></a>Kivételtípusok
Az alábbi táblázat felsorolja az üzenetküldési kivételtípusokat, azok okait, valamint a javasolt műveleteket.

| **Kivétel típusa** | **Leírás/Ok/Példák** | **Javasolt művelet** | **Megjegyzés az automatikus/azonnali újrapróbálkozásról** |
| --- | --- | --- | --- |
| [IdőoutKivétel](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kiszolgáló nem válaszolt a kért műveletre a megadott időn belül, amelyet az [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vezérel. Lehet, hogy a kiszolgáló befejezte a kért műveletet. Ez történhet, mert a hálózati vagy egyéb infrastruktúra késések. |Ellenőrizze a rendszerállapotát a konzisztencia szempontjából, és szükség esetén próbálkozzon újra. Lásd: [Időelési kivételek](#timeoutexception). |Az újrapróbálkozás bizonyos esetekben segíthet; újrapróbálkozási logika hozzáadása a kódhoz. |
| [ÉrvénytelenMűveletkivétel](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részleteket lásd a kivételüzenetben. A [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) például akkor hozza létre ezt a kivételt, ha az üzenet [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban érkezett. |Ellenőrizze a kódot és a dokumentációt. Ellenőrizze, hogy a kért művelet érvényes-e. |Az újrapróbálkozás nem segít. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Kísérlet történt egy művelet meghívására egy olyan objektumon, amelymár le van zárva, megszakadt vagy ártalmatlanított. Ritka esetekben a környezeti tranzakció már el van helyezve. |Ellenőrizze a kódot, és győződjön meg arról, hogy nem hívja meg a műveleteket egy ártalmatlanított objektumon. |Az újrapróbálkozás nem segít. |
| [Jogosulatlan AccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott megszerezni egy jogkivonatot, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet hez szükséges jogcímeket. |Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel jön létre. Ellenőrizze a hozzáférés-vezérlési szolgáltatás konfigurációját. |Az újrapróbálkozás bizonyos esetekben segíthet; újrapróbálkozási logika hozzáadása a kódhoz. |
| [ArgumentException argumentum](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |A metódushoz megadott egy vagy több argumentum érvénytelen.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [a Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) számára megadott URI elérési útszegmens(eke)t tartalmaz.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy a Create számára megadott URI-séma érvénytelen. [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) <br />A tulajdonság értéke nagyobb, mint 32 KB. |Ellenőrizze a hívókódot, és győződjön meg arról, hogy az argumentumok helyesek. |Az újrapróbálkozás nem segít. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A művelethez társított entitás nem létezik, vagy törölték. |Ellenőrizze, hogy létezik-e az entitás. |Az újrapróbálkozás nem segít. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Kísérlet egy adott sorszámmal rendelkező üzenet fogadására. Ez az üzenet nem található. |Ellenőrizze, hogy az üzenet még nem érkezett-e meg. Ellenőrizze a kézbesítetlen levelek várólistáját, hogy az üzenet kézbesített-e. |Az újrapróbálkozás nem segít. |
| [ÜzenetküldésKommunikációs kivétel](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Az ügyfél nem tud kapcsolatot létesíteni a Service Bus-szal. |Ellenőrizze, hogy a megadott állomásnév helyes-e, és hogy az állomás elérhető-e. |Az újrapróbálkozás segíthet, ha időszakos kapcsolódási problémák merülnek fel. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. |Az ügyfél várhat egy ideig, majd próbálkozzon újra a művelettel. |Az ügyfél bizonyos időköz után újra próbálkozhat. Ha egy újrapróbálkozás egy másik kivételt eredményez, ellenőrizze a kivétel újrapróbálkozási viselkedését. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Az üzenethez társított zárolási jogkivonat lejárt, vagy a zárolási jogkivonat nem található. |Dobja el az üzenetet. |Az újrapróbálkozás nem segít. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |A munkamenethez társított zárolás elvész. |A MessageSession objektum [megszakítása.](/dotnet/api/microsoft.servicebus.messaging.messagesession) |Az újrapróbálkozás nem segít. |
| [MessagingIngException (Üzenetkivétel)](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Általános üzenetküldési kivétel, amely a következő esetekben fordulhat elő:<p>Kísérlet történt egy [QueueClient létrehozására](/dotnet/api/microsoft.azure.servicebus.queueclient) egy másik entitástípushoz (például témakörhöz) tartozó név vagy elérési út használatával.</p><p>256 KB-nál nagyobb üzenet küldésére tesz kísérletet. </p>A kiszolgáló vagy a szolgáltatás hibát észlelt a kérelem feldolgozása során. A részleteket lásd a kivételüzenetben. Ez általában átmeneti kivétel.</p><p>A kérelem megszakadt, mert az entitás szabályozásalatt áll. Hibakód: 50001, 50002, 50008. </p> | Ellenőrizze a kódot, és győződjön meg arról, hogy csak szerializálható objektumokat használ az üzenet törzséhez (vagy egyéni szerializálót). <p>Ellenőrizze a tulajdonságok támogatott értéktípusainak dokumentációját, és csak a támogatott típusokat használja.</p><p> Ellenőrizze az [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonságot. Ha ez **igaz,** próbálkozzon újra a művelettel. </p>| Ha a kivétel a szabályozás miatt van, várjon néhány másodpercet, majd próbálkozzon újra a művelettel. Az újrapróbálkozási viselkedés nem definiált, és előfordulhat, hogy más esetekben nem segít.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Olyan entitás létrehozására, amelynek nevét a szolgáltatás névterén egy másik entitás már használja. |Törölje a meglévő entitást, vagy válasszon másik nevet a létrehozandó entitáshoz. |Az újrapróbálkozás nem segít. |
| [QuotaExceededKivétel](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Az üzenetküldő entitás elérte a megengedett maximális méretet, vagy túllépte a névtérrel való kapcsolatok maximális számát. |Hozzon létre helyet az entitásban az entitástól vagy annak alvárólistáitól érkező üzenetek fogadásával. Lásd [QuotaExceededException](#quotaexceededexception). |Az újrapróbálkozás segíthet, ha időközben eltávolították az üzeneteket. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |A Service Bus ezt a kivételt adja vissza, ha érvénytelen szabályműveletet próbál létrehozni. A Service Bus ezt a kivételt egy kézbesített üzenethez csatolja, ha hiba történik az üzenet szabályműveletének feldolgozása közben. |Ellenőrizze a szabályművelet helyességét. |Az újrapróbálkozás nem segít. |
| [FilterException (SzűrésKivétel)](/dotnet/api/microsoft.servicebus.messaging.filterexception) |A Service Bus ezt a kivételt adja vissza, ha érvénytelen szűrőt próbál létrehozni. A Service Bus ezt a kivételt egy kézbesített üzenethez csatolja, ha hiba történt az üzenet szűrőjének feldolgozása közben. |Ellenőrizze a szűrő helyességét. |Az újrapróbálkozás nem segít. |
| [A munkamenet nem lehetlockedkivétellel](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Kísérlet egy adott munkamenet-azonosítóval rendelkező munkamenet elfogadására, de a munkamenetet jelenleg egy másik ügyfél zárolja. |Győződjön meg arról, hogy a munkamenet zárolása más ügyfelek által fel van oldva. |Az újrapróbálkozás segíthet, ha a munkamenet időközben megjelent. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Túl sok művelet része a tranzakciónak. |Csökkentse a tranzakció részét használó műveletek számát. |Az újrapróbálkozás nem segít. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Futásidejű művelet kérése egy letiltott entitáson. |Aktiválja az entitást. |Az újrapróbálkozás segíthet, ha az entitást időközben aktiválták. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |A Service Bus ezt a kivételt adja vissza, ha olyan témakörnek küld üzenetet, amelynek engedélyezve van az előzetes szűrés, és egyik szűrő sem egyezik. |Győződjön meg arról, hogy legalább egy szűrő egyezik. |Az újrapróbálkozás nem segít. |
| [MessageSizeExceededKivétel](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Az üzenet hasznos mennyisége meghaladja a 256 KB-os korlátot. A 256 KB-os korlát az üzenet teljes mérete, amely magában foglalhatja a rendszertulajdonságokat és a .NET terhelést. |Csökkentse az üzenet hasznos adatának méretét, majd próbálkozzon újra a művelettel. |Az újrapróbálkozás nem segít. |
| [TransactionException (TranzakcióKivétel)](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A környezeti tranzakció (*Transaction.Current*) érvénytelen. Lehet, hogy befejeződött vagy megszakadt. A belső kivétel további információkat nyújthat. | |Az újrapróbálkozás nem segít. |
| [TransactionInDoubtKivétel](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |A művelet kísérlet egy olyan tranzakció, amely kétséges, vagy kísérlet készül a tranzakció véglegesítésére, és a tranzakció kétségessé válik. |Az alkalmazásnak kezelnie kell ezt a kivételt (különleges esetként), mivel a tranzakció taszthatmár véglegesítve. |- |

## <a name="quotaexceededexception"></a>QuotaExceededKivétel
[A QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelzi, hogy egy adott entitáskvótája túllépve.

### <a name="queues-and-topics"></a>Várólisták és témakörök
Várólisták és témakörök esetén gyakran a várólista mérete. A hibaüzenet tulajdonság további részleteket tartalmaz, mint a következő példában:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Az üzenet azt állítja, hogy a témakör túllépte a méretkorlátot, ebben az esetben 1 GB (az alapértelmezett méretkorlát). 

### <a name="namespaces"></a>Névterek

Névterek esetén [a QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) azt jelezheti, hogy egy alkalmazás túllépte a névtérhez való kapcsolatok maximális számát. Például:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van: a kézbesítetlen levelek várólistája és a nem működő üzenetfogadók.

1. **[Kézbesítetlen levelek várólistája](service-bus-dead-letter-queues.md)** Az olvasó nem tudta befejezni az üzeneteket, és az üzenetek a zárolás lejártakor visszakerülnek a várólistába/témakörbe. Ez akkor fordulhat elő, ha az olvasó olyan kivételt tapasztal, amely megakadályozza, hogy felhívja [a BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)szolgáltatást. Miután egy üzenetet 10-szer elolvasta, alapértelmezés szerint a kézbesítetlen levelek várólistájára kerül. Ezt a viselkedést a [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) tulajdonság szabályozza, és az alapértelmezett értéke 10. Ahogy az üzenetek felhalmozódnak a kézbesítetlen levelek várólistájában, helyet foglalnak.
   
    A probléma megoldásához olvassa el és fejezze be a kézbesítetlen levelek várólistájából származó üzeneteket, ugyanúgy, mint bármely más várólistából. A [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metódus segítségével formázhatja a kézbesítetlen levelek várólistájának elérési útját.
2. **A vevő leállt**. A fogadó leállt egy várólistából vagy előfizetésből érkező üzenetek fogadásával. Ennek azonosítása a [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) tulajdonságot jeleníti meg, amely az üzenetek teljes bontását mutatja. Ha az [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) tulajdonság magas vagy növekszik, akkor az üzenetek nem lesznek olyan gyorsan olvasva, mint ahogy azok írásra kerülnek.

## <a name="timeoutexception"></a>IdőoutKivétel
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy a felhasználó által kezdeményezett művelet hosszabb időt vesz igénybe, mint a művelet időkitöltése. 

Ellenőrizze a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság értékét, mivel ha eléri ezt a korlátot, az [időtúllépéskivételt](https://msdn.microsoft.com/library/system.timeoutexception.aspx)is okozhat.

### <a name="queues-and-topics"></a>Várólisták és témakörök
Várólisták és témakörök esetén az időtúllépés a [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) tulajdonságban, a kapcsolati karakterlánc részeként vagy a [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder)tulajdonságban van megadva. Maga a hibaüzenet változhat, de mindig tartalmazza az aktuális művelethez megadott időtúlértéket. 

## <a name="next-steps"></a>További lépések
A Service Bus .NET API teljes referencia, lásd az [Azure .NET API referencia](/dotnet/api/overview/azure/service-bus).
Hibaelhárítási tippeket a [Hibaelhárítási útmutatóban](service-bus-troubleshooting-guide.md) talál.
