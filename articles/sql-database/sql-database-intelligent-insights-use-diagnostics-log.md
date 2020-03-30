---
title: Az Intelligens elemzések teljesítménydiagnosztikai naplója
description: Az Intelligent Insights diagnosztikai naplót biztosít az Azure SQL Database teljesítményproblémáiról
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bb62b087451140261aee7aaa2fab0de14ea36283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209449"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Az Intelligent Insights Azure SQL Database teljesítménydiagnosztikai naplójának használata

Ez a lap az Azure SQL Database teljesítménydiagnosztikai naplójának [az Intelligent Insights](sql-database-intelligent-insights.md), annak formátuma és az egyéni fejlesztési igényekhez való használata által létrehozott használatával kapcsolatos információkat tartalmaz. Ezt a diagnosztikai naplót elküldheti az [Azure Monitor-naplókba,](../azure-monitor/insights/azure-sql.md) [az Azure Event Hubs-ba, az](../azure-monitor/platform/resource-logs-stream-event-hubs.md)Azure [Storage-ba](sql-database-metrics-diag-logging.md#stream-into-azure-storage)vagy egy külső megoldásba az egyéni DevOps-riasztási és jelentéskészítési képességekhez.

## <a name="log-header"></a>Naplófejléc

A diagnosztikai napló JSON szabványos formátumot használ az Intelligens insights-eredmények kimenetéhez. Az Intelligent Insights-napló elérésének pontos kategóriatulajdonsága az "SQLInsights" rögzített érték.

A napló fejléce gyakori, és a bejegyzés létrehozásának időpontjára vonatkozó időbélyegzőből (TimeGenerated) áll. Tartalmaz egy erőforrás-azonosítót (ResourceId), amely arra az SQL-adatbázisra hivatkozik, amelyre a bejegyzés vonatkozik. A kategória (kategória), a szint (Szint) és a művelet neve (OperationName) olyan rögzített tulajdonságok, amelyek értékei nem változnak. Azt jelzik, hogy a naplóbejegyzés információs, és hogy az Intelligent Insights (SQLInsights) származik.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>A problémaazonosító és az adatbázis érintett

A problémaazonosító tulajdonság (issueId_d) lehetővé teszi a teljesítményproblémák egyedi nyomon követését a megoldásig. Ugyanazon probléma naplójelentési állapotában több eseményrekord is ugyanazt a problémaazonosítót fogja megosztani.

A problémaazonosítóval együtt a diagnosztikai napló jelenti az adott esemény kezdő (intervalStartTime_t) és befejezési (intervalEndTme_t) időbélyegeit a diagnosztikai naplóban jelentett problémához kapcsolódóan.

A rugalmas készlet (elasticPoolName_s) tulajdonság azt jelzi, hogy a problémával rendelkező adatbázis melyik rugalmas készlethez tartozik. Ha az adatbázis nem része egy rugalmas készlet, ez a tulajdonság nincs értéke. Az adatbázis, amelyben problémát észlelt, az adatbázis név (databaseName_s) tulajdonságában kerül nyilvánosságra.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Észlelt problémák

Az Intelligent Insights teljesítménynapló következő szakasza olyan teljesítményproblémákat tartalmaz, amelyeket a beépített mesterséges intelligencia észlel. Az észlelések a JSON diagnosztikai naplóban található tulajdonságokban kerülnek nyilvánosságra. Ezek az észlelések a probléma kategóriájából, a probléma hatásából, az érintett lekérdezésekből és a metrikákból állnak. Az észlelések tulajdonságai több észlelt teljesítményproblémát is tartalmazhatnak.

Az észlelt teljesítményproblémák a következő észlelések tulajdonságstruktúrával jelennek meg:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

A diagnosztikai naplóba kimenetelét észlelhető teljesítményminták és a diagnosztikai naplóba kimenetelét tartalmazó részleteket az alábbi táblázat tartalmazza.

### <a name="detection-category"></a>Észlelési kategória

A kategória (kategória) tulajdonság a kimutatható teljesítményminták kategóriáját írja le. Az alábbi táblázatban a kimutatható teljesítményminták összes lehetséges kategóriáját láthatja. További információt az [Intelligens elemzésekkel kapcsolatos adatbázis-teljesítményproblémák elhárítása című](sql-database-intelligent-insights-troubleshoot-performance.md)témakörben talál.

Az észlelt teljesítményproblémától függően a diagnosztikai naplófájlban lelett adatok ennek megfelelően eltérnek.

| Észlelhető teljesítményminták | Kimeneti részletek |
| :------------------- | ------------------- |
| Erőforráskorlátok elérése | <li>Érintett erőforrások</li><li>Lekérdezési kibékülések</li><li>Erőforrás-felhasználás százaléka</li> |
| Munkaterhelés növelése | <li>Azon lekérdezések száma, amelyek végrehajtása nőtt</li><li>Lekérdezési lekérdezési lekérdezési kihágásai a legnagyobb mértékben járulhozzá a munkaterhelés növeléséhez</li> |
| Memóriaterhelés | <li>Memória ügyintéző</li> |
| Zárolás | <li>Érintett lekérdezési kihaék</li><li>Lekérdezéskikérdezések letiltása</li> |
| Megnövekedett MAXDOP | <li>Lekérdezési kibékülések</li><li>CXP várakozási idő</li><li>Várakozási idők</li> |
| Pagelatch versengés | <li>Versengést okozó lekérdezések lekérdezési kikérdezése</li> |
| Hiányzó index | <li>Lekérdezési kibékülések</li> |
| Új lekérdezés | <li>Az új lekérdezések kivonatának lekérdezése</li> |
| Szokatlan várakozási statisztika | <li>Szokatlan várakozási típusok</li><li>Lekérdezési kibékülések</li><li>Lekérdezés várakozási ideje</li> |
| TempDB-versengés | <li>Versengést okozó lekérdezések lekérdezési kikérdezése</li><li>Lekérdezési hozzárendelés a teljes adatbázis-lapzár versengés várakozási idejéhez [%]</li> |
| Rugalmas medence DTU hiány | <li>Rugalmas készlet</li><li>A legjobb DTU-fogyasztó adatbázis</li><li>A felső fogyasztó által használt pool DTU százaléka</li> |
| Regresszió simára tervezése | <li>Lekérdezési kibékülések</li><li>Jó tervazonosítók</li><li>Hibás csomagazonosítók</li> |
| Adatbázis-hatókörrel konfigurált érték módosítása | <li>Adatbázis-hatókörű konfigurációmódosításai az alapértelmezett értékekhez képest</li> |
| Lassú ügyfél | <li>Lekérdezési kibékülések</li><li>Várakozási idők</li> |
| Tarifacsomag visszaminősítése | <li>Szöveges értesítés</li> |

### <a name="impact"></a>Hatás

A hatás (hatás) tulajdonság azt írja le, hogy az észlelt viselkedés mennyiben járult hozzá az adatbázis problémájához. A hatások 1-től 3-ig terjednek, a legnagyobb hozzájárulás 3, a 2 mérsékelt, az 1 pedig a legalacsonyabb. A hatás érték lehet használni, mint egy bemeneti egyéni riasztási automatizálás, attól függően, hogy az adott igényeket. Az érintett tulajdonságlekérdezések (QueryHashes) az adott észlelés által érintett lekérdezési kihágások listáját adják meg.

### <a name="impacted-queries"></a>Érintett lekérdezések

Az Intelligent Insights-napló következő szakasza az észlelt teljesítményproblémák által érintett lekérdezésekkel kapcsolatos információkat tartalmazza. Ez az információ a impact_s tulajdonságba ágyazott objektumok tömbjeként kerül nyilvánosságra. Az impact tulajdonság entitásokból és metrikákból áll. Az entitások egy adott lekérdezésre hivatkoznak (Típus: Lekérdezés). Az egyedi lekérdezési kivonat az érték (Érték) tulajdonság alatt kerül nyilvánosságra. Emellett minden egyes közzétett lekérdezések követi egy metrika és egy érték, amely jelzi az észlelt teljesítmény probléma.

A következő naplópéldában a 0x9102EXZ4 kivonattal rendelkező lekérdezés a végrehajtás megnövekedett időtartamával (Metric: DurationIncreaseSeconds) észlelte. A 110 másodperces érték azt jelzi, hogy ez a lekérdezés végrehajtása 110 másodperccel tovább tartott. Mivel több lekérdezés is észlelhető, ez a naplószakasz több lekérdezési bejegyzést is tartalmazhat.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Mérőszámok

Az egyes jelentett metrikák mértékegysége a metrika (metrika) tulajdonság alatt található, a másodpercek, a szám és a százalék lehetséges értékeivel. A mért metrika értékét az érték (érték) tulajdonság jelenti.

Az DurationIncreaseSeconds tulajdonság másodpercek alatt biztosítja a mértékegységet. A CriticalErrorCount mértékegység egy hibaszámot képviselő szám.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Kiváltó ok elemzési és javítási javaslatok

Az Intelligent Insights teljesítménynapló utolsó része az azonosított teljesítménycsökkenési probléma automatikus kiváltó okelemzésére vonatkozik. Az információ emberbarát szóhasználatban jelenik meg a gyökérok elemzése (rootCauseAnalysis_s) tulajdonságban. A javítási javaslatok lehetőség szerint a naplóban is szerepelnek.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Használhatja az Intelligent Insights teljesítménynaplóaz [Azure Monitor naplók]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) vagy egy harmadik féltől származó megoldás egyéni DevOps riasztási és jelentéskészítési képességek.

## <a name="next-steps"></a>További lépések

- Ismerje meg [az Intelligens elemzési](sql-database-intelligent-insights.md) fogalmakat.
- Ismerje meg, hogyan [háríthatja el az Azure SQL Database teljesítményproblémáit az Intelligent Insights segítségével.](sql-database-intelligent-insights-troubleshoot-performance.md)
- Ismerje meg, hogyan figyelheti az [Azure SQL Database-t az Azure SQL Analytics használatával.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)
- Ismerje meg, hogyan [gyűjthet és használható fel naplóadatokat az Azure-erőforrásokból.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
