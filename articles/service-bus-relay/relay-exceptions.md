---
title: Az Azure Relay-kivételek és azok megoldását |} A Microsoft Docs
description: Az Azure Relay-kivételek és oldhatja meg őket érdekében javasolt műveletek listája.
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
ms.openlocfilehash: 0fe30fe95e77adceaa5013f89206b08daf2a58a2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702095"
---
# <a name="azure-relay-exceptions"></a>Az Azure Relay-kivételek

Ez a cikk néhány kivétel, előfordulhat, hogy létrejött az Azure Relay API-k által sorolja fel. Ez a hivatkozás változhatnak, ezért vissza frissítések keresése.

## <a name="exception-categories"></a>Kivétel kategóriák

A Relay-API-k készítése a kivételeket, amelyek előfordulhat, hogy a következő kategóriákba esnek. Is megtalálhatók javasolt műveleteket, amelyeket a kivételek megoldása érdekében.

*   **Kódolási hiba felhasználói**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Általános művelet**: próbálja meg kijavítani a kódot, folytatás előtt.
*   **A telepítő vagy konfigurációs hiba**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Általános művelet**: tekintse át a konfigurációt. Szükség esetén módosítsa a konfigurációt.
*   **Átmeneti kivételek**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Általános művelet**: értesítse a felhasználókat, vagy próbálja megismételni a műveletet.
*   **Kivételek**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Általános művelet**: jellemző a kivétel típusát. A táblázatban a következő szakaszban találja. 

## <a name="exception-types"></a>Kivételtípusok

A következő táblázat felsorolja az üzenetkezelési kivételtípusok és okaik. Azt is elvégezhető, a kivételek megoldása érdekében javasolt műveletek megjegyzések.

| **Kivétel típusa** | **Leírás** | **Javasolt művelet** | **Vegye figyelembe az automatikus vagy azonnali újrapróbálkozás** |
| --- | --- | --- | --- |
| [Időtúllépés](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kért művelet, amely szabályozza a meghatározott időn belül nem válaszol a kiszolgáló [így időtúllépés történt](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Előfordulhat, hogy a kiszolgáló befejezte a kért művelet. Ez akkor fordulhat elő, hálózati vagy más infrastruktúra késedelem miatt. |Ellenőrizze a rendszer állapotát, a konzisztencia, és próbálkozzon újra, ha szükséges. Lásd: [TimeoutException](#timeoutexception). |Bizonyos esetekben; segíthet újrapróbálkozási újrapróbálkozási logika hozzáadása a kódot. |
| [Érvénytelen művelet](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgáló vagy a szolgáltatás belül. Tekintse meg a részleteket a kivétel üzenetét. |Ellenőrizze a kód és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvénytelen. |Újrapróbálkozás nem segít. |
| [Művelet megszakítva](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Kísérlet történik egy olyan objektum, amely már zárt, megszakadt, vagy eldobva művelet meghívásához. Bizonyos ritkán előforduló esetekben a környezeti tranzakció már eldobva. |Ellenőrizze a kódot, és ellenőrizze, hogy nem lép működésbe műveletek nA smazaném objektu. |Újrapróbálkozás nem segít. |
| [Jogosulatlan hozzáférés](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektumot nem sikerült megszerezni a jogkivonatot, a jogkivonat érvénytelen vagy a jogkivonat nem tartalmazza a jogcímeket, a művelet végrehajtásához szükséges. |Győződjön meg arról, hogy a jogkivonat-szolgáltató jön létre a megfelelő értékekkel. Ellenőrizze a konfigurációt, az Access Control Service szolgáltatást. |Bizonyos esetekben; segíthet újrapróbálkozási újrapróbálkozási logika hozzáadása a kódot. |
| [Argumentumkivétel](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Null argumentum](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumentum az engedélyezett tartományon kívül esik](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Egy vagy több, a következő lépett fel:<br />A metódushoz megadott egy vagy több argumentumok érvénytelenek.<br /> Az URI-t megadott [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozás](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) tartalmazza az egy vagy több elérési út.<br />A megadott URI-séma [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozás](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) je neplatná. <br />A tulajdonság értéke 32 KB-nál nagyobb. |Ellenőrizze a hívó kód, és ellenőrizze, hogy az argumentumok megfelelőek. |Újrapróbálkozás nem segít. |
| [A kiszolgáló foglalt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Szolgáltatás nem tudja feldolgozni a kérelmet jelenleg. |Az ügyfél Várjon egy ideig, majd próbálja megismételni a műveletet. |Az ügyfél a előfordulhat, hogy egy adott időtartam után próbálkozzon újra. Ha egy másik kivételt újrapróbálkozási eredményez, ellenőrizze, hogy a kivétel újrapróbálkozási viselkedését. |
| [Kvóta túllépve](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Az üzenetkezelési entitás elérte a maximális megengedett méretét. |Üzenetek fogadása az entitást vagy a alvárólista létrehozásához az entitásban lévő terület. Lásd: [QuotaExceededException](#quotaexceededexception). |Újrapróbálkozási segíthet, ha üzenetek időközben eltávolították. |
| [Üzenet mérete túllépve](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Üzenet hasznos adattartalmából meghaladja a 256 KB-os korlátját. Vegye figyelembe, hogy a 256 KB-os korlátot a teljes üzenetmérete. A teljes üzenetmérete Rendszertulajdonságok és bármely Microsoft .NET-terhelést is tartalmazhat. |Az üzenet hasznos adattartalmából méretének csökkentése, majd próbálja megismételni a műveletet. |Újrapróbálkozás nem segít. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) azt jelzi, hogy egy adott entitáshoz tartozó kvóta túl lett lépve.

A Relay, ehhez a kivételhez burkolja az [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), ami azt jelenti, hogy a Figyelők maximális számát ezen a végponton túl lett lépve. Ez jelzi a a **MaximumListenersPerEndpoint** a kivételre vonatkozó üzenet értékét.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy egy felhasználó által kezdeményezett művelet a vártnál tovább tart, mint a művelet időkorlátja. 

Ellenőrizze a [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) tulajdonság. Ez a korlát elérése is okozhatnak a [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

A Relay időtúllépési kivételeket egy közvetítő küldő kapcsolat első megnyitásakor jelenhet meg. Ehhez a kivételhez két gyakori oka is van:

*   A [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) értéke túl kicsi (Ha még a másodperc tört) lehet.
* Lehet, hogy nem válaszol egy helyszíni relay-figyelő (vagy tűzfal szabályok problémák, amelyek tiltják a figyelők az új ügyfélgépi kapcsolatokat fogad, léphetnek fel), és a [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) értéke kisebb, mint körülbelül 20 másodperc.

Példa:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Gyakori okai
Ez a hiba a két gyakori oka is van:

*   **Helytelen konfiguráció**
    
    Lehet, hogy a művelet időkorlátja túl kicsi a műveleti feltétel. A művelet időkorlátja, az ügyfél-SDK az alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy a kódban értéke valami túl kicsi. Vegye figyelembe, hogy CPU-használat és a hálózat állapota hatással lehet egy művelet végrehajtásához szükséges időt. Célszerű nagyon kis értéket adjon meg a művelet időkorlátja.
*   **Átmeneti szolgáltatáshiba**

    Néha előfordul a továbbítási szolgáltatás tapasztalhat az késleltetések kérelmek feldolgozása. Ez akkor fordulhat elő, például a nagy forgalom időszakokban. Ha ez történik, próbálja megismételni a műveletet, némi késéssel, amíg a művelet sikeres. Ha ugyanazt a műveletet tett kísérletet követően továbbra is fennáll, ellenőrizze a [Azure-szolgáltatások](https://azure.microsoft.com/status/) annak ellenőrzéséhez, hogy szolgáltatáskimaradások ismertek.

## <a name="next-steps"></a>További lépések
* [Az Azure Relay – gyakori kérdések](relay-faq.md)
* [Relay-névtér létrehozása](relay-create-namespace-portal.md)
* [Ismerkedés az Azure Relay- és .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Ismerkedés az Azure Relay és csomópont](relay-hybrid-connections-node-get-started.md)

