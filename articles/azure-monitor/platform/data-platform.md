---
title: Azure Monitor adatplatform | Microsoft dokumentumok
description: Az Azure Monitor által gyűjtött adatok figyelése olyan metrikákra van leválasztva, amelyek könnyűek, és képesek közel valós idejű forgatókönyveket és naplókat támogatni, amelyeket speciális elemzéshez használnak.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a1b5859341237c1b177ee8deaf636a67f4824948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666547"
---
# <a name="azure-monitor-data-platform"></a>Az Azure Monitor adatplatformja

A felhőalapú és helyszíni szolgáltatásokra támaszkodó elosztott alkalmazásokat futtató összetett számítástechnikai környezetekben a megfigyelhetőség lehetővé tétele az elosztott rendszer minden rétegéről és összetevőjéről származó működési adatok gyűjtését igényli. Képesnek kell lennie arra, hogy mélyreható betekintést az adatokat, és konszolidálja azt egyetlen üvegtábla különböző perspektívák, hogy támogassa a számos érdekelt fél a szervezetben.

[Az Azure Monitor](../overview.md) különböző forrásokból származó adatokat gyűjt és összesít egy közös adatplatformba, ahol elemzésre, vizualizációra és riasztásra használható. Konzisztens élményt nyújt a több forrásból származó adatokon, amely mélyreható betekintést nyújt az összes figyelt erőforrásba, és még más szolgáltatásokból származó adatokkal is, amelyek az Azure Monitorban tárolják az adataikat.


![Azure Monitor – áttekintés](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Megfigyelhetőségi adatok az Azure Monitorban
Metrikák, naplók, és elosztott nyomkövetések gyakran nevezik a három pillére megfigyelhetőság. Ezek azok a különböző típusú adatok, amelyeket egy felügyeleti eszköznek össze kell gyűjtenie és elemeznie kell, hogy a figyelt rendszer megfelelő enermitást biztosítson. A megfigyelhetőség a több pillérből származó adatok korrelálásával és a figyelt erőforrások teljes készletére vonatkozó adatok összesítésével érhető el. Mivel az Azure Monitor több forrásból származó adatokat tárol együtt, az adatok egy közös eszközkészlet használatával korrelálhatók és elemezhetők. Emellett több Azure-előfizetés és bérlő közötti adatokat is korrelál, az egyéb szolgáltatások adatainak üzemeltetése mellett.

Az Azure-erőforrások jelentős mennyiségű figyelési adatot hoznak létre. Az Azure Monitor ezeket az adatokat a más forrásokból származó adatok figyelésével együtt egy Metrika vagy a Naplók platformra összesíti. Mindegyik adott figyelési forgatókönyvekhez van optimalizálva, és mindegyik különböző funkciókat támogat az Azure Monitorban. Az olyan funkciók, mint az adatelemzés, a vizualizációk vagy a riasztások megkövetelik a különbségek megértését, hogy a szükséges forgatókönyvet a leghatékonyabb anameddig megvalósíthassa. Insights az Azure Monitorban, például [az Application Insights](../app/app-insights-overview.md) vagy az Azure Monitor virtuális gépek [elemzési](../insights/vminsights-overview.md) eszközök, amelyek lehetővé teszik, hogy összpontosítson az adott figyelési forgatókönyv anélkül, hogy a két típusú adatok közötti különbségek et. 


### <a name="metrics"></a>Mérőszámok
[A metrikák](data-platform-metrics.md) olyan numerikus értékek, amelyek egy adott időpontban a rendszer bizonyos aspektusait írják le. Ezeket rendszeres időközönként gyűjtik, és időbélyeggel, névvel, értékkel és egy vagy több meghatározó címkével azonosítják. A metrikák különböző algoritmusok használatával összesíthetők, összehasonlítva más mérőszámokkal, és elemezhetők az időbeli trendek. 

Metrikák az Azure Monitor ban tárolja egy idősorozat-adatbázis, amely optimalizált időbélyegzett adatok elemzésére. Ez különösen alkalmassá teszi a mérőszámokat a problémák riasztására és gyors észlelésére. Meg tudják mondani, hogyan teljesít a rendszer, de általában össze kell kapcsolni a naplókat a problémák kiváltó okának azonosításához.

A metrikák interaktív elemzésre érhetők el az [Azure Metrics Explorer](../platform/metrics-getting-started.md)segítségével az Azure Portalon. Hozzáadhatók egy [Azure-irányítópulthoz](../learn/tutorial-app-dashboards.md) a vizualizációhoz más adatokkal kombinálva, és közel valós idejű [riasztáshoz](alerts-metric.md)használhatók.

További információ az Azure Monitor metrikákról, beleértve azok adatforrásait az [Azure Monitor Metrikák ban.](data-platform-metrics.md)

### <a name="logs"></a>Naplók
[A naplók](data-platform-logs.md) a rendszeren belül bekövetkezett események. Különböző típusú adatokat tartalmazhatnak, és lehetnek strukturált vagy szabad formátumú szövegek időbélyeggel. Előfordulhat, hogy szórványosan jönnek létre, mivel a környezetben lévő események naplóbejegyzéseket hoznak létre, és a nagy terhelés alatt lévő rendszer általában több naplókötetet hoz létre.

Az Azure Monitor naplói az [Azure Data Explorer](/azure/data-explorer/) en alapuló Log Analytics-munkaterületen tárolódnak, amely hatékony elemzőmotort és gazdag [lekérdezési nyelvet](/azure/kusto/query/)biztosít. A naplók általában elegendő információt nyújtanak ahhoz, hogy teljes kontextust biztosítsanak az azonosított problémáról, és értékesek a problémák gyökéresetének azonosításához.

> [!NOTE]
> Fontos megkülönböztetni az Azure Monitor naplók és az Azure-beli naplóadatok forrásait. Például az Azure-beli előfizetési szintű események egy [tevékenységnaplóba](platform-logs-overview.md) kerülnek, amelyet az Azure Monitor menüből tekinthet meg. A legtöbb erőforrás működési adatokat ír egy [erőforrásnaplóba,](platform-logs-overview.md) amelyet különböző helyekre továbbíthat. Az Azure Monitor Logs egy naplóadat-platform, amely tevékenységnaplókat és erőforrásnaplókat, valamint más figyelési adatokat gyűjt, hogy mélyreható elemzést nyújtson az erőforrások teljes készletében.


 A [naplólekérdezések](../log-query/log-query-overview.md) interaktívan dolgozhat nak a [Log Analytics](../log-query/portals.md) az Azure Portalon, vagy adja hozzá az eredményeket egy [Azure-irányítópulton](../learn/tutorial-app-dashboards.md) vizualizációs más adatokkal együtt. [Naplóriasztásokat](alerts-log.md) is létrehozhat, amelyek egy ütemezési lekérdezés eredménye alapján riasztást váltanak ki.

További információ az Azure Monitor-naplókról, beleértve azok adatforrásait az [Azure Monitor naplóiban.](data-platform-logs.md)

### <a name="distributed-traces"></a>Elosztott nyomkövetések
A nyomkövetések olyan kapcsolódó események sorozatai, amelyek egy elosztott rendszeren keresztül követik a felhasználói kérést. Ezeket az alkalmazáskód és a különböző tranzakciók teljesítményének meghatározására használható. Míg a naplók gyakran jön létre az elosztott rendszer egyes összetevői, a nyomkövetés méri az alkalmazás működését és teljesítményét az összetevők teljes készletében.

Az Azure Monitor ban elosztott nyomkövetés engedélyezve van az [Application Insights SDK-val,](../app/distributed-tracing.md)és a nyomkövetési adatokat az Application Insights által gyűjtött egyéb alkalmazásnapló-adatokkal együtt tárolják. Ez elérhetővé teszi ugyanazokat az elemzési eszközöket, mint más naplóadatok, beleértve a naplólekérdezéseket, irányítópultokat és riasztásokat.

További információ a terjesztett nyomkövetésről [a Mi az elosztott nyomkövetés?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Az Azure Monitor metrikáinak és naplóinak összehasonlítása

Az alábbi táblázat összehasonlítja a metrikák és naplók az Azure Monitorban.

| Attribútum  | Mérőszámok | Naplók |
|:---|:---|:---|
| Előnyök | Könnyű és képes közel valós idejű forgatókönyvek, például riasztás. Ideális a problémák gyors felismeréséhez. | Gazdag lekérdezési nyelvvel elemezve. Ideális a mély elemzéshez és a kiváltó ok azonosításához. |
| Adatok | Csak numerikus értékek | Szöveg vagy numerikus adatok |
| Struktúra | A tulajdonságok szabványos készlete, beleértve a mintavételi időt, a figyelt erőforrást, egy numerikus értéket. Egyes mutatók több dimenziót tartalmaznak a további meghatározáshoz. | A tulajdonságok egyedi készlete a napló típusától függően. |
| Gyűjtemény | Rendszeres időközönként összegyűjtve. | Gyűjthető szórványosan, mivel az események egy rekord létrehozását váltják ki. |
| Megtekintés az Azure Portalon | Metrikaböngésző | Log Analytics |
| Az adatforrások közé tartoznak a következők: | Az Azure-erőforrásokból gyűjtött platformmutatók.<br>Az Application Insights által figyelt alkalmazások.<br>Alkalmazás vagy API által definiált egyéni. | Alkalmazás- és erőforrásnaplók.<br>Felügyeleti megoldások.<br>Ügynökök és virtuálisgép-bővítmények.<br>Alkalmazáskérések és kivételek.<br>Az Azure Biztonsági Központ.<br>Adatgyűjtő API. |

## <a name="collect-monitoring-data"></a>Nyomon követési adatok gyűjtése
Az Azure Monitor különböző [adatforrásai](data-sources.md) a Log Analytics-munkaterületre (Naplók) vagy az Azure Monitor metrika-adatbázisába (metrikák) vagy mindkettőre írnak. Egyes források közvetlenül ezekbe az adattárakba írnak, míg mások más helyre, például az Azure storage-ba írhatnak, és bizonyos konfigurációra van szükség a naplók vagy metrikák feltöltéséhez. 

[Az Azure Monitor](data-platform-metrics.md) és az Azure Monitor [naplói](data-platform-logs.md) az egyes típusokat felújuló különböző adatforrások listáját olvassa el.


## <a name="stream-data-to-external-systems"></a>Adatok streamelése külső rendszerekre
Amellett, hogy az Azure-ban a figyelési adatok elemzésére szolgáló eszközök használatával rendelkezik, előfordulhat, hogy szükség van arra, hogy továbbítsa azokat egy külső eszközre, például egy biztonsági információ- és eseménykezelési (SIEM) termékre. Ez a továbbítás általában közvetlenül a figyelt erőforrásokból történik az [Azure Event Hubs-on](/azure/event-hubs/)keresztül. Egyes források beállíthatók, hogy az adatokat közvetlenül egy eseményközpontba küldje, miközben egy másik folyamat, például egy logikai alkalmazás segítségével lekérheti a szükséges adatokat. A részleteket lásd: [Stream Azure figyelési adatok egy eseményközpont ba egy külső eszköz általi felhasználás.](stream-monitoring-data-event-hubs.md)



## <a name="next-steps"></a>További lépések

- További információ [a Metrikákról az Azure Monitorban.](data-platform-metrics.md)
- További információ [a Naplók az Azure Monitorban.](data-platform-logs.md)
- Ismerje meg a [figyelési adatok at az](data-sources.md) Azure-ban rendelkezésre álló különböző erőforrások.
