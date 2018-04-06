---
title: Az Azure Event Hubs metrikát a Azure Monitor (előzetes verzió) |} Microsoft Docs
description: Azure-figyelés használatával figyelheti az Event Hubs
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: sethm
ms.openlocfilehash: 8ca00b234c00bfeb52a5b601e8780d56a0732dd9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Az Azure Event Hubs metrikát a Azure Monitor (előzetes verzió)

Event Hubs metrikák lehetővé teszi az Event Hubs erőforrások az Azure-előfizetése állapotát. A metrikai adatok széles skáláját felmérheti az eseményközpontok nem csak a névterek szintjén, hanem az entitás szintjén általános állapotát. A statisztikai információk olyankor lehet fontos, mivel ezek segítségével figyelheti az eseményközpontok állapotának. Metrikák is hozzájárulhat a kiváltó okának elhárítása anélkül, hogy az Azure ügyfélszolgálatához.

Az Azure biztosít, egységes felhasználói felületek keresztüli különböző Azure-szolgáltatásokhoz. További információkért lásd: [figyelése a Microsoft Azure-ban](../monitoring-and-diagnostics/monitoring-overview.md) és a [.NET metrikák beolvasása Azure figyelő](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon.

## <a name="access-metrics"></a>Hozzáférés metrikák

Az Azure hozzáférési metrikák több lehetőség is biztosít. Mindkét hozzáférési metrikák keresztül is a [Azure-portálon](https://portal.azure.com), vagy használja az Azure figyelő API-k (REST és .NET) és elemzési megoldások, például a felügyeleti csomag művelet és az Event Hubs. További információkért lásd: [Azure figyelő metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Alapértelmezés szerint engedélyezve vannak a metrikákat, és érheti el az utolsó 30 napnyi adat. Ha szeretné megőrizni az adatokat egy hosszabb ideig, úgy archiválhatók metrikai adatok az Azure Storage-fiók. Ez úgy van konfigurálva a [diagnosztikai beállítások](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) Azure-figyelőben.

## <a name="access-metrics-in-the-portal"></a>Hozzáférés metrikákat a portálon

Adott idő alatt a figyelheti a metrikák a [Azure-portálon](https://portal.azure.com). A következő példa bemutatja, hogyan sikeres és a bejövő kérelmeket a fiók szintjén megtekintése:

![][1]

Metrikák közvetlenül a névtér keresztül is elérheti. Ehhez jelölje ki a névteret, és kattintson a **metrikák (Peview)**. Az event hubs hatókörébe szűrt metrikák megjelenítéséhez jelölje ki az eseményközpont, és kattintson **metrikák (előzetes verzió)**.

A dimenziók támogató metrika a kívánt dimenzió értékű a következő példában látható módon kell szűrése:

![][2]

## <a name="billing"></a>Számlázás

A metrikák Azure figyelőben használata jelenleg szabad, miközben a képen. Azonban további megoldások, amelyek a metrikai adatok betöltési használatakor, akkor előfordulhat, hogy számlázni ezek a megoldások. Például kell fizetni Azure Storage által archiválja metrikák egy Azure Storage-fiókhoz. Is meg számlázása az Azure-ban, ha adatfolyam formájában a speciális elemzésekre szolgáló metrikák Naplóelemzési adatokat.

A következő mérőszámokat adhat a szolgáltatás állapotának áttekintése. 

> [!NOTE]
> Más néven áthelyezett azt is elavulttá több metrikákat. Ehhez szükség lehet, hogy frissítse a hivatkozásokat. A "elavult" kulcsszó jelölésű metrikák módosítástól fogva nem támogatott.

Minden értékeihez Azure figyelő percenként küldi. A részletességet határozza meg az az időtartam, amelynek metrikák értékek jelenjenek meg. Minden Event Hubs metrikák támogatott időközének értéke 1 perc.

## <a name="request-metrics"></a>Kérelem metrikák

Megjeleníti az adatok és felügyeleti műveletek kérelmek számát.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
| A bejövő kérések (előzetes verzió) | A megadott időszakon át az Azure Event Hubs szolgáltatás felé intézett kérések száma. <br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName |
| Sikeres kérések (előzetes verzió)   | A sikeres kérelmek száma az Azure Event Hubs szolgáltatás az adott időszakon belül. <br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName |
| Kiszolgálóhibákat (előzetes verzió) | A megadott időszakon át az Azure Event Hubs szolgáltatás a bekövetkezett hiba miatt nem feldolgozott kérelmek száma. <br/><br/>Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName |
|Felhasználó hibákat (előzetes verzió)|A megadott időszakban felhasználói hibák miatt nem feldolgozott kérelmek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Szabályozottan halmozott kérelmek (előzetes verzió)|Az átviteli egység használati túllépése miatt volt halmozódni kérelmek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kvóta túllépve hibák (előzetes verzió)|A kérések száma meghaladja a rendelkezésre álló kvótát. Lásd: [Ez a cikk](event-hubs-quotas.md) további információ az Event Hubs kvótákat.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="throughput-metrics"></a>Átviteli sebességre vonatkozó mérőszámok

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Szabályozottan halmozott kérelmek (előzetes verzió)|Az átviteli egység használati túllépése miatt volt halmozódni kérelmek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="message-metrics"></a>Üzenet metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|A bejövő üzenetek (előzetes verzió)|Események vagy adott időszakon belül az Event Hubs küldött üzenetek száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kimenő üzenetek (előzetes verzió)|Az események vagy üzenetek száma lekért Event Hubs egy adott időszakban.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Bejövő bájtok (előzetes verzió)|A megadott időszakon át az Azure Event Hubs szolgáltatásnak küldött bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kimenő bájtok (előzetes verzió)|Az Azure Event Hubs szolgáltatásból beolvasott bájtok száma adott időszakon belül.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="connection-metrics"></a>Kapcsolati metrikák

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|ActiveConnections (előzetes verzió)|Egy névtér, valamint egy entitás aktív kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kapcsolatok Opened (előzetes verzió)|A nyitott kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Kapcsolatok lezárva (előzetes verzió)|Lezárt kapcsolatok száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs rögzítése metrikák

Ha engedélyezi a rögzítése funkció az eseményközpontok a hatékonyan nyomon követheti a metrikákat Event Hubs rögzítése. A következő mérőszámokat írják le, az engedélyezett rögzítésével is figyelheti.

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|Rögzítheti a várakozó (előzetes verzió)|Még a választott cél rögzítéshez bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Rögzített üzenetek (előzetes verzió)|Üzenetek vagy adott időszakon belül a választott cél rögzített események száma.<br/><br/> Egység: száma <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|
|Rögzített bájtok (előzetes verzió)|A megadott időszakon át a választott cél rögzített bájtok száma.<br/><br/> Egység: bájtok <br/> Összesítési típusát: teljes <br/> Dimenzió: EntityName|

## <a name="metrics-dimensions"></a>Metrikák dimenziók

Az Azure Event Hubs Azure a figyelő a metrikák a következő dimenziók támogatja. Dimenziók hozzáadása a metrikákat, nem kötelező megadni. Ha nem adja hozzá a dimenziók, metrikák meg van adva, a névterek szintjén. 

| Metrika neve | Leírás |
| ------------------- | ----------------- |
|entityName| Az Event Hubs az event hub entitásokat a wsrmp támogatja.|

## <a name="next-steps"></a>További lépések

* Tekintse meg a [Azure Figyelés áttekintése](../monitoring-and-diagnostics/monitoring-overview.md).
* [.NET Azure figyelő metrikák beolvasása](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) mintát a Githubon. 

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



