---
title: "Intelligens Insights diagnosztika Teljesítménynapló - Azure SQL Database |} Microsoft Docs"
description: "Intelligens Insights biztosít az Azure SQL Database teljesítménnyel kapcsolatos problémák diagnosztika naplózása"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 358986f58c431aebfe7b41daa8c40ba641dc408a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Az intelligens Insights Azure SQL Database teljesítményét diagnosztika napló használata

Ezen a lapon megtudhatja hogyan használható az Azure SQL Database diagnosztika Teljesítménynapló által generált [intelligens Insights](sql-database-intelligent-insights.md), annak formátuma, és az adatokhoz tartozó az egyéni fejlesztői kell. A diagnosztika naplót elküldheti [Azure Naplóelemzés](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), vagy egy külső megoldás egyéni DevOps és jelentéskészítési lehetőségei képességek.

## <a name="log-header"></a>Napló fejléc

A diagnosztikai naplófájl intelligens Insights megállapítások kimeneti JSON szabványos formátumot használja. A pontos kategória fér hozzá egy intelligens Insights napló tulajdonsága a rögzített érték "SQLInsights".

A fejléc napló gyakori, és bemutatja a bejegyzés létrehozásának időbélyegzőjét (TimeGenerated) áll. Is tartalmaz egy erőforrás-azonosító (ResourceId), hogy az adott SQL-adatbázis, a bejegyzés vonatkozik hivatkozik. A kategória (kategória), a szintet () és a művelet neve (OperationName) fix tulajdonságait, amelyek értékét ne módosítsa. Utal, hogy a naplóbejegyzést tájékoztató, és hogy intelligens Insights (SQLInsights) származik.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Problémaazonosító és az érintett adatbázis

A probléma azonosítása tulajdonság (issueId_d) biztosítja az egyedi most megoldásáig teljesítményproblémák nyomon követése. Intelligens Insights minden probléma életciklus "Active", "Ellenőrzése" vagy "Kész" vonatkoznak. Állapot fázisokkal végig intelligens Insights több eseményrekordok rögzítheti a naplóban. Az egyes ezeket a bejegyzéseket a probléma azonosítószámát egyedi marad. Intelligens Insights nyomon követi a problémát a teljes életciklusukon keresztül, és egyet az hoz létre a diagnosztikai naplófájl 15 percenként.

Miután a teljesítménybeli problémát észlelt, és mindaddig, amíg tart, a probléma van a csoportban az állapot (status_s) tulajdonság "Aktív" jelentett. Észlelt probléma elhárítására, miután ellenőrizte, és jelentése szerint "Ellenőrzése", az állapot (status_s) tulajdonság alapján. Ha a probléma már nem található, az állapot (status_s) tulajdonság "Complete" probléma jelzi.

A probléma azonosítója, valamint a diagnosztikai naplófájl (intervalStartTime_t) kezdő- és a diagnosztika naplóban jelentett problémát kapcsolódik az adott esemény vége (intervalEndTme_t) időbélyeggel jelentést készít.

A rugalmas készlet (elasticPoolName_s) tulajdonság azt jelzi, hogy mely tartozik az adatbázis kapjon egy problémához rugalmas készlet. Ha az adatbázis nem egy rugalmas készlet része, ez a tulajdonság nem rendelkezik értékkel. Az adatbázist, amelyben egy problémát észlelt az adatbázis nevét (databaseName_s) tulajdonságban felfedett.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Észlelt problémák

A következő szakasz az intelligens Insights teljesítmény napló teljesítménnyel kapcsolatos problémákat talált keresztül beépített mesterséges eszközintelligencia tartalmazza. A tulajdonságokat a JSON-diagnosztikai naplófájl észlelési közzéteszik. Ezek az észlelések áll, milyen típusú egy problémához, a probléma, az érintett lekérdezések és a metrikák hatását. Az észlelések tulajdonságok is tartalmazhatnak, több teljesítménnyel kapcsolatos problémákat talált.

A következő észlelések tulajdonság struktúrájú jelentett észlelt teljesítménnyel kapcsolatos problémák:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Észlelhető teljesítmény minták és a rendszer outputted a diagnosztika naplóba az alábbi táblázatban szerepelnek.

### <a name="detection-category"></a>Észlelési kategória

A category (kategória) tulajdonság észlelhető teljesítmény minták kategória ismerteti. Lásd az alábbi táblázatban minden lehetséges kategória észlelhető teljesítmény minták. További információkért lásd: [intelligens, amelyen adatbázis teljesítménnyel kapcsolatos problémák elhárítása](sql-database-intelligent-insights-troubleshoot-performance.md).

Attól függően, hogy a teljesítménycsökkenés oka észlel, a részletek a diagnosztika a outputted naplófájl eltérőek lehetnek, ennek megfelelően.

| Észlelhető teljesítmény minták | Outputted részletei |
| :------------------- | ------------------- |
| Az erőforrás-korlátozások | <li>Érintett erőforrások</li><li>Lekérdezés kivonatok</li><li>Erőforrás-használat százalékos aránya</li> |
| Munkaterhelések növelése | <li>Lekérdezések, amelyek végrehajtása nőtt</li><li>A legnagyobb mértékben a terhelés növekedésével a lekérdezések kivonatok lekérdezése</li> |
| Memória | <li>Memóriakezelőt</li> |
| Zárolás | <li>Érintett lekérdezés kivonatok</li><li>Lekérdezés kivonatok blokkolása</li> |
| Nagyobb MAXDOP | <li>Lekérdezés kivonatok</li><li>CXP várakozási idő</li><li>Várakozási idő</li> |
| Pagelatch versengés | <li>A lekérdezések versengés, amely a kivonatok lekérdezése</li> |
| Hiányzó Index | <li>Lekérdezés kivonatok</li> |
| Új lekérdezés | <li>Az új lekérdezések lekérdezés kivonata</li> |
| Szokatlan várakozási statisztika | <li>Szokatlan várakozási típusok</li><li>Lekérdezés kivonatok</li><li>Lekérdezés várakozási idő</li> |
| A TempDB versengés | <li>A lekérdezések versengés, amely a kivonatok lekérdezése</li><li>Azokat a teljes adatbázis pagelatch versengés várakozási idő [%] lekérdezése</li> |
| A rugalmas készlet DTU kevés | <li>A rugalmas készlet</li><li>Felső DTU-igényes adatbázis</li><li>Készlet DTU felső fogyasztója használja százaléka</li> |
| Regressziós megtervezése | <li>Lekérdezés kivonatok</li><li>Jó terv azonosítók</li><li>Hibás terv azonosítók</li> |
| Adatbázis-alapú kötődő konfigurációs érték módosítása | <li>Az alapértelmezett értékeket képest adatbázishoz kötődő konfigurációs módosítások</li> |
| Lassú ügyfél | <li>Lekérdezés kivonatok</li><li>Várakozási idő</li> |
| Árképzési szint alacsonyabb szintre való visszalépést | <li>Szöveges értesítés</li> |

### <a name="impact"></a>Gyakorolt hatás

A hatás tulajdonság ismerteti a problémát, amelynek adatbázis hozzájárult mennyi észlelt viselkedés (hatás). Hatással van az 1 – 3, 3, a legmagasabb hozzájárulás, mérsékelt, 2 és 1 közé eső legalacsonyabb befizetendő. A hatás előfordulhat, hogy használható bemenetként egyéni riasztási automation, attól függően, hogy a saját igényeinek. A tulajdonság-lekérdezéseket érintett (QueryHashes) sorolja fel azokat a lekérdezés a kivonatok, amely egy adott észlelési is hatással volt.

### <a name="impacted-queries"></a>Érintett lekérdezések

Intelligens Insights napló a következő szakasz ismerteti a adott lekérdezések észlelt teljesítményével kapcsolatos problémákat is hatással volt. Ezt az információt a impact_s tulajdonság beágyazott objektumokat tömbként felfedett. A hatás tulajdonság entitásokat és a metrikák áll. Entitás egy adott lekérdezés hivatkozik (típus: lekérdezés). Az egyedi lekérdezés kivonatoló felfedett alatt a value (érték) tulajdonságot. Emellett egyes közzétett lekérdezések követi metrika és egy érték, amely jelzi a észlelt teljesítménybeli problémát.

A következő napló példában a lekérdezés a kivonat 0x9102EXZ4 lett észlelte, hogy egy nagyobb végrehajtásának időtartama (metrika: DurationIncreaseSeconds). 110 másodpercben azt jelzi, hogy ez a lekérdezés végrehajtásához már 110 másodperc. Több lekérdezés észlelhető, mert ez a szakasz adott napló több lekérdezés bejegyzések közé tartozik.

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

Mindegyik metrikát jelentett mértékegységét másodperc, számát és százalékos aránya a lehetséges értékek a a metrika (metrika) tulajdonság által biztosított. Egy mért metrika értékének jelentésekről a value (érték) tulajdonságot.

A DurationIncreaseSeconds tulajdonság másodpercben mértékegységet biztosít. A CriticalErrorCount mértékegységet egy hibaszám érték.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Legfelső szintű ok elemzés és fokozása javaslatok

Az intelligens Insights Teljesítménynapló utolsó részének a automatizált kiváltó okának elemzése a azonosított teljesítményének csökkenéséhez probléma vonatkozik. Az információ jelenik meg a legfelső szintű ok elemzés (rootCauseAnalysis_s) tulajdonságban szóhasználatára emberi mobilbarát hasonlítanak. Fokozása javaslatok szerepelnek a napló, ahol csak lehetséges.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Használhatja az intelligens Insights teljesítmény naplóra [Azure Naplóelemzés]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) vagy egy külső megoldás egyéni DevOps lehetőséget, és a jelentéskészítési képességek.

## <a name="next-steps"></a>Következő lépések
- További tudnivalók [intelligens Insights](sql-database-intelligent-insights.md) fogalmakat.
- Megtudhatja, hogyan [intelligens, amelyen az Azure SQL Database teljesítménnyel kapcsolatos problémák elhárítása](sql-database-intelligent-insights-troubleshoot-performance.md).
- Megtudhatja, hogyan [figyelje az Azure SQL Database az Azure SQL elemzés](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Megtudhatja, hogyan [gyűjtése és felhasználása az Azure-erőforrások naplóadatait](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



