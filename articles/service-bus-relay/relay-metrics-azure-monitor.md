---
title: Azure Relay metrikák a Azure Monitorban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Monitor az Azure Relay állapotának figyelésére.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273116"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Monitor Azure Relay metrikák 
Azure Relay mérőszámok az Azure-előfizetésében lévő erőforrások állapotát adják meg. A metrikai adatok gazdag készletével felmérhető a továbbítási erőforrások általános állapota, nem csak a névtér szintjén, hanem az entitás szintjén is. Ezek a statisztikák fontosak lehetnek, mivel segítenek a Azure Relay állapotának figyelésében. Metrikák is segít kiváltó problémák elhárítása anélkül, hogy forduljon az Azure ügyfélszolgálatához.

Az Azure Monitor egységes felhasználói felületet biztosít a különböző Azure-szolgáltatások figyelésére. További információkért lásd: [Microsoft Azure figyelése](../monitoring-and-diagnostics/monitoring-overview.md) és a [Azure monitor metrikáinak beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a githubon .net-minta használatával.

> [!IMPORTANT]
> Ez a cikk csak a Azure Relay Hibrid kapcsolatok szolgáltatására vonatkozik, nem a WCF Relay. 

## <a name="access-metrics"></a>Access-metrikák

Az Azure Monitor hozzáférés metrikák több módot is biztosít. A mérőszámokat a [Azure Portalon](https://portal.azure.com)keresztül érheti el, vagy használhatja a Azure monitor API-kat (REST és .net) és az elemzési megoldásokat, például az Operation Management Suite-t és a Event Hubst. További információ: [Azure monitor által összegyűjtött adatok figyelése](../azure-monitor/platform/data-platform.md).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és elérheti az utolsó 30 nap adatait. Ha szeretne egy hosszabb ideig megőrizni az adatokat, úgy archiválhatók metrikák adatai egy Azure Storage-fiókhoz. Ez a Azure Monitor [diagnosztikai beállításaiban](../azure-monitor/platform/diagnostic-settings.md) van konfigurálva.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés mérőszámok portálon

A metrikák a [Azure Portalban](https://portal.azure.com)is megfigyelhetők. Az alábbi példa bemutatja, hogyan sikeres kérések és a fiók szintjén a bejövő kérelmek megtekintése:

![][1]

Metrikák a névtér keresztül közvetlenül is elérheti. Ehhez válassza ki a névteret, majd kattintson a * * mérőszámok * * elemre. 

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
| ListenerConnections-Success  | Egy adott időszakban Azure Relay sikeres figyelő-kapcsolatok száma. <br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|ListenerConnections-ClientError |A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|ListenerConnections-ServerError |A figyelő kapcsolatainak száma a megadott időszakban.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|SenderConnections-Success |A megadott időszakban sikeres küldő kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|SenderConnections-ClientError |Az ügyfél hibáinak száma a küldő kapcsolatain a megadott időszakban.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|SenderConnections-ServerError |A megadott időszakon belül a küldő kapcsolaton futó kiszolgálói hibák száma.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|ListenerConnections-TotalRequests |A figyelő kapcsolatainak teljes száma egy adott időszakban.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|SenderConnections-TotalRequests |A küldők által megadott időszakon keresztül kezdeményezett kapcsolati kérelmek.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|ActiveConnections |Az aktív kapcsolatok száma. Ez az érték egy időponthoz tartozó érték.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|ActiveListeners |Az aktív figyelők száma. Ez az érték egy időponthoz tartozó érték.<br/><br/> Egység: száma <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|Bontásai |A leválasztott figyelők száma egy adott időszakban.<br/><br/> Egység: bájt <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|
|SenderDisconnects |A leválasztott küldők száma egy adott időszakban.<br/><br/> Egység: bájt <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|

## <a name="memory-usage-metrics"></a>Memóriahasználat mérőszámai

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|BytesTransferred |A megadott időszakon át továbbított bájtok száma.<br/><br/> Egység: bájt <br/> Összesítés típusa: összes <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

A Azure Relay a Azure Monitor metrikáinak következő dimenzióit támogatja. A metrikák dimenziók hozzáadása nem kötelező. Ha nem adja hozzá a dimenziók, a metrikák a névterek szintjén vannak megadva. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|EntityName| Azure Relay támogatja az üzenetküldési entitásokat a névtérben.|

## <a name="next-steps"></a>Következő lépések

Lásd az [Azure monitoring áttekintése](../monitoring-and-diagnostics/monitoring-overview.md)című témakört.

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




