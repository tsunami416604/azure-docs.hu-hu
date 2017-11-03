---
title: "Az Azure-figyelője (előzetes verzió) Azure továbbítási metrikák |} Microsoft Docs"
description: "Azure-figyelés használatával figyelheti az Azure-továbbító"
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2017
ms.author: sethm
ms.openlocfilehash: 3652e80c20c425570ba90a1f3ce7a3035762a34d
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Az Azure továbbítási metrikát a Azure Monitor (előzetes verzió)

Az Azure továbbítási metrikák lehetővé teszi az erőforrások az Azure-előfizetése állapotát. A metrikai adatok széles skáláját felmérheti a továbbítási erőforrásokat, nem csak a névterek szintjén, hanem az entitás szintjén általános állapotát. A statisztikai információk olyankor lehet fontos, mivel ezek segítségével figyelheti az Azure-továbbítási állapotát. Metrikák is hozzájárulhat a kiváltó okának elhárítása anélkül, hogy az Azure ügyfélszolgálatához.

Az Azure biztosít, egységes felhasználói felületek keresztüli különböző Azure-szolgáltatásokhoz. További információkért lásd: [figyelése a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md) és a [.NET metrikák beolvasása Azure figyelő](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

## <a name="access-metrics"></a>Hozzáférés metrikák

Az Azure hozzáférési metrikák több lehetőség is biztosít. Mindkét hozzáférési metrikák keresztül is a [Azure-portálon](https://portal.azure.com), vagy használja az Azure figyelő API-k (REST és .NET) és elemzési megoldások, például a felügyeleti csomag művelet és az Event Hubs. További információkért lásd: [Azure figyelő metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és érheti el az utolsó 30 napnyi adat. Ha szeretné megőrizni az adatokat egy hosszabb ideig, úgy archiválhatók metrikai adatok az Azure Storage-fiók. Ez úgy van konfigurálva a [diagnosztikai beállítások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) Azure-figyelőben.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés metrikákat a portálon

Adott idő alatt a figyelheti a metrikák a [Azure-portálon](https://portal.azure.com). A következő példa bemutatja, hogyan sikeres és a bejövő kérelmeket a fiók szintjén megtekintése:

![][1]

Metrikák közvetlenül a névtér keresztül is elérheti. Ehhez jelölje ki a névteret, és kattintson a **metrikák (Peview)**. 

A dimenziók támogató metrika szűrheti kell a kívánt dimenzió értékű.

## <a name="billing"></a>Számlázás

A metrikák Azure figyelőben használata jelenleg szabad, miközben a képen. Azonban további megoldások, amelyek a metrikai adatok betöltési használatakor, akkor előfordulhat, hogy számlázni ezek a megoldások. Például kell fizetni Azure Storage által archiválja metrikák egy Azure Storage-fiókhoz. Ha adatfolyam formájában a metrikai adatok az OMS Szolgáltatáshoz speciális elemzésekre szolgáló művelet felügyeleti csomag (OMS) által is kell fizetni.

A következő mérőszámokat adhat a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt is elavulttá több metrikákat. Ehhez szükség lehet, hogy frissítse a hivatkozásokat. A "elavult" kulcsszó jelölésű metrikák módosítástól fogva nem támogatott.

Minden értékeihez Azure figyelő percenként küldi. A részletességet határozza meg az az időtartam, amelynek metrikák értékek jelenjenek meg. A támogatott időtartam alatt, az összes Azure-továbbítási metrikát értéke 1 perc.

## <a name="connection-metrics"></a>Kapcsolati metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| ListenerConnections-sikerült (előzetes verzió) | A sikeres figyelő létesített kapcsolat száma Azure irányítja adott időszakon belül. <br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|ListenerConnections-ClientError (előzetes verzió)|Az ügyfél hibáinak száma a figyelő kapcsolatok adott időszakon belül.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|ListenerConnections-ServerError (előzetes verzió)|A kiszolgáló hibáinak száma a megadott időszakon át a figyelő kapcsolatok.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|SenderConnections-sikerült (előzetes verzió)|Egy adott időszakban létrehozott sikeres küldő kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|SenderConnections-ClientError (előzetes verzió)|Az ügyfél hibáinak száma a küldő kapcsolatok adott időszakon belül.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|SenderConnections-ServerError (előzetes verzió)|A kiszolgáló hibáinak száma a küldő kapcsolatok adott időszakon belül.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|ListenerConnections-TotalRequests (előzetes verzió)|Egy megadott időszakban figyelő kapcsolatok teljes száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|SenderConnections-TotalRequests (előzetes verzió)|A kapcsolat kérelmeire a küldők adott időszakon belül.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|ActiveConnections (előzetes verzió)|Egy adott időszakban az aktív kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|ActiveListeners (előzetes verzió)|Egy adott időszakban aktív figyelői száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|ListenerDisconnects (előzetes verzió)|Egy megadott időszakban leválasztott figyelői száma.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|SenderDisconnects (előzetes verzió)|A megadott időszakban leválasztott feladók száma.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="memory-usage-metrics"></a>A szoftverhasználati mérési adatok memória

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|BytesTransferred (előzetes verzió)|Adott időszakon belül átvitt bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Azure Relay Azure-figyelő a metrikák a következő dimenziókat támogatja. Dimenziók hozzáadása a metrikákat, nem kötelező megadni. Ha nem adja hozzá a dimenziók, metrikák meg van adva, a névterek szintjén. 

|Dimenzió neve|Leírás|
| ------------------- | ----------------- |
|entityName| Az Azure továbbító a névtér az üzenetküldési entitások támogatja.|

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




