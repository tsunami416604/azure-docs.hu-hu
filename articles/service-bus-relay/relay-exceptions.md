---
title: "Azure továbbítási kivételeket és azok megoldását |} Microsoft Docs"
description: "Azure továbbítási kivételeket és azok megoldása érdekében elvégezhető javasolt műveletek listájának beolvasása."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.openlocfilehash: 83ff97b59e428e7b617a7f5d1011ca5ddf3060b6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-relay-exceptions"></a>Az Azure továbbítási kivételek

Ez a cikk az Azure-továbbítási API-k által előfordulhat, hogy okozó kivétel sorolja fel. Ez a hivatkozás változhat, így biztonsági frissítések keresése.

## <a name="exception-categories"></a>Kivétel kategóriák

A továbbító API-k kivételeket, amelyek esetleg tartaléka lehet a következő kategóriákba sorolhatók hoz létre. Is listában megtalálhatók a javasolt műveleteket, amelyek a kivételek elhárítása érdekében.

*   **Kódolási hiba felhasználói**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Általános művelet**: javító a kódot, mielőtt továbblép.
*   **A telepítő-konfigurációs hiba**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Általános művelet**: tekintse át a konfigurációt. Szükség esetén módosítsa a konfigurációt.
*   **Átmeneti kivételek**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Általános művelet**: próbálja megismételni a műveletet, vagy értesítse a felhasználókat.
*   **Más kivételek**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Általános művelet**: a kivétel típusát jellemző. Az alábbi részben található táblázatban találja. 

## <a name="exception-types"></a>Kivétel típusa

A következő táblázat üzenetkezelési kivétel típusa és az okaik. Azt is jelzi javasolt elvégezhető műveletekhez nyújtanak a kivételek elhárítása érdekében.

| **Kivétel típusa** | **Leírás** | **Javasolt művelet** | **Jegyezze fel az automatikus vagy azonnali újrapróbálkozási** |
| --- | --- | --- | --- |
| [Időtúllépés](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kért művelet, amely vezérli a meghatározott időn belül nem válaszol a kiszolgáló [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Előfordulhat, hogy a kiszolgáló befejezte a kért műveletet. Ez akkor fordulhat elő, hálózati vagy más infrastruktúra késleltetése miatt. |Ellenőrizze a rendszer, a konzisztencia, majd próbálkozzon újra, ha szükséges. Lásd: [TimeoutException](#timeoutexception). |Újrapróbálkozási segíthet bizonyos esetekben; újrapróbálkozási logika hozzáadása a kódot. |
| [Érvénytelen művelet](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgáló vagy a szolgáltatás belül. A kivétel üzenetét, a részletekért tekintse meg. |Ellenőrizze a kódot, és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvénytelen. |Nem nyújt az újra gombra. |
| [A művelet megszakítva](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Egy olyan objektum, amely már lezárták, megszakadt, vagy használaton művelet meghívásához tett kísérlet. Ritka esetekben a környezeti tranzakció már eldobták. |Ellenőrizze a kódot, és győződjön meg arról, hogy nem lép működésbe műveleteket végez el egy teljesített objektum. |Nem nyújt az újra gombra. |
| [Jogosulatlan hozzáférés](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektumot nem sikerült megszerezni a jogkivonatot, a token érvénytelen, vagy a jogkivonat nem tartalmazza a jogcímeket, a művelet végrehajtásához szükséges. |Győződjön meg arról, hogy a jogkivonat-szolgáltató létrejött-e a megfelelő értékekkel. A hozzáférés-vezérlés szolgáltatás konfigurációjának ellenőrzése. |Újrapróbálkozási segíthet bizonyos esetekben; újrapróbálkozási logika hozzáadása a kódot. |
| [Argumentum kivétel](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argumentum Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumentum az engedélyezett tartományon kívül esik](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Egy vagy több, a következő lépett fel:<br />A metódus számára megadott egy vagy több argumentumok érvénytelenek.<br /> Az URI számára megadott [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozása](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) egy vagy több szegmenst tartalmaz.<br />A megadott URI-séma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozása](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) érvénytelen. <br />A tulajdonság értéke nagyobb, mint 32 KB lehet. |Ellenőrizze a hívó kódot, és győződjön meg arról, hogy az argumentumok megfelelőek. |Nem nyújt az újra gombra. |
| [A kiszolgáló foglalt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Szolgáltatás állapota nem tudja feldolgozni a kérést most. |Az ügyfél Várjon egy ideig, majd próbálja megismételni a műveletet. |Az ügyfél egy adott időtartam után újra. Ha egy másik kivétel újrapróbálkozási eredményez, ellenőrizze, hogy a kivétel a újrapróbálási viselkedése. |
| [Túllépte a kvótát.](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Az üzenetküldési entitás elérte a megengedett maximális méretet. |Üzenetek fogadása az entitás vagy a alsorokon létrehozásához az entitásban levő terület. Lásd: [QuotaExceededException](#quotaexceededexception). |Újrapróbálkozási segíthet, ha üzenetek időközben eltávolították. |
| [Üzenet mérete meghaladta](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Üzenetadatokat meghaladja a 256 KB-os korlátozását. Vegye figyelembe, hogy a 256 KB-os korlát az összes üzenet mérete. Az összes üzenet mérete Rendszertulajdonságok és a Microsoft .NET-terhelés tartalmazhatnak. |Csökkentse a üzenetadatokat méretét, majd próbálja megismételni a műveletet. |Nem nyújt az újra gombra. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitás kvóta túl lett lépve.

A továbbító, ez a kivétel becsomagolja a [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), ami azt jelenti, hogy a Figyelők maximális számát ezen a végponton túl lett lépve. A jelzett a **MaximumListenersPerEndpoint** a kivételre vonatkozó üzenet értékét.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy egy felhasználó által kezdeményezett művelet a vártnál a művelet időkorlátja lejár. 

Ellenőrizze a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság. Szerezze meg ezt a korlátozást is okozhatnak a [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

A továbbító időtúllépési kivétel fordulhat elő, egy közvetítő küldő kapcsolat első megnyitásakor. Ehhez a kivételhez két gyakori oka is van:

*   A [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) értéke túl kicsi (Ha még által másodperc töredéke alatt) lehet.
* Lehet, hogy egy helyszíni továbbítási figyelőt nem válaszoló (vagy tűzfal szabályok kapcsolatos problémák figyelői kapcsolatokat fogadjon előforduló), és a [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) értéke kisebb, mint körülbelül 20 másodperc.

Példa:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Általános okok
A hiba gyakori okai két van:

*   **Helytelen konfiguráció**
    
    Előfordulhat, hogy a művelet időkorlátja túl kicsi a működési feltétel. A művelet időtúllépési értékét, az ügyfél SDK alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy az érték a kódban értéke túl kicsi. Vegye figyelembe, hogy CPU-használat és a hálózat állapotának hatással lehet a művelet befejezéséhez szükséges időt. Célszerű nem értékre való beállítására a művelet időtúllépési értékét egy nagyon kis.
*   **Szolgáltatások átmeneti hiba**

    Alkalmanként a továbbítási szolgáltatás tapasztalhat késést kérelmek feldolgozásához. Ez azért fordulhat elő, például nagy forgalom idején. Ilyen esetben újra a művelettel késleltetés után, amíg a művelet sikeres nem lesz. Ha ugyanazt a műveletet több próbálkozást követően továbbra is fennáll, ellenőrizze a [Azure szolgáltatás állapota hely](https://azure.microsoft.com/status/) annak ellenőrzéséhez, hogy a szolgáltatás-kimaradások számát ismertek.

## <a name="next-steps"></a>Következő lépések
* [Az Azure továbbító – gyakori kérdések](relay-faq.md)
* [Továbbító névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés az Azure és a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Ismerkedés az Azure és a csomópont](relay-hybrid-connections-node-get-started.md)

