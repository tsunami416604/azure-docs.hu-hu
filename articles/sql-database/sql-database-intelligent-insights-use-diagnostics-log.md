---
title: Intelligent Insights teljesítmény diagnosztikai naplója – Azure SQL Database | Microsoft Docs
description: Intelligent Insights diagnosztikai naplót biztosít Azure SQL Database teljesítménnyel kapcsolatos problémákról
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: c25d37a4d1695ab94cc0667a13e36e4da640e12a
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262149"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>A Intelligent Insights Azure SQL Database teljesítmény-diagnosztikai naplójának használata

Ez az oldal arról nyújt tájékoztatást, hogyan használható a [Intelligent Insights](sql-database-intelligent-insights.md)által generált Azure SQL Database Performance Diagnostics-napló, annak formátuma és az egyéni fejlesztési igényekhez tartozó adatok. Ezt a diagnosztikai naplót elküldheti [Azure monitor naplókba](../azure-monitor/insights/azure-sql.md), az [Azure Event Hubsba](../azure-monitor/platform/resource-logs-stream-event-hubs.md), az [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage)-ba vagy egy harmadik féltől származó megoldásra az egyéni DevOps-riasztási és jelentéskészítési funkciókhoz.

## <a name="log-header"></a>Napló fejléce

A diagnosztikai napló JSON szabványos formátumot használ a Intelligent Insights eredményeinek kimenetére. Egy Intelligent Insights napló elérésének pontos category tulajdonsága a "SQLInsights" rögzített érték.

A napló fejléce gyakori, és a bejegyzés létrehozásakor megjelenő időbélyegző (TimeGenerated) áll. Ide tartozik egy erőforrás-azonosító (ResourceId) is, amely arra az adott SQL Database hivatkozik, amely a bejegyzésre vonatkozik. A kategória (kategória), a szint (szint) és a művelet neve (OperationName) olyan rögzített tulajdonságok, amelyek értékei nem változnak. Azt jelzik, hogy a naplóbejegyzés tájékoztatási és Intelligent Insights (SQLInsights) származik.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>A probléma azonosítója és az érintett adatbázis

A probléma azonosítása tulajdonság (issueId_d) lehetővé teszi a teljesítménnyel kapcsolatos problémák egyedi nyomon követését a megoldásig. Ugyanannak a hibának a naplózási jelentéskészítési állapotában több esemény rekord is ugyanazzal a probléma-AZONOSÍTÓval fog osztozni.

A probléma-AZONOSÍTÓval együtt a diagnosztikai napló a diagnosztikai naplóban jelentett problémával kapcsolatos indítási (intervalStartTime_t) és befejezési (intervalEndTme_t) időbélyegzőket jelenti.

A rugalmas készlet (elasticPoolName_s) tulajdonság azt jelzi, hogy melyik rugalmas készlethez tartozik az adatbázis, amelynek a problémája van. Ha az adatbázis nem része egy rugalmas készletnek, ennek a tulajdonságnak nincs értéke. Az adatbázis neve (databaseName_s) tulajdonsága azt az adatbázist adja meg, amelyben a rendszer hibát észlelt.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Észlelt problémák

A Intelligent Insights Teljesítménynapló következő szakasza olyan teljesítménnyel kapcsolatos problémákat tartalmaz, amelyeket a rendszer a beépített mesterséges intelligenciával észlelt. Az észleléseket a rendszer a JSON diagnosztikai naplójában lévő tulajdonságok között adja meg. Ezek az észlelések a probléma kategóriáját, a probléma következményeit, az érintett lekérdezéseket és a metrikákat tartalmazzák. Az észlelési tulajdonságok több, észlelt teljesítménnyel kapcsolatos problémát is tartalmazhatnak.

A rendszer az észlelt teljesítménnyel kapcsolatos problémákat a következő észlelési tulajdonságok struktúrájával fogja jelenteni:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

A észlelhető teljesítményi minták és a diagnosztikai naplóhoz tartozó részletek a következő táblázatban találhatók.

### <a name="detection-category"></a>Észlelési kategória

A category (kategória) tulajdonság a észlelhető teljesítményi minták kategóriáját írja le. A észlelhető teljesítményi minták összes lehetséges kategóriáját a következő táblázat tartalmazza. További információ: [az adatbázis teljesítményével kapcsolatos hibák elhárítása Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Az észlelt teljesítménnyel kapcsolatos probléma függvényében a diagnosztikai naplófájlban található részletek ennek megfelelően eltérnek.

| Észlelhető teljesítményi minták | További részletek |
| :------------------- | ------------------- |
| Erőforrás-korlátok elérése | <li>Érintett erőforrások</li><li>Lekérdezések kivonatai</li><li>Erőforrás-felhasználási százalék</li> |
| Munkaterhelés növekedése | <li>A végrehajtást megnövelő lekérdezések száma</li><li>Lekérdezési kivonatok lekérése a legnagyobb mértékben hozzájárul a munkaterhelés növeléséhez</li> |
| Memória nyomása | <li>Memória-jegyző</li> |
| Zárolás | <li>Érintett lekérdezési kivonatok</li><li>Lekérdezési kivonatok blokkolása</li> |
| Megnövekedett MAXDOP | <li>Lekérdezések kivonatai</li><li>CXP várakozási ideje</li><li>Várakozási idő</li> |
| Pagelatch-tartalom | <li>A lekérdezést kiváltó lekérdezések kivonatai</li> |
| Hiányzó index | <li>Lekérdezések kivonatai</li> |
| Új lekérdezés | <li>Az új lekérdezések lekérdezési kivonata</li> |
| Szokatlan várakozási statisztika | <li>Szokatlan várakozási típusok</li><li>Lekérdezések kivonatai</li><li>Lekérdezési várakozási idő</li> |
| TempDB-tartalom | <li>A lekérdezést kiváltó lekérdezések kivonatai</li><li>Lekérdezés a teljes adatbázis pagelatch-tartalmának várakozási ideje [%]</li> |
| A rugalmas készlet DTU hiánya | <li>Rugalmas készlet</li><li>Leggyakoribb DTU adatbázis</li><li>A felső fogyasztó által használt készlet DTU százaléka</li> |
| Regressziós terv | <li>Lekérdezések kivonatai</li><li>Helyes csomag azonosítói</li><li>Rossz terv azonosítói</li> |
| Adatbázis hatókörű konfigurációs értékének változása | <li>Adatbázis-hatókörű konfiguráció módosításai az alapértelmezett értékekhez képest</li> |
| Lassú ügyfél | <li>Lekérdezések kivonatai</li><li>Várakozási idő</li> |
| Díjszabási szintek visszalépése | <li>Szöveges értesítés</li> |

### <a name="impact"></a>Hatás

A hatás (hatás) tulajdonság azt írja le, hogy az észlelt viselkedés mekkora mértékben járult hozzá az adatbázis által felmerülő problémákhoz. A hatások 1 és 3 közötti tartományba esnek, a legmagasabb hozzájárulással, 2 – közepes és 1 a legalacsonyabb hozzájárulással. A hatás értéke az egyéni riasztások automatizálásának bemenete lehet, az adott igényektől függően. Az érintett tulajdonság-lekérdezések (QueryHashes) megadják az adott észlelés által érintett lekérdezési kivonatok listáját.

### <a name="impacted-queries"></a>Érintett lekérdezések

A Intelligent Insights napló következő szakasza információt nyújt az észlelt teljesítménnyel kapcsolatos problémák által érintett lekérdezésekről. Ezeket az információkat a impact_s tulajdonságban beágyazott objektumok tömbje kell közzétenni. Az Impact tulajdonság entitásokból és mérőszámokból áll. Az entitások egy adott lekérdezésre vonatkoznak (típus: Lekérdezés). Az egyedi lekérdezési kivonatot az érték (érték) tulajdonság alatt kell közzétenni. Emellett a közzétett lekérdezések mindegyikét egy metrika és egy érték követi, amely egy észlelt teljesítménnyel kapcsolatos problémát jelez.

A következő példában a rendszer a kivonat 0x9102EXZ4 rendelkező lekérdezést észlelte a végrehajtás megnövekedésének időtartamával (metrikus: DurationIncreaseSeconds). Az 110 másodperces érték azt jelzi, hogy az adott lekérdezés végrehajtása hosszabb volt, mint 110 másodperc. Mivel több lekérdezés is észlelhető, az adott napló szakasz több lekérdezési bejegyzést is tartalmazhat.

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

Az egyes jelentett mérőszámok mértékegysége a metrika (metrikus) tulajdonság alatt van megadva, a másodperc, a szám és a százalék lehetséges értékeivel. A mért metrika értékét a Value (Value) tulajdonságban kell jelenteni.

A DurationIncreaseSeconds tulajdonság másodpercek alatt biztosítja a mértékegységet. A CriticalErrorCount mértékegysége egy szám, amely a hibák számát jelöli.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Alapvető okok elemzése és tökéletesítésére vonatkozó javaslatok

A Intelligent Insights teljesítmény naplójának utolsó része az azonosított teljesítmény-romlási probléma automatizált kiváltó okának elemzésére vonatkozik. Az információk az alapvető okok elemzése (rootCauseAnalysis_s) tulajdonságban az emberi-barát szóhasználatát jelennek meg. A fejlesztéssel kapcsolatos javaslatok a naplóban találhatók, ahol lehetséges.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

A Intelligent Insights Teljesítménynapló [Azure monitor naplókkal]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) vagy harmadik féltől származó megoldással az egyéni DevOps-riasztásokhoz és jelentéskészítési funkciókhoz.

## <a name="next-steps"></a>További lépések
- Ismerkedjen meg [Intelligent Insights](sql-database-intelligent-insights.md) fogalmakkal.
- Ismerje meg, hogyan lehet [elhárítani a Intelligent Insightsokkal kapcsolatos teljesítményproblémák Azure SQL Database](sql-database-intelligent-insights-troubleshoot-performance.md).
- Megtudhatja, hogyan [figyelheti Azure SQL Database a Azure SQL Analytics használatával](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Ismerje meg, hogyan [gyűjthet és használhat adatokat az Azure-erőforrásokból](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



