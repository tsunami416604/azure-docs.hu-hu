---
title: Azure Monitor Azure Relay metrikák (előzetes verzió) | Microsoft Docs
description: Az Azure monitoring használata a Azure Relay figyeléséhez
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: 9933b7857580f29cbb6a54ebc82d4e7207456ad4
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261794"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Monitor Azure Relay metrikák (előzetes verzió)
Azure Relay mérőszámok az Azure-előfizetésében lévő erőforrások állapotát adják meg. A metrikai adatok gazdag készletével felmérhető a továbbítási erőforrások általános állapota, nem csak a névtér szintjén, hanem az entitás szintjén is. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Azure Relay állapotának figyelésében. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [a Microsoft Azure figyelés](../monitoring-and-diagnostics/monitoring-overview.md) és a [lekérése az Azure Monitor-metrikák .NET-tel](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

> [!IMPORTANT]
> Ez a cikk csak a Azure Relay Hibrid kapcsolatok szolgáltatására vonatkozik, nem a WCF Relay. 

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. Mindkét hozzáférési metrikák keresztül is a [az Azure portal](https://portal.azure.com), vagy használja az Azure Monitor API-k (REST és .NET) és elemzési megoldásokkal, például az Operation Management Suite és az Event Hubs. További információkért lásd: [figyelési adatokat gyűjtött az Azure Monitor](../azure-monitor/platform/data-platform.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ennek a konfigurációja a [diagnosztikai beállítások](../azure-monitor/platform/diagnostic-settings.md) az Azure monitorban.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

Az idő függvényében segítségével figyelheti a mérőszámokat a [az Azure portal](https://portal.azure.com). Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, majd kattintson a **metrikák (előzetes verzió)** elemre. 

A dimenziókat támogató metrikák esetében a kívánt dimenzió értékkel kell szűrnie.

## <a name="billing"></a>Számlázás

Az Azure monitorban mérőszámok segítségével a jelenleg előzetes verzióban ingyenes. Azonban használ további megoldásokat, amelyek fogadni a mérőszámadatokat, akkor előfordulhat, hogy után kell díjat ezek a megoldások által. Ha például számítjuk fel az Azure Storage által archiválja mérőszámok az Azure Storage-fiók. A Azure Monitor naplók számlázása akkor is történik, ha a metrikák adatait a speciális elemzéshez Azure Monitor naplókba.

Az alábbi mérőszámok segítségével a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt rendszer kivezetése több metrikát. Ehhez szükség lehet, hogy a hivatkozások frissítését. Metrikák, a "elavult" kulcsszó megjelölve a jövőben nem támogatja.

Az összes értékeihez kapnak az Azure Monitor percenként. Az idő részletessége határozza meg az időintervallum, amelynek metrikák jelennek meg. Az összes Azure Relay-metrika támogatott időintervalluma 1 perc.

## <a name="connection-metrics"></a>Kapcsolati metrika

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| Figyelőkapcsolatokra – sikeres (előzetes verzió) | Egy adott időszakban Azure Relay sikeres figyelő-kapcsolatok száma. <br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Figyelőkapcsolatokra-vonatkozó ügyfélhibái (előzetes verzió)|A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Figyelőkapcsolatokra-ServerError (előzetes verzió)|A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Feladói kapcsolatokra – sikeres (előzetes verzió)|A megadott időszakban sikeres küldő kapcsolatok száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Feladói kapcsolatokra-vonatkozó ügyfélhibái (előzetes verzió)|Az ügyfél hibáinak száma a küldő kapcsolatain a megadott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Feladói kapcsolatokra-ServerError (előzetes verzió)|A megadott időszakon belül a küldő kapcsolaton futó kiszolgálói hibák száma.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Figyelőkapcsolatokra-TotalRequests (előzetes verzió)|A figyelő kapcsolatainak teljes száma egy adott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Feladói kapcsolatokra-TotalRequests (előzetes verzió)|A küldők által megadott időszakon keresztül kezdeményezett kapcsolati kérelmek.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Aktív kapcsolatai (előzetes verzió)|Az aktív kapcsolatok száma egy adott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|ActiveListeners (előzetes verzió)|Az aktív figyelők száma egy adott időszakban.<br/><br/> Egység Count <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|Bontásai (előzetes verzió)|A leválasztott figyelők száma egy adott időszakban.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|
|SenderDisconnects (előzetes verzió)|A leválasztott küldők száma egy adott időszakban.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="memory-usage-metrics"></a>Memóriahasználat mérőszámai

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|BytesTransferred (előzetes verzió)|A megadott időszakon át továbbított bájtok száma.<br/><br/> Egység Bájt <br/> Összesítés típusa: Összes <br/> Dimenzió EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

A Azure Relay a Azure Monitor metrikáinak következő dimenzióit támogatja. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| Azure Relay támogatja az üzenetküldési entitásokat a névtérben.|

## <a name="next-steps"></a>További lépések

Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




