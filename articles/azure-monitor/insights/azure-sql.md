---
title: Azure SQL Analytics megoldás a Azure Monitorban | Microsoft Docs
description: Azure SQL Analytics megoldás segítségével kezelheti Azure SQL-adatbázisait
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 12/04/2019
ms.reviewer: carlrab
ms.openlocfilehash: b54783f29de731613a5f3c9c5d9d3b805b2d0115
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819526"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Azure SQL Database figyelése Azure SQL Analytics használatával (előzetes verzió)

![Azure SQL Analytics szimbólum](./media/azure-sql/azure-sql-symbol.png)

A Azure SQL Analytics egy fejlett felhőalapú figyelési megoldás, amely az Azure SQL Database-adatbázisok, a rugalmas készletek és a felügyelt példányok teljesítményének figyelésére, illetve több előfizetésre kiterjedő, egyetlen ablaktáblán keresztül. Beépített intelligencia segítségével gyűjti össze és vizualizálja az Azure SQL Database fontos teljesítmény-mérőszámait a teljesítménnyel kapcsolatos hibák elhárításához.

A megoldással összegyűjtött mérőszámok használatával egyéni figyelési szabályokat és riasztásokat hozhat létre. A megoldás segítségével azonosíthatja a problémákat az alkalmazás-verem minden rétegében. Az Azure diagnosztikai metrikákat és Azure Monitor nézeteket használ, hogy az összes Azure SQL-adatbázisra, rugalmas készletre és adatbázisra vonatkozóan adatokat nyújtson a felügyelt példányokban egyetlen Log Analytics munkaterületen. Azure Monitor a strukturált és strukturálatlan adatok összegyűjtését, összekapcsolását és megjelenítését teszi lehetővé.

A Azure SQL Analytics megoldás használatáról és a tipikus használati forgatókönyvekről a beágyazott videóban talál további információt:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Összekapcsolt források

A Azure SQL Analytics egy Felhőbeli figyelési megoldás, amely támogatja az Azure SQL Database-adatbázisok diagnosztikai telemetria folyamatos átvitelét: egyetlen, készletezett és felügyelt példányok adatbázisai. Mivel a megoldás nem használ ügynököket Azure Monitorhoz való kapcsolódásra, a megoldás nem támogatja a helyszíni vagy virtuális gépeken üzemeltetett SQL Server figyelését, lásd az alábbi kompatibilitási táblázatot.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Diagnosztikai beállítások](../platform/diagnostic-settings.md) | **Igen** | Az Azure metrika és a naplózási adatok közvetlenül az Azure-ban érkeznek Azure Monitor naplókra. |
| [Azure Storage-fiók](../platform/collect-azure-metrics-logs.md) | Nem | Azure Monitor nem olvassa be a Storage-fiók adatait. |
| [Windows-ügynökök](../platform/agent-windows.md) | Nem | A megoldás nem használja a közvetlen Windows-ügynököket. |
| [Linux-ügynökök](../learn/quick-collect-linux-computer.md) | Nem | A megoldás nem használja a közvetlen Linux-ügynököket. |
| [System Center Operations Manager felügyeleti csoport](../platform/om-agents.md) | Nem | A megoldás nem használja közvetlen kapcsolódást a Operations Manager ügynöktől a Azure Monitorhoz. |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics beállítások

Az alábbi táblázat a Azure SQL Analytics irányítópult két verziójának támogatott beállításait ismerteti, egyet az Azure SQL Database-hez és a rugalmas készletekhez, a másikat pedig a felügyelt példányok számára.

| Azure SQL Analytics lehetőség | Leírás | SQL Database és rugalmas készletek támogatása | Felügyelt példányok támogatása |
| --- | ------- | ----- | ----- |
| Erőforrás típus szerint | Az összes figyelt erőforrást megszámoló perspektíva. | Igen | Igen |
| Elemzés | Hierarchikus részletezést biztosít a Intelligent Insightsba a teljesítményig. | Igen | Igen |
| Hibák | Hierarchikus részletezést biztosít az adatbázisokon történt SQL-hibákhoz. | Igen | Igen |
| Időtúllépések | Hierarchikus részletezést biztosít az adatbázisokon történt SQL-időtúllépésekhez. | Igen | Nem |
| Tiltások | Hierarchikus részletezést biztosít az adatbázisokon történt SQL-blokkokhoz. | Igen | Nem |
| Adatbázis-várakozások | Hierarchikus részletezést biztosít az SQL WAIT statisztikái számára az adatbázis szintjén. A várakozási idő összegzését és a várakozási idő várakozási idejét foglalja magában. |Igen | Nem |
| Lekérdezés időtartama | Hierarchikus részletezést biztosít a lekérdezés végrehajtási statisztikái, például a lekérdezési időtartam, a CPU-használat, az adatio-használat, a log IO használata során. | Igen | Igen |
| Lekérdezési várakozások | Hierarchikus részletezést biztosít a lekérdezési várakozási statisztikákra a várakozási kategóriánként. | Igen | Igen |

## <a name="configuration"></a>Konfiguráció
A Azure SQL Analytics (előzetes verzió) megoldásnak az Log Analytics-munkaterülethez való hozzáadásához használja a [Azure monitor-megoldások hozzáadása a Solutions Galleryban](../../azure-monitor/insights/solutions.md) című témakörben leírt eljárást.

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Azure SQL Database-adatbázisok, rugalmas készletek és felügyelt példányok konfigurálása a stream Diagnostics telemetria

Miután létrehozta Azure SQL Analytics megoldást a munkaterületen, **konfigurálnia** kell a figyelni kívánt erőforrásokat a diagnosztikai telemetria a megoldásba való továbbításához. Kövesse az oldalon található részletes utasításokat:

- Engedélyezze Azure Diagnostics az Azure SQL Database-hez, hogy a [stream diagnosztikai telemetria Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

A fenti oldalon megtudhatja, hogyan engedélyezheti a több Azure-előfizetés figyelését egyetlen Azure SQL Analytics munkaterületről egyetlen üvegtáblaként.

## <a name="using-the-solution"></a>A megoldás használata

Amikor hozzáadja a megoldást a munkaterülethez, a Azure SQL Analytics csempe bekerül a munkaterületre, és megjelenik az Áttekintés területen. A csempe tartalmának betöltéséhez válassza az összefoglalás megtekintése hivatkozást.

![Azure SQL Analytics összefoglaló csempe](./media/azure-sql/azure-sql-sol-tile-01.png)

A betöltést követően a csempe megjeleníti az Azure SQL Database-adatbázisok, rugalmas készletek, felügyelt példányok és a felügyelt példányokban lévő adatbázisok számát, amelyeket a megoldás diagnosztikai telemetria kapott.

![Azure SQL Analytics csempe](./media/azure-sql/azure-sql-sol-tile-02.png)

A megoldás két különálló nézetet biztosít – egyet az Azure SQL Database-adatbázisok és a rugalmas készletek figyelésére, valamint a felügyelt példányok figyelésére szolgáló másik nézetet, valamint a felügyelt példányokban lévő adatbázisokat.

Az Azure SQL Database-adatbázisok és rugalmas készletek Azure SQL Analytics monitorozási irányítópultjának megtekintéséhez kattintson a csempe felső részén. A felügyelt példányok Azure SQL Analytics figyelési irányítópultjának, valamint a felügyelt példányok adatbázisainak megtekintéséhez kattintson a csempe alsó részére.

### <a name="viewing-azure-sql-analytics-data"></a>AdatAzure SQL Analyticsek megtekintése

Az irányítópult a különböző perspektívák által figyelt adatbázisok áttekintését tartalmazza. A különböző perspektívák működéséhez engedélyeznie kell a megfelelő metrikákat vagy naplókat az SQL-erőforrásokon a Log Analytics munkaterületre való továbbításhoz.

Vegye figyelembe, hogy ha egyes mérőszámok vagy naplók nem áramlanak Azure Monitorba, a megoldás csempéi nem lesznek kitöltve a figyelési adatokkal.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Azure SQL Database és rugalmas készlet nézet

Miután kiválasztotta az adatbázis Azure SQL Analytics csempét, megjelenik a figyelési irányítópult.

![Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview.png)

A csempék bármelyikének kiválasztásával megnyithatja a részletezési jelentést az adott perspektívában. A perspektíva kiválasztását követően megnyílik a részletezési jelentés.

![Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics.png)

Az ebben a nézetben szereplő összes perspektíva az előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázis szintjének összegzését tartalmazza. Emellett minden perspektívában látható a jelentésre vonatkozó perspektíva a jobb oldalon. Ha kijelöli az előfizetést, a kiszolgálót, a készletet vagy az adatbázist a listából, folytatja a részletezést.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>Felügyelt példányok és adatbázisok felügyelt példány nézetben

Miután kiválasztotta az adatbázisok Azure SQL Analytics csempét, megjelenik a figyelési irányítópult.

![Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview-mi.png)

A csempék bármelyikének kiválasztásával megnyithatja a részletezési jelentést az adott perspektívában. A perspektíva kiválasztását követően megnyílik a részletezési jelentés.

A felügyelt példány nézet kiválasztásával megtekintheti a felügyelt példányok kihasználtságának részleteit, a benne lévő adatbázisokat és a példányon végrehajtott lekérdezések telemetria.

![Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights jelentés

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) segítségével megtudhatja, mi történik az összes Azure SQL-adatbázis teljesítményével. Az összegyűjtött Intelligent Insights a bepillantások perspektívájában megjeleníthetők és elérhetők.

![Azure SQL Analyticsi adatfelismerés](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rugalmas készlet és adatbázis-jelentések

Mind a rugalmas készletek, mind az SQL-adatbázisok rendelkeznek saját konkrét jelentésekkel, amelyek az adott időszakban az erőforráshoz összegyűjtött összes adatokat megjelenítik.

![Adatbázis Azure SQL Analytics](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL rugalmas készlet](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Jelentések lekérdezése

A lekérdezés időtartama és a lekérdezési várakozási idő alapján a lekérdezési jelentésen keresztül összekapcsolhatók a lekérdezések teljesítménye. Ez a jelentés összehasonlítja a lekérdezési teljesítményt a különböző adatbázisok között, és megkönnyíti a kiválasztott lekérdezési és lassú összehasonlítást végző adatbázisok azonosítását.

![Lekérdezések Azure SQL Analytics](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Engedélyek

A Azure SQL Analytics használatához a felhasználóknak minimális jogosultságot kell biztosítaniuk az olvasó szerepkörhöz az Azure-ban. Ez a szerepkör azonban nem teszi lehetővé a felhasználók számára a lekérdezési szöveg megtekintését, vagy Automatikus hangolási műveletek elvégzését. Az Azure-ban több olyan engedékenyebb szerepkört, amelyek lehetővé teszik a megoldás teljes körű használatát a tulajdonos, a közreműködő, az SQL DB közreműködő vagy SQL Server közreműködő. Érdemes lehet egyéni szerepkört létrehozni a portálon olyan konkrét engedélyekkel, amelyek csak a Azure SQL Analytics használatához szükségesek, és nem férnek hozzá más erőforrások kezeléséhez.

### <a name="creating-a-custom-role-in-portal"></a>Egyéni szerepkör létrehozása a portálon

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Annak felismerése, hogy egyes szervezetek szigorú engedélyekkel rendelkeznek az Azure-ban, keresse meg a következő PowerShell-parancsfájlt, amely lehetővé teszi az "SQL Analytics-figyelő operátor" egyéni szerepkör létrehozását a Azure Portal a minimális olvasási és írási engedéllyel Azure SQL Analytics használata a legteljesebb mértékben.

Cserélje le a "{SubscriptionId}" kifejezést az alábbi parancsfájlban az Azure-előfizetési AZONOSÍTÓra, és hajtsa végre a bejelentkezett parancsfájlt tulajdonosi vagy közreműködői szerepkörként az Azure-ban.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Az új szerepkör létrehozása után rendelje hozzá ezt a szerepkört minden olyan felhasználóhoz, akinek egyéni engedélyeket kell adnia a Azure SQL Analytics használatához.

## <a name="analyze-data-and-create-alerts"></a>Az adatelemzés és a riasztások létrehozása

Az adatelemzés Azure SQL Analytics az egyéni lekérdezés és jelentéskészítés [log Analytics nyelvén](../log-query/get-started-queries.md) alapul. Az adatbázis-erőforrásból összegyűjtött rendelkezésre álló adatok leírását az egyéni lekérdezésekhez a [mérőszámok és naplók elérhetővé](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)tételében találja.

A megoldásban az automatizált riasztások olyan Log Analytics-lekérdezések írására szolgálnak, amelyek egy adott feltétel teljesülése esetén riasztást indítanak. Az alábbiakban számos példát talál Log Analytics lekérdezésekre, amelyeken a riasztás beállítható a megoldásban.

### <a name="creating-alerts-for-azure-sql-database"></a>Riasztások létrehozása Azure SQL Databasehoz

Egyszerűen létrehozhat Azure SQL Database erőforrásokból érkező adatokkal kapcsolatos [riasztásokat](../platform/alerts-metric.md) . Íme néhány hasznos, a log-riasztással használható [naplózási lekérdezés](../log-query/log-query-overview.md) :

#### <a name="high-cpu-on-azure-sql-database"></a>Magas CPU Azure SQL Database

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok alapvető mérőszámokat továbbítanak a megoldásnak.
> - Ehelyett cserélje le a MetricName értéket cpu_percent és dtu_consumption_percent a magas DTU eredmények beszerzéséhez.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Magas CPU Azure SQL Database rugalmas készletekben

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok alapvető mérőszámokat továbbítanak a megoldásnak.
> - Ehelyett cserélje le a MetricName értéket cpu_percent és dtu_consumption_percent a magas DTU eredmények beszerzéséhez.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Az elmúlt 1 HR-ben átlagosan a 95%-nál nagyobb tárterületet Azure SQL Database.

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok alapvető mérőszámokat továbbítanak a megoldásnak.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel egyes adatbázisokon létezik. A kimenet azoknak az adatbázis-erőforrásoknak a listája, amelyek a megadott time_range storage_threshold felett vannak.
> - A kimenet azoknak az adatbázis-erőforrásoknak a listája, amelyek a megadott time_range storage_threshold felett vannak.

#### <a name="alert-on-intelligent-insights"></a>Intelligens adatfelismerések riasztása

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok stream SQLInsights diagnosztikai naplója a megoldáshoz legyen.
> - Ehhez a lekérdezéshez a riasztási szabályt úgy kell beállítani, hogy az ugyanazzal a gyakorisággal fusson, mint a alert_run_interval az eredmények elkerülése érdekében. A szabályt úgy kell beállítani, hogy kikapcsolja a riasztást, ha létezik eredmény (> 0 eredmény) a lekérdezésből.
> - Szabja testre a alert_run_interval, hogy megadja az időtartományt, hogy meggyőződjön arról, hogy a feltétel bekövetkezett-e az SQLInsights-napló a megoldáshoz való továbbítására konfigurált adatbázisokban
> - A insights_string testre szabásával rögzítheti az elemzések kiváltó okának elemzési szövegének eredményét. Ez ugyanaz a szöveg jelenik meg a megoldás felhasználói felületén, amelyet a meglévő adatokból használhat. Azt is megteheti, hogy az alábbi lekérdezéssel megtekinti az előfizetésen generált összes információ szövegét. A lekérdezés kimenetének használatával begyűjtheti a riasztások beállításához szükséges különböző karakterláncokat.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Riasztások létrehozása a felügyelt példányhoz

#### <a name="managed-instance-storage-is-above-90"></a>A felügyelt példányok tárolója 90% fölött van

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - A riasztás beállításának előfeltétele, hogy a figyelt felügyelt példányon engedélyezve legyen a ResourceUsageStats-napló folyamatos átvitele a megoldáshoz.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel létezik a felügyelt példányon. A kimenet a felügyelt példány tárolási százalékos aránya.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Felügyelt példány CPU átlagos fogyasztása 95%-nál nagyobb az elmúlt 1 HR-ben

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - A riasztás beállításának előfeltétele, hogy a figyelt felügyelt példányon engedélyezve legyen a ResourceUsageStats-napló folyamatos átvitele a megoldáshoz.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel létezik a felügyelt példányon. A kimenet a felügyelt példányon megadott időszakra vonatkozó átlagos CPU-kihasználtság százalékos aránya.

### <a name="pricing"></a>Díjszabás

Amíg a megoldás ingyenesen használható, a diagnosztika telemetria az egyes hónapokban lefoglalt adatfeldolgozás ingyenes egységei felett kell lennie, lásd: [log Analytics díjszabás](https://azure.microsoft.com/pricing/details/monitor). Az adatfeldolgozás ingyenes egysége lehetővé teszi, hogy minden hónapban több adatbázis ingyenes figyelése legyen elérhető. Vegye figyelembe, hogy a súlyosabb számítási feladatokkal rendelkező több aktív adatbázis több adatot és üresjárati adatbázisokat is felhasznál. A megoldásban egyszerűen figyelheti az adatok betöltését, ha a Azure SQL Analytics navigációs menüjében a OMS munkaterület lehetőségre kattint, majd kiválasztja a használati és becsült költségeket.

## <a name="next-steps"></a>Következő lépések

- A Azure Monitorban található [naplók](../log-query/log-query-overview.md) használatával részletes Azure SQL-információk jeleníthetők meg.
- [Saját irányítópultokat hozhat létre](../learn/tutorial-logs-dashboards.md) , amelyek az Azure SQL-adatait jelenítik meg.
- [Riasztások létrehozása](../platform/alerts-overview.md) , ha adott Azure SQL-események történnek.
