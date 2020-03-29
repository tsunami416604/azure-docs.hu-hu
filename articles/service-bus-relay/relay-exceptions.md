---
title: Az Azure Relay kivételei és megoldásuk | Microsoft dokumentumok
description: Az Azure Relay-kivételek és a megoldásuk hozadékának érdekében javasolt műveletek listája.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60749036"
---
# <a name="azure-relay-exceptions"></a>Azure Relay kivételek

Ez a cikk felsorolja az okat, amelyek az Azure Relay API-k által létrehozott néhány kivételt sorol fel. Ez a hivatkozás változhat, ezért látogasson vissza a frissítéseket.

## <a name="exception-categories"></a>Kivételkategóriák

A továbbító API-k olyan kivételeket hoznak létre, amelyek a következő kategóriákba sorolhatók. A felsorolásban szerepelnek azok a javasolt műveletek is, amelyek segítségével megoldhatja a kivételeket.

*   **Felhasználói kódolási hiba**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Általános művelet:** A folytatás előtt próbálja meg kijavítani a kódot.
*   **Telepítési/konfigurációs hiba**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Általános művelet:** Tekintse át a konfigurációt. Szükség esetén módosítsa a konfigurációt.
*   **Átmeneti kivételek**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Általános művelet**: Próbálkozzon újra a művelettel, vagy értesítse a felhasználókat.
*   **Egyéb kivételek**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Általános intézkedés**: A kivételtípusra jellemző. Lásd a táblázatot a következő szakaszban. 

## <a name="exception-types"></a>Kivételtípusok

Az alábbi táblázat felsorolja az üzenetküldési kivételtípusokat és azok okait. Megjegyzi továbbá a kivételek megoldásához szükséges műveleteket.

| **Kivételtípus** | **Leírás** | **Javasolt művelet** | **Megjegyzés az automatikus vagy azonnali újrapróbálkozáshoz** |
| --- | --- | --- | --- |
| [Időtúllépés](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kiszolgáló nem válaszolt a kért műveletre a megadott időn belül, amelyet az [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout)vezérel. Lehet, hogy a kiszolgáló befejezte a kért műveletet. Ez hálózati vagy egyéb infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotát a konzisztencia szempontjából, majd szükség esetén próbálkozzon újra. Lásd: [TimeoutException](#timeoutexception). |Az újrapróbálkozás bizonyos esetekben segíthet; újrapróbálkozási logika hozzáadása a kódhoz. |
| [Érvénytelen művelet](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részleteket lásd a kivételüzenetben. |Ellenőrizze a kódot és a dokumentációt. Ellenőrizze, hogy a kért művelet érvényes-e. |Az újrapróbálkozás nem segít. |
| [A művelet megszakítva](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Kísérlet történt egy művelet meghívására egy olyan objektumon, amelymár le van zárva, megszakadt vagy ártalmatlanított. Ritka esetekben a környezeti tranzakció már el van helyezve. |Ellenőrizze a kódot, és győződjön meg arról, hogy nem hív meg műveleteket egy ártalmatlanított objektumon. |Az újrapróbálkozás nem segít. |
| [Jogosulatlan hozzáférés](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott jogkivonatot szerezni, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet végrehajtásához szükséges jogcímeket. |Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel jön létre. Ellenőrizze a hozzáférés-vezérlési szolgáltatás konfigurációját. |Az újrapróbálkozás bizonyos esetekben segíthet; újrapróbálkozási logika hozzáadása a kódhoz. |
| [Argumentl kivétel](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [ArgumentY Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumentum kívül esik a tartományon](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Az alábbiak közül egy vagy több történt:<br />A metódushoz megadott egy vagy több argumentum érvénytelen.<br /> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [a Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) számára megadott URI egy vagy több elérési útszegmenst tartalmaz.<br />A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy a Create számára megadott URI-séma érvénytelen. [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) <br />A tulajdonság értéke nagyobb, mint 32 KB. |Ellenőrizze a hívókódot, és győződjön meg arról, hogy az argumentumok helyesek. |Az újrapróbálkozás nem segít. |
| [Kiszolgáló foglalt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. |Az ügyfél várhat egy ideig, majd próbálja meg újra a műveletet. |Előfordulhat, hogy az ügyfél egy adott időköz után újra próbálkozik. Ha egy újrapróbálkozás egy másik kivételt eredményez, ellenőrizze a kivétel újrapróbálkozási viselkedését. |
| [Kvóta túllépve](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Az üzenetküldő entitás elérte a megengedett maximális méretet. |Hozzon létre helyet az entitásban az entitástól vagy annak alvárólistáitól érkező üzenetek fogadásával. Lásd [QuotaExceededException](#quotaexceededexception). |Az újrapróbálkozás segíthet, ha időközben eltávolították az üzeneteket. |
| [Az üzenetek mérete túllépve](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Az üzenet hasznos mennyisége meghaladja a 256 KB-os korlátot. Ne feledje, hogy a 256 KB-os korlát az üzenet teljes mérete. Az üzenet teljes mérete magában foglalhatja a rendszer tulajdonságait és a Microsoft .NET terhelését. |Csökkentse az üzenet hasznos adatának méretét, majd próbálkozzon újra a művelettel. |Az újrapróbálkozás nem segít. |

## <a name="quotaexceededexception"></a>QuotaExceededKivétel

[A QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitáskvótája túllépve.

A továbbítás esetén ez a kivétel a [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), amely azt jelzi, hogy a figyelők maximális száma túllépte ezt a végpontot. Ezt a kivételüzenet **MaximumListenersPerEndpoint** értéke jelzi.

## <a name="timeoutexception"></a>IdőoutKivétel
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy a felhasználó által kezdeményezett művelet hosszabb időt vesz igénybe, mint a művelet időkitöltése. 

Ellenőrizze a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság értékét. A korlát elérése [időtúllépéskivételt](https://msdn.microsoft.com/library/system.timeoutexception.aspx)is okozhat.

A Továbbítás esetében időtúllépési kivételeket kaphat, amikor először nyit meg egy továbbító feladói kapcsolatot. Ennek a kivételnek két gyakori oka van:

*   Az [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) érték lehet túl kicsi (ha még egy másodperc töredéke).
* Előfordulhat, hogy a helyszíni továbbítófigyelő nem válaszol (vagy tűzfalszabályokkal kapcsolatos problémákmerülnek fel, amelyek megtiltják a figyelők számára az új ügyfélkapcsolatok fogadását), és az [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) értéke kevesebb, mint 20 másodperc.

Példa:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Gyakori okok
A hibának két gyakori oka van:

*   **A konfiguráció helytelen**
    
    Lehet, hogy a művelet időtúlideje túl kicsi a működési feltételhez. Az ügyfél SDK-ban a művelet időtúltartományának alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy a kód értéke túl kicsi re van-e beállítva. Vegye figyelembe, hogy a processzorhasználat és a hálózat állapota befolyásolhatja a művelet befejezéséhez szükséges időt. Célszerű, hogy ne állítsa a művelet időtúlértékét egy nagyon kis értékre.
*   **Átmeneti szolgáltatáshiba**

    Alkalmanként a továbbító szolgáltatás a kérelmek feldolgozásának késését tapasztalhatja. Ez például nagy forgalmú időszakokban fordulhat elő. Ebben az esetben próbálkozzon újra a művelettel egy késleltetés után, amíg a művelet sikeres nem lesz. Ha ugyanaz a művelet több kísérlet után is sikertelen, ellenőrizze az [Azure-szolgáltatás állapotwebhelyét,](https://azure.microsoft.com/status/) hogy vannak-e ismert szolgáltatáskimaradások.

## <a name="next-steps"></a>További lépések
* [Azure Relay – gyakori kérdések](relay-faq.md)
* [Továbbító névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés az Azure Relayrel és a .NET szolgáltatással](relay-hybrid-connections-dotnet-get-started.md)
* [Ismerkedés az Azure Relay relével és a node-val](relay-hybrid-connections-node-get-started.md)

