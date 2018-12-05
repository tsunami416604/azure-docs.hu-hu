---
title: Intelligent Insights diagnosztikai teljesítménynaplóban – Azure SQL Database |} A Microsoft Docs
description: Intelligent Insights biztosít egy diagnosztikai napló, az Azure SQL Database teljesítménnyel kapcsolatos problémák
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/04/2018
ms.openlocfilehash: 2809dd45042e41c8337ecddccc76ec4e16d7cb8b
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52887695"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Az Intelligent Insights az Azure SQL Database teljesítményét diagnosztikai napló használata

Ez az oldal nyújt információkat által létrehozott Azure SQL Database teljesítményét diagnosztikai napló használatával [Intelligent Insights](sql-database-intelligent-insights.md), annak formátum, és az adatok esetében az egyéni fejlesztői kell tartalmaz. Elküldheti a diagnosztikai naplót a [Azure Log Analytics](../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage), vagy egy külső megoldás, riasztási és jelentéskészítési egyéni fejlesztők és üzemeltetők számára képességek.

## <a name="log-header"></a>Napló-fejléc

A diagnosztikai napló intelligens elemzési eredmények kimeneti JSON szabványos formátumot használja. Az Intelligent Insights napló eléréséhez pontos kategória tulajdonság a rögzített érték "SQLInsights".

A napló a fejléc gyakori, és az időbélyeg (TimeGenerated), amely megjeleníti a bejegyzés létrehozásakor áll. Egy erőforrás-azonosítója (erőforrás-azonosító), amely az adott SQL-adatbázis vonatkozik, a bejegyzés vonatkozik is tartalmaz. A kategória (kategória), a szintet () és a művelet neve (OperationName) rögzítettek tulajdonságok, amelyek értékét ne módosítsa. Ezek azt jelzik, hogy a naplóbejegyzést tájékoztató és intelligens elemzési (SQLInsights) származnak.

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Probléma azonosítója és az érintett adatbázis

A probléma azonosítása tulajdonság (issueId_d) biztosít a teljesítménnyel kapcsolatos problémák megoldásáig egyedileg követésének. A naplóban, ugyanez a probléma állapotát reporting több eseményrekordok osztani, ugyanezzel az azonosítóval probléma.

A probléma azonosítója, valamint a diagnosztikai napló a kezdő (intervalStartTime_t) és befejezési (intervalEndTme_t) időbélyegeket az adott esemény, a diagnosztikai naplóban jelentett probléma kapcsolatos jelentések.

A rugalmas készlet (elasticPoolName_s) tulajdonság azt jelzi, hogy melyik rugalmas készlet tartozik az adatbázis egy problémához. Ha az adatbázis egy rugalmas készlet része nem, ez a tulajdonság nem tartozik érték. Az adatbázis, amelyben egy problémát észlelt az adatbázis nevét (databaseName_s) tulajdonságban felfedett.

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Észlelt problémák

A következő szakaszban az Intelligent Insights teljesítménynaplóban, teljesítménnyel kapcsolatos problémák – beépített mesterséges intelligenciával észlelt tartalmazza. Észlelések közzéteszik a tulajdonságokat a JSON-diagnosztikai naplót. Ezek az észlelések kategóriát adta meg a problémát, a probléma, az érintett lekérdezések és a metrikák hatásának állnak. Az észlelések tulajdonságok tartalmazhatja több teljesítményproblémát, amelyekkel az észlelt.

A következő észlelésekből tulajdonság struktúrával jelentett észlelt teljesítménnyel kapcsolatos problémák:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

Cserélhető eszközként észlelhetőnek teljesítmény minták és a részletek, amelyek a rendszer használt kimeneti adattípus a diagnosztikai napló, az alábbi táblázatban szerepelnek.

### <a name="detection-category"></a>Észlelési kategória

A category (kategória) tulajdonság cserélhető eszközként észlelhetőnek teljesítmény minták kategóriáját ismerteti. Tekintse meg a következő táblázat tartalmazza az összes lehetséges kategóriák cserélhető eszközként észlelhetőnek teljesítmény minták. További információkért lásd: [adatbázis teljesítményproblémák megoldásában az Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Attól függően, a teljesítménybeli problémát észlel, a részletek a diagnosztika a használt kimeneti adattípus naplófájl ennek megfelelően eltérő.

| Cserélhető eszközként észlelhetőnek teljesítmény minták | Használt kimeneti adattípus részletei |
| :------------------- | ------------------- |
| Ért el erőforráskorlátok | <li>Érintett erőforrások</li><li>Lekérdezés kivonatok</li><li>Erőforrás-használat százalékos aránya</li> |
| Számítási feladatok növekedése | <li>Amelyek végrehajtása nőtt-lekérdezések száma</li><li>Lekérdezés kivonatai a legnagyobb mértékben a számítási feladatok növekedése lekérdezéseket.</li> |
| Rendelkezésre álló memória mennyisége | <li>Memóriakezelőt</li> |
| Zárolás | <li>Érintett lekérdezés kivonatok</li><li>Lekérdezés kivonatok blokkolása</li> |
| Nagyobb MAXDOP | <li>Lekérdezés kivonatok</li><li>CXP várakozási időt</li><li>Várakozási idő</li> |
| Pagelatch versengés | <li>A versengés okozó lekérdezések kivonatok lekérdezése</li> |
| Hiányzó Index | <li>Lekérdezés kivonatok</li> |
| Új lekérdezés | <li>Az új lekérdezések lekérdezés kivonata</li> |
| Szokatlan várakozási statisztika | <li>Szokatlan várakozási típusok</li><li>Lekérdezés kivonatok</li><li>Lekérdezés várakozási időt</li> |
| A TempDB versengés | <li>A versengés okozó lekérdezések kivonatok lekérdezése</li><li>Lekérdezés megnevezése, az általános adatbázis pagelatch versengés várakozási idő: [%]</li> |
| Rugalmas készlet dtu-k hiánya | <li>Rugalmas készlet</li><li>Felső DTU-igényes adatbázis</li><li>A felső fogyasztók által használt DTU készlet százaléka</li> |
| Regresszió megtervezése | <li>Lekérdezés kivonatok</li><li>Jó terv azonosítók</li><li>Hibás terv azonosítók</li> |
| Adatbázis-specifikus konfigurációs érték módosítása | <li>Az alapértelmezett értékeket képest adatbázishoz kötődő konfigurációs módosítások</li> |
| Lassú ügyféloldali | <li>Lekérdezés kivonatok</li><li>Várakozási idő</li> |
| Díjszabási szint alacsonyabb szintű | <li>Szöveges értesítés</li> |

### <a name="impact"></a>Hatás

A hatás (gyakorolt hatás) a tulajdonság azt mutatja, hogy mekkora észlelt viselkedés járult hozzá, amelyek egy adatbázist tapasztalja a problémát. Hatással van az 1-3, 3, a legmagasabb hozzájárulást, közepes, mint 2 és 1 közé eső legalacsonyabb befizetendő. Az ütközési értéket az egyéni riasztási automation igényektől használhatók bemenetként. A tulajdonság-lekérdezéseket érintett (QueryHashes) Itt adhatja meg, a lekérdezés, amely egy adott észlelési érintette kivonatokat.

### <a name="impacted-queries"></a>Érintett lekérdezések

A következő szakaszban az Intelligent Insights napló adott lekérdezések érintette észlelt teljesítményével kapcsolatos problémákat ismerteti. Ezek az információk nyilvánosságra impact_s tulajdonság a beágyazott objektumok tömbjeként. A hatás tulajdonság entitások és a metrikák áll. Entitások hivatkozni egy adott lekérdezés (típus: lekérdezés). Az egyedi lekérdezési kivonat felfedett value (érték) tulajdonsága alapján. Emellett minden egyes közzétett lekérdezések követ egy metrika- és egy értéket, amely jelzi a észlelt teljesítménybeli problémát.

A következő naplófájl-példában a lekérdezés a kivonat 0x9102EXZ4 észlelte, hogy-végrehajtás megnövekedett időtartama (metrika: DurationIncreaseSeconds). 110 másodperc értékét azt jelzi, hogy ez a lekérdezés végrehajtásához szükséges már 110 másodperc. Több lekérdezés észlelhető, mert az adott szakasz tartalmazhat több lekérdezés bejegyzést.

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

A mértékegység meghatározásáért jelentett mindegyik metrikát a lehetséges értékek a másodpercek számát és százalékos példáira a metrika (metrikus) tulajdonság alapján. A value (érték) tulajdonság jelenti, egy mért mérőszám értéke.

A DurationIncreaseSeconds tulajdonság biztosítja a mértékegységet másodpercek alatt. CriticalErrorCount mértékegység meghatározásáért egy szám, amely egy hibák száma jelenti.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Legfelső szintű OK elemzési és javítására vonatkozó javaslatok

Az Intelligent Insights teljesítménynaplóban utolsó része az automatizált kiváltó okok elemzése, a teljesítménycsökkenés azonosított teljesítményprobléma vonatkozik. Az információ emberi mobilbarát szóhasználatára hasonlítanak a legfelső szintű okok elemzése (rootCauseAnalysis_s) tulajdonságban jelenik meg. Teljesítményjavítási javaslatokkal szerepelnek a napló, ahol csak lehetséges.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Használhatja az Intelligent Insights teljesítménynaplóban a [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) vagy egy külső megoldás egyéni DevOps, riasztási és jelentéskészítési képességgel.

## <a name="next-steps"></a>További lépések
- Ismerje meg [Intelligent Insights](sql-database-intelligent-insights.md) fogalmakat.
- Ismerje meg, hogyan [Intelligent Insights az Azure SQL Database teljesítménnyel kapcsolatos problémáinak elhárítása](sql-database-intelligent-insights-troubleshoot-performance.md).
- Ismerje meg, hogyan [Azure SQL Database monitorozása az Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Ismerje meg, hogyan [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



