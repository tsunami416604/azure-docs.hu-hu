---
title: Hibaelhárítási útmutató a Azure Service Bushoz | Microsoft Docs
description: Service Bus üzenetkezelési kivételek és javasolt műveletek listája.
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
ms.openlocfilehash: eebbef25f2cd4539a5092f271c3944c24503f287
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156811"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>A Azure Service Bus hibaelhárítási útmutatója
Ez a cikk a Service Bus .NET-keretrendszer API-jai által generált .NET-kivételeket, valamint a hibaelhárítással kapcsolatos egyéb tippeket tartalmaz. 

## <a name="service-bus-messaging-exceptions"></a>A Service Bus-alapú üzenetkezelés kivételei
Ez a szakasz a .NET-keretrendszer API-jai által generált .NET-kivételeket sorolja fel. 

### <a name="exception-categories"></a>Kivételek kategóriái
Az üzenetkezelési API-k olyan kivételeket állítanak elő, amelyek a következő kategóriákba sorolhatók, valamint a hozzájuk tartozó műveletekkel, amelyekkel kipróbálhatja a javítást. A kivételek jelentése és okai az üzenetkezelési entitás típusától függően eltérőek lehetnek:

1. Felhasználói kódolási hiba ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. szerializálás. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Általános művelet: a folytatás előtt próbálja meg kijavítani a kódot.
2. Telepítési/konfigurációs hiba ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: Ellenőrizze a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Általános művelet: Próbálja megismételni a műveletet, vagy értesítse a felhasználókat. Az ügyfél-SDK `RetryPolicy` osztálya beállítható úgy, hogy az újrapróbálkozások automatikusan kezeljék. További információ: [újrapróbálkozási útmutató](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Egyéb kivételek ([System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. timeoutexception osztályról](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Általános művelet: a kivétel típusára jellemző; Tekintse át a következő szakaszban található táblázatot: 

### <a name="exception-types"></a>Kivételek típusai
Az alábbi táblázat az üzenetkezelési kivételek típusait, valamint azok okait és megjegyzéseit sorolja fel.

| **Kivétel típusa** | **Leírás/ok/példák** | **Javasolt művelet** | **Megjegyzés automatikus/azonnali újrapróbálkozás** |
| --- | --- | --- | --- |
| [Timeoutexception osztályról](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kiszolgáló a megadott időn belül nem válaszolt a kért műveletre, amelyet a [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vezérel. Lehet, hogy a kiszolgáló végrehajtotta a kért műveletet. Hálózati vagy más infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotot a konzisztencia érdekében, és szükség esetén próbálkozzon újra. Lásd: [időtúllépési kivételek](#timeoutexception). |Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részletekért tekintse meg a kivételt jelző üzenetet. Például a [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ezt a kivételt hozza létre, ha az üzenet [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban érkezett. |Keresse meg a kódot és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvényes. |Az újrapróbálkozás nem segít. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Kísérlet történt egy olyan objektum műveletének meghívására, amely már be van zárva, megszakadt vagy el lett távolítva. Ritka esetekben a környezeti tranzakció már el van távolítva. |Ellenőrizze a kódot, és győződjön meg róla, hogy nem hív meg műveleteket egy eldobott objektumon. |Az újrapróbálkozás nem segít. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott jogkivonatot beszerezni, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet végrehajtásához szükséges jogcímeket. |Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel lett létrehozva. Vizsgálja meg a Access Control Service konfigurációját. |Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |A metódushoz megadott egy vagy több argumentum érvénytelen.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-azonosító szegmens (eke) t tartalmaz.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy- [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-séma érvénytelen. <br />A tulajdonság értéke nagyobb, mint 32 KB. |Ellenőrizze a hívó kódját, és ellenőrizze, hogy helyesek-e az argumentumok. |Az újrapróbálkozás nem segít. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A művelethez társított entitás nem létezik, vagy törölve lett. |Győződjön meg arról, hogy az entitás létezik. |Az újrapróbálkozás nem segít. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Egy megadott sorszámú üzenet fogadására tett kísérlet. Ez az üzenet nem található. |Győződjön meg arról, hogy az üzenet már nem érkezett meg. Tekintse meg a kézbesítetlen levelek-várólistát, és ellenőrizze, hogy az üzenet el lett-e deadlettered. |Az újrapróbálkozás nem segít. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Az ügyfél nem tud kapcsolatot létesíteni Service Bushoz. |Győződjön meg arról, hogy a megadott állomásnév helyes, és a gazdagép elérhető. |Az újrapróbálkozás akkor lehet hasznos, ha akadozó kapcsolódási problémák léptek fel. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. |Az ügyfél hosszabb ideig is megvárhat, majd próbálja megismételni a műveletet. |Előfordulhat, hogy az ügyfél bizonyos intervallum után újra próbálkozik. Ha az újrapróbálkozások eltérő kivételt eredményeznek, akkor ellenőrizze, hogy az újrapróbálkozási viselkedést az adott kivétel okozza. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Az üzenethez társított zárolási jogkivonat lejárt, vagy nem található a zárolási jogkivonat. |Az üzenet eldobása. |Az újrapróbálkozás nem segít. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |A munkamenethez társított zárolás elvész. |A [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektum megszakítása. |Az újrapróbálkozás nem segít. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Általános üzenetküldési kivétel, amely a következő esetekben fordulhat elő:<br /> Kísérlet történt olyan [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) létrehozására, amely egy másik entitás típusához (például egy témakörhöz) tartozó névvel vagy elérési úttal rendelkezik.<br />  Kísérlet történt a 256 KB-nál nagyobb üzenetek küldésére. A kiszolgáló vagy szolgáltatás hibát észlelt a kérelem feldolgozása során. A részletekért tekintse meg a kivételt jelző üzenetet. Ez általában átmeneti kivétel. |Ellenőrizze a kódot, és győződjön meg arról, hogy csak szerializálható objektumok használatosak az üzenet törzséhez (vagy használjon egyéni szerializáló). Keresse meg a tulajdonságok támogatott értékeit, és csak a támogatott típusokat használja. Keresse meg a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonságot. Ha az **értéke igaz**, megismételheti a műveletet. |Az újrapróbálkozási viselkedés nincs meghatározva, és lehet, hogy nem segít. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Kísérlet történt olyan entitás létrehozására, amelynek a neve már használja egy másik entitás által az adott szolgáltatási névtérben. |Törölje a meglévő entitást, vagy válasszon másik nevet a létrehozandó entitás számára. |Az újrapróbálkozás nem segít. |
| [Quotaexceededexception osztályról](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Az üzenetküldési entitás elérte a maximálisan megengedett méretet, vagy túllépte a névtér kapcsolatainak maximális számát. |Hozzon létre helyet az entitásban az entitásból vagy annak alvárólistából érkező üzenetek fogadásával. Lásd: [quotaexceededexception osztályról](#quotaexceededexception). |Az újrapróbálkozás akkor lehet hasznos, ha az üzenetek időközben el lettek távolítva. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |A Service Bus ezt a kivételt adja vissza, ha érvénytelen szabály-műveletet próbál meg létrehozni. Service Bus ezt a kivételt csatolja egy deadlettered-üzenethez, ha hiba történik az üzenetre vonatkozó szabály műveletének feldolgozása során. |Győződjön meg arról, hogy a szabály művelete megfelelő. |Az újrapróbálkozás nem segít. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |A Service Bus ezt a kivételt adja vissza, ha érvénytelen szűrőt próbál létrehozni. A Service Bus ezt a kivételt egy deadlettered-üzenethez csatolja, ha hiba történt az üzenet szűrőjének feldolgozása során. |Győződjön meg a szűrő helyességéről. |Az újrapróbálkozás nem segít. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Kísérlet történt egy munkamenet elfogadására egy adott munkamenet-AZONOSÍTÓval, de a munkamenetet jelenleg egy másik ügyfél zárolta. |Győződjön meg arról, hogy a munkamenetet más ügyfelek is feloldják. |Az újrapróbálkozás segíthet abban az esetben, ha a munkamenetet ideiglenes kiadásban adták ki. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Túl sok művelet része a tranzakciónak. |Csökkentse a tranzakció részét képező műveletek számát. |Az újrapróbálkozás nem segít. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Futásidejű művelet kérése letiltott entitáson. |Aktiválja az entitást. |Az újrapróbálkozás segíthet abban az esetben, ha az entitást ideiglenesen aktiválták. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |A Service Bus ezt a kivételt adja vissza, ha üzenetet küld egy olyan témakörnek, amelyen engedélyezve van az előzetes szűrés, és a szűrők egyike sem egyezik. |Győződjön meg arról, hogy legalább egy szűrő egyezik. |Az újrapróbálkozás nem segít. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Az üzenet tartalma meghaladja az 256-KB korlátot. Az 256-KB korlát a teljes üzenet mérete, amely magában foglalhatja a rendszertulajdonságokat és a .NET-terhelést is. |Csökkentse az üzenet adattartalmát, majd próbálja megismételni a műveletet. |Az újrapróbálkozás nem segít. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A környezeti tranzakció (*tranzakció. Current*) érvénytelen. Lehetséges, hogy a művelet befejeződött vagy meg lett szakítva. A belső kivétel további információkat is biztosíthat. | |Az újrapróbálkozás nem segít. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Egy művelet egy olyan tranzakcióra próbálkozik, amely kétséges, vagy kísérlet történt a tranzakció véglegesítére, és a tranzakció kétségbe kerül. |Az alkalmazásnak kezelnie kell ezt a kivételt (különleges esetben), mert a tranzakció már véglegesítve lett. |- |

### <a name="quotaexceededexception"></a>Quotaexceededexception osztályról
A [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) kivétel azt jelzi, hogy túllépte az adott entitáshoz tartozó kvótát.

#### <a name="queues-and-topics"></a>Várólisták és témakörök
Várólisták és témakörök esetében ez gyakran a várólista mérete. A hibaüzenet tulajdonsága további részleteket tartalmaz, ahogy az alábbi példában is látható:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Az üzenet azt jelzi, hogy a témakör túllépte a méretkorlátot, ebben az esetben 1 GB-ot (az alapértelmezett méretkorlát). 

#### <a name="namespaces"></a>Névterek

A névterek esetében a [quotaexceededexception osztályról](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) jelezheti, hogy egy alkalmazás túllépte a névtérrel létesített kapcsolatok maximális számát. Példa:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van: a kézbesítetlen levelek várólistája és a nem működő üzenetek fogadói.

1. **[Kézbesítetlen levelek várólistája](service-bus-dead-letter-queues.md)** Az olvasó nem teljesíti az üzeneteket, és az üzenetek az üzenetsor/témakörbe kerülnek vissza, amikor a zárolás lejár. Ez akkor fordulhat elő, ha az olvasó olyan kivételt észlel, amely megakadályozza a [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete)meghívását. Az üzenet 10 alkalommal való olvasása után a rendszer alapértelmezés szerint áthelyezi a kézbesítetlen levelek várólistára. Ezt a viselkedést a [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) tulajdonság szabályozza, és az alapértelmezett érték 10. Az üzenetek a kézbesítetlen levelek várólistáján halmoznak fel helyet.
   
    A probléma megoldásához olvassa el és fejezze be az üzeneteket a kézbesítetlen levelek várólistából, ahogy azt bármely más üzenetsor esetében tenné. A [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metódus segítségével formázhatja a kézbesítetlen levelek várólistájának elérési útját.
2. A **fogadó leállt**. Egy fogadó leállt egy üzenetsor vagy előfizetés üzeneteinek fogadásával. Ennek azonosítására az [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) tulajdonságot kell megtekinteni, amely az üzenetek teljes bontását mutatja. Ha a [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) tulajdonság magas vagy növekvő, akkor az üzenetek nem olvashatók be az írás során.

### <a name="timeoutexception"></a>Timeoutexception osztályról
A [timeoutexception osztályról](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy a felhasználó által kezdeményezett művelet a művelet időkorlátja alatt hosszabb időt vesz igénybe. 

Győződjön meg arról, hogy a [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság értéke, mivel ez a korlát is okozhat [timeoutexception osztályról](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Várólisták és témakörök
A várólisták és témakörök esetében az időtúllépést a [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) tulajdonság adja meg a kapcsolati karakterlánc részeként, vagy a [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder)-n keresztül. Maga a hibaüzenet is változhat, de mindig az aktuális művelethez megadott időtúllépési értéket tartalmazza. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Kapcsolati, tanúsítvány-vagy időtúllépési problémák
A következő lépések segítséget nyújthatnak a kapcsolat/tanúsítvány/időtúllépési problémák hibaelhárításához a *. servicebus.windows.net alatti összes szolgáltatáshoz. 

- Tallózással keresse meg vagy a [wget](https://www.gnu.org/software/wget/) `https://sbwagn2.servicebus.windows.net/`. Segít ellenőrizni, hogy rendelkezik-e IP-szűréssel, illetve virtuális hálózati vagy tanúsítványlánc-problémákkal (a Java SDK használatakor leggyakrabban).
- A következő parancs futtatásával ellenőrizze, hogy a tűzfal blokkolja-e a portokat. A használt könyvtártól függően más portok is használatban vannak. Például: 443, 5672, 9354.

    ```powershell
    tnc sbwagn2.servicebus.windows.net -port 5671
    ```

    Linux rendszeren:

    ```shell
    telnet sbwagn2.servicebus.windows.net 5671
    ```
- Időnkénti kapcsolódási problémák esetén futtassa az alábbi parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ezzel a paranccsal 25 különböző TCP-kapcsolatot hozhat létre másodpercenként a szolgáltatással, így a sikeres/sikertelen és a TCP-kapcsolat késése is megtekinthető. Az `psping` eszközt [innen](/sysinternals/downloads/psping)töltheti le.

    ```shell
    .\psping.exe -n 25 -i 1 -q yournamespace.servicebus.windows.net:5671 -nobanner     
    ```
    Ha más eszközöket (például `tnc`, `ping`stb.) használ, egyenértékű parancsokat is használhat. 
- Szerezze be a hálózati nyomkövetést, ha az előző lépések nem segítenek és nem elemzik, vagy vegye fel a kapcsolatot [Microsoft ügyfélszolgálata](https://support.microsoft.com/).


## <a name="next-steps"></a>Következő lépések

A teljes Service Bus .NET API-referenciával kapcsolatban tekintse meg az [Azure .NET API-referenciát](/dotnet/api/overview/azure/service-bus).

Ha többet szeretne megtudni a [Service Busről](https://azure.microsoft.com/services/service-bus/), tekintse meg a következő cikkeket:

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [Service Bus-architektúra](service-bus-architecture.md)

