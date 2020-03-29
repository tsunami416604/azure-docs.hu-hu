---
title: Hibaelhárítási útmutató - Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs üzenetkezelési kivételeinek és javasolt műveleteknek a listáját tartalmazza.
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
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309778"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Hibaelhárítási útmutató az Azure Event Hubs-hoz
Ez a cikk az Event Hubs . 

## <a name="event-hubs-messaging-exceptions---net"></a>Eseményközpontok üzenetkezelési kivételei - .NET
Ez a szakasz a . 

### <a name="exception-categories"></a>Kivételkategóriák

Az Event Hubs .NET API-k kivételeket hoznak létre, amelyek a következő kategóriákba sorolhatók, valamint a kapcsolódó művelettel együtt, amelyekkel megpróbálhatja kijavítani őket.

1. Felhasználói kódolási hiba: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Általános művelet: próbálja meg kijavítani a kódot, mielőtt folytatná.
2. Telepítési/konfigurációs hiba: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: tekintse át a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Általános művelet: próbálja meg újra a műveletet, vagy értesítse a felhasználókat.
4. Egyéb kivételek: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Általános intézkedés: a kivételtípusra jellemző; lásd a következő szakaszban található táblázatot. 

### <a name="exception-types"></a>Kivételtípusok
Az alábbi táblázat felsorolja az üzenetküldési kivételtípusokat, azok okait, valamint a javasolt műveleteket.

| Kivétel típusa | Leírás/Ok/Példák | Javasolt művelet | Megjegyzés az automatikus/azonnali újrapróbálkozásról |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [IdőoutKivétel](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kiszolgáló nem válaszolt a kért műveletre a megadott időn belül, amelyet az [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vezérel. Lehet, hogy a kiszolgáló befejezte a kért műveletet. Ez a kivétel hálózati vagy egyéb infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotát a konzisztencia szempontjából, és szükség esetén próbálkozzon újra.<br /> Lásd: [TimeoutException](#timeoutexception). | Az újrapróbálkozás bizonyos esetekben segíthet; újrapróbálkozási logika hozzáadása a kódhoz. |
| [ÉrvénytelenMűveletkivétel](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részleteket lásd a kivételüzenetben. A [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) például akkor hozza létre ezt a kivételt, ha az üzenet [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) módban érkezett. | Ellenőrizze a kódot és a dokumentációt. Ellenőrizze, hogy a kért művelet érvényes-e. | Az újrapróbálkozás nem segít. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Kísérlet történt egy művelet meghívására egy olyan objektumon, amelymár le van zárva, megszakadt vagy ártalmatlanított. Ritka esetekben a környezeti tranzakció már el van helyezve. | Ellenőrizze a kódot, és győződjön meg arról, hogy nem hívja meg a műveleteket egy ártalmatlanított objektumon. | Az újrapróbálkozás nem segít. |
| [Jogosulatlan AccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott megszerezni egy jogkivonatot, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet hez szükséges jogcímeket. | Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel jön létre. Ellenőrizze a hozzáférés-vezérlési szolgáltatás konfigurációját. | Az újrapróbálkozás bizonyos esetekben segíthet; újrapróbálkozási logika hozzáadása a kódhoz. |
| [ArgumentException argumentum](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | A metódushoz megadott egy vagy több argumentum érvénytelen. A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [a Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) számára megadott URI elérési útszegmens(eke)t tartalmaz. A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy a Create számára megadott URI-séma érvénytelen. [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) A tulajdonság értéke nagyobb, mint 32 KB. | Ellenőrizze a hívókódot, és győződjön meg arról, hogy az argumentumok helyesek. | Az újrapróbálkozás nem segít. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A művelethez társított entitás nem létezik, vagy törölték. | Ellenőrizze, hogy létezik-e az entitás. | Az újrapróbálkozás nem segít. |
| [ÜzenetküldésKommunikációs kivétel](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Az ügyfél nem tud kapcsolatot létesíteni az Event Hubbal. |Ellenőrizze, hogy a megadott állomásnév helyes-e, és hogy az állomás elérhető-e. | Az újrapróbálkozás segíthet, ha időszakos kapcsolódási problémák merülnek fel. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. | Az ügyfél várhat egy ideig, majd próbálkozzon újra a művelettel. <br /> Lásd: [ServerBusyException](#serverbusyexception). | Az ügyfél bizonyos időköz után újra próbálkozhat. Ha egy újrapróbálkozás egy másik kivételt eredményez, ellenőrizze a kivétel újrapróbálkozási viselkedését. |
| [MessagingIngException (Üzenetkivétel)](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Általános üzenetkezelési kivétel, amely a következő esetekben fordulhat elő: Kísérlet történik egy [QueueClient létrehozására](/dotnet/api/microsoft.servicebus.messaging.queueclient) egy másik entitástípushoz (például egy témakörhöz) tartozó név vagy elérési út használatával. 1 MB-nál nagyobb üzenet küldésére tesz kísérletet. A kiszolgáló vagy a szolgáltatás hibát észlelt a kérelem feldolgozása során. A részleteket lásd a kivételüzenetben. Ez a kivétel általában átmeneti kivétel. | Ellenőrizze a kódot, és győződjön meg arról, hogy csak szerializálható objektumokat használ az üzenet törzséhez (vagy egyéni szerializálót). Ellenőrizze a tulajdonságok támogatott értéktípusainak dokumentációját, és csak a támogatott típusokat használja. Ellenőrizze az [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonságot. Ha ez **igaz,** próbálkozzon újra a művelettel. | Az újrapróbálkozási viselkedés nem definiált, és lehet, hogy nem segít. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Olyan entitás létrehozására, amelynek nevét a szolgáltatás névterén egy másik entitás már használja. | Törölje a meglévő entitást, vagy válasszon másik nevet a létrehozandó entitáshoz. | Az újrapróbálkozás nem segít. |
| [QuotaExceededKivétel](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Az üzenetküldő entitás elérte a megengedett maximális méretet. Ez a kivétel akkor fordulhat elő, ha a vevőkészülékek maximális számát (amely 5) már megnyitotta a fogyasztói csoportonkénti szinten. | Hozzon létre helyet az entitásban az entitástól vagy annak alvárólistáitól érkező üzenetek fogadásával. <br /> Lásd [QuotaExceededException](#quotaexceededexception) | Az újrapróbálkozás segíthet, ha időközben eltávolították az üzeneteket. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Futásidejű művelet kérése egy letiltott entitáson. |Aktiválja az entitást. | Az újrapróbálkozás segíthet, ha az entitást időközben aktiválták. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Az üzenet hasznos mennyisége meghaladja az 1 MB-os korlátot. Ez az 1 MB-os korlát a teljes üzenetre van, amely magában foglalhatja a rendszer tulajdonságait és a .NET terhelést. | Csökkentse az üzenet hasznos adatának méretét, majd próbálkozzon újra a művelettel. |Az újrapróbálkozás nem segít. |

### <a name="quotaexceededexception"></a>QuotaExceededKivétel
[A QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitáskvótája túllépve.

Ez a kivétel akkor fordulhat elő, ha a vevőkészülékek maximális számát (5) már megnyitottak a fogyasztói csoportonkénti szinten.

#### <a name="event-hubs"></a>Event Hubs
Az Event Hubs eseményközpontonként legfeljebb 20 fogyasztói csoporttal rendelkezik. Ha további rendszert próbál létrehozni, a [QuotaExceededException .](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) 

### <a name="timeoutexception"></a>IdőoutKivétel
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy a felhasználó által kezdeményezett művelet hosszabb időt vesz igénybe, mint a művelet időkitöltése. 

Az Event Hubs esetében az időtúllépés a kapcsolati karakterlánc részeként vagy a [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)szolgáltatáson keresztül van megadva. Maga a hibaüzenet változhat, de mindig tartalmazza az aktuális művelethez megadott időtúlértéket. 

#### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van: helytelen konfiguráció vagy átmeneti szolgáltatáshiba.

1. **Helytelen konfiguráció** Lehet, hogy a művelet időtúlideje túl kicsi a működési feltételhez. Az ügyfél SDK-ban a művelet időtúltartományának alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy a kód értéke túl kicsi értékre van-e állítva. A hálózat és a processzor használatának állapota befolyásolhatja egy adott művelet befejezéséhez szükséges időt, ezért a művelet időtúllépése nem állítható kis értékre.
2. **Átmeneti szolgáltatáshiba** Előfordulhat, hogy az Event Hubs szolgáltatás késedelmet szenved a kérelmek feldolgozásában; például nagy forgalmú időszakokban. Ilyen esetekben újra próbálkozhat a művelettel egy késleltetés után, amíg a művelet sikeres nem lesz. Ha ugyanaz a művelet több kísérlet után is sikertelen, látogasson el az [Azure-szolgáltatás állapotwebhelyére,](https://azure.microsoft.com/status/) és nézze meg, hogy vannak-e ismert szolgáltatáskimaradások.

### <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) vagy [a Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) azt jelzi, hogy a kiszolgáló túlterhelt. Ehhez a kivételhez két vonatkozó hibakód vonatkozik.

#### <a name="error-code-50002"></a>50002-es hibakód

Ennek a hibának az egyik oka lehet:

1. A terhelés nem egyenletesen oszlik el az eseményközpont összes partíciója között, és egy partíció eléri a helyi átviteli egység korlátozását.
    
    Megoldás: A partícióterjesztési stratégia felülvizsgálata vagy az [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kipróbálása segíthet.

2. Az Event Hubs névtér nem rendelkezik elegendő átviteli egységgel (ellenőrizheti a **Metrikák képernyőt** az Azure [Portal](https://portal.azure.com) On-Hubs névtér ablakában a megerősítéshez). A portál összesített (1 perces) adatokat jelenít meg, de az átviteli időt valós időben mérjük – így ez csak becslés.

    Megoldás: A névtér átviteli egységeinek növelése segíthet. Ezt a műveletet a portálon, az Event Hubs névtér képernyő **Méretezés** ablakában teheti meg. Vagy használhatja [az Automatikus felfújás](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>50001-es hibakód

Ez a hiba ritkán fordulhat elő. Ez akkor fordul elő, ha a névtér kódot futtató tárolóban kevés a CPU – legfeljebb néhány másodperccel az Event Hubs terheléselosztó kezdete előtt.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>A GetRuntimeInformation metódus hívásainak korlátozása
Az Azure Event Hubs másodpercenként legfeljebb 50 hívást támogat a GetRuntimeInfo másodpercenként. A korlát elérése után az alábbihoz hasonló kivételt kaphat:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Kapcsolódási, tanúsítvány- vagy idő-meghosszabbítási problémák
A következő lépések segíthetnek a kapcsolódási/tanúsítvány-/idő-kikapcsolási problémák elhárításában a *.servicebus.windows.net alatt található összes szolgáltatás esetében. 

- Tallózás vagy [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Segít annak ellenőrzésében, hogy van-e IP-szűrés vagy virtuális hálózati vagy tanúsítványlánc problémák (a leggyakoribb java SDK használataesetén).

    Példa a sikeres üzenetre:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Példa hibaüzenet a hibaüzenetre:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Futtassa a következő parancsot annak ellenőrzéséhez, hogy a tűzfal on-e blokkolva van-e a port. A használt portok: 443 (HTTPS), 5671 (AMQP) és 9093 (Kafka). A használt könyvtártól függően más portok is használatosak. Itt van a minta parancs, amely ellenőrzi, hogy az 5671 port blokkolva van-e.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linuxalatt:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Ha időszakos kapcsolódási problémák merülnek fel, futtassa a következő parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ez a parancs 1 másodpercenként 25 különböző TCP-kapcsolatot próbál létesíteni a szolgáltatással. Ezután ellenőrizheti, hogy közülük hánysikerült/sikertelen, és megtekintheti a TCP-kapcsolat késését is. Letöltheti az `psping` eszközt [innen](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Egyenértékű parancsokat használhat, ha más eszközöket `tnc`használ, például a , `ping`és így tovább. 
- Szerezzen be egy hálózati nyomkövetést, ha az előző lépések nem segítenek, és elemezze azt olyan eszközökkel, mint a [Wireshark](https://www.wireshark.org/). Szükség esetén forduljon [a Microsoft támogatási szolgálatához.](https://support.microsoft.com/) 

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Eseményközpontok – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
