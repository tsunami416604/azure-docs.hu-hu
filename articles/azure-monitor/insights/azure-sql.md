---
title: Azure SQL Analytics megoldás a Azure Monitorban | Microsoft Docs
description: Az Azure SQL Analytics megoldás segítségével az Azure SQL-adatbázisok kezelése
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667040"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Az Azure SQL Database megfigyelése az Azure SQL Analytics (előzetes verzió)

![Az Azure SQL Analytics szimbólum](./media/azure-sql/azure-sql-symbol.png)

A Azure SQL Analytics egy fejlett felhőalapú figyelési megoldás, amely az összes Azure SQL Database-adatbázis teljesítményét és egyetlen nézetben több előfizetést is figyel. A Azure SQL Analytics a teljesítménnyel kapcsolatos hibaelhárításhoz beépített intelligenciával rendelkező fő teljesítménymutatókat gyűjti és jeleníti meg.

Az összegyűjtött metrikák használatával egyéni figyelési szabályokat és riasztásokat hozhat létre. Azure SQL Analytics segítségével azonosíthatja a problémákat az alkalmazás-verem minden rétegében. Az Azure diagnosztikai metrikákat és Azure Monitor nézeteket használ, hogy az összes Azure SQL-adatbázis adatait egyetlen Log Analytics munkaterületen lehessen bemutatni. Azure Monitor a strukturált és strukturálatlan adatok összegyűjtését, összekapcsolását és megjelenítését teszi lehetővé.

A gyakorlati áttekintése az Azure SQL Analytics megoldás használatával és a jellemző használati forgatókönyvei: a beágyazott videót:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Összekapcsolt források

A Azure SQL Analytics egy Felhőbeli figyelési megoldás, amely támogatja az összes Azure SQL-adatbázis diagnosztikai telemetria folyamatos átvitelét. Mivel Azure SQL Analytics nem használ ügynököket Azure Monitorhoz való kapcsolódásra, nem támogatja a helyszíni vagy virtuális gépeken üzemeltetett SQL Server figyelését.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Diagnosztikai beállítások](../platform/diagnostic-settings.md) | **Igen** | Az Azure metrika és a naplózási adatok közvetlenül az Azure-ban érkeznek Azure Monitor naplókra. |
| [Azure Storage-fiók](../platform/collect-azure-metrics-logs.md) | Nem | Azure Monitor nem olvassa be a Storage-fiók adatait. |
| [Windows-ügynökök](../platform/agent-windows.md) | Nem | Azure SQL Analytics nem használja a közvetlen Windows-ügynököket. |
| [Linux-ügynökök](../learn/quick-collect-linux-computer.md) | Nem | Azure SQL Analytics nem használja a közvetlen Linux-ügynököket. |
| [System Center Operations Manager felügyeleti csoport](../platform/om-agents.md) | Nem | A Azure SQL Analytics nem használja közvetlen kapcsolódást a Operations Manager ügynöktől a Azure Monitor. |

## <a name="azure-sql-analytics-options"></a>Azure SQL Analytics beállítások

Az alábbi táblázat a Azure SQL Analytics irányítópult két verziójának támogatott lehetőségeit vázolja fel, egyet az önálló és a készletezett adatbázisokhoz, valamint a rugalmas készletekhez, a másikat pedig felügyelt példányok és példány-adatbázisok számára.

| Azure SQL Analytics lehetőség | Leírás | Önálló és készletezett adatbázisok és rugalmas készletek támogatása | Felügyelt példányok és példányok adatbázis-támogatása |
| --- | ------- | ----- | ----- |
| Erőforrás típusa szerint | Adott összes, a figyelt erőforrások szempontjából. | Igen | Igen |
| Insights | Hierarchikus Lehatolás biztosít intelligens elemzésekké teljesítményéről. | Igen | Igen |
| Hibák | Hierarchikus Lehatolás biztosít, és ismételje meg az adatbázisok SQL hibákká. | Igen | Igen |
| Időtúllépések | Hierarchikus Lehatolás biztosít az SQL-időtúllépések, az adatbázisok történt. | Igen | Nem |
| Letiltások | Hierarchikus Lehatolás biztosít, és ismételje meg az adatbázisok SQL blockings be. | Igen | Nem |
| Adatbázis-várakozások | SQL várakozási statisztikák adatbázisszintű hierarchikus Lehatolás szolgál kapcsolatban. Teljes várakozási idő és a várakozási idő várakozási típusonként összegzéseket tartalmaz. |Igen | Nem |
| Lekérdezés időtartama | Hierarchikus Lehatolás biztosít, például a lekérdezések időtartama, CPU-használat, adat IO kihasználtsága, naplózási IO kihasználtsága lekérdezés végrehajtási statisztikák. | Igen | Igen |
| Lekérdezési várakozások | Hierarchikus Lehatolás biztosít, a lekérdezés várakozási statisztikák várakozási kategória szerint. | Igen | Igen |

## <a name="configuration"></a>Konfiguráció

Használja a [Solutions Gallery Azure monitor-megoldások hozzáadása az](../../azure-monitor/insights/solutions.md) Azure SQL Analytics (előzetes verzió) a log Analytics munkaterülethez való hozzáadásához használt eljárást.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Azure SQL Database-adatbázisok konfigurálása a stream Diagnostics telemetria

Miután létrehozta Azure SQL Analytics megoldást a munkaterületen, **konfigurálnia** kell az összes figyelni kívánt erőforrást, hogy a diagnosztikai telemetria továbbítsa a Azure SQL Analytics. Az alábbi részletes útmutatás:

- Engedélyezze Azure Diagnostics az Azure SQL Database-hez, hogy a [stream diagnosztikai telemetria Azure SQL Analytics](../../sql-database/sql-database-metrics-diag-logging.md).

A fenti lapot is útmutatás több Azure-előfizetéssel egyetlen Azure SQL Analytics-munkaterületről monitorozásra tekinthesse egyetlen ablaktábla támogatásának engedélyezése.

## <a name="using-azure-sql-analytics"></a>Azure SQL Analytics használata

Azure SQL Analytics a munkaterülethez való hozzáadásakor a rendszer hozzáadja a Azure SQL Analytics csempét a munkaterülethez, és megjelenik az Áttekintés területen. A csempe tartalmának betöltéséhez válassza az összefoglalás megtekintése hivatkozást.

![Azure SQL Analytics összefoglaló csempe](./media/azure-sql/azure-sql-sol-tile-01.png)

A betöltés után a csempén látható az önálló és a készletezett adatbázisok, a rugalmas készletek, a felügyelt példányok és a felügyelt példányok adatbázisainak száma, amelyekből Azure SQL Analytics diagnosztikai telemetria kapnak.

![Az Azure SQL Analytics csempére](./media/azure-sql/azure-sql-sol-tile-02.png)

Azure SQL Analytics két különálló nézetet biztosít – egyet az önálló adatbázisok és a készletezett adatbázisok, valamint a rugalmas készletek figyelésére, valamint a felügyelt példányok és példányok adatbázisainak figyelésére szolgáló másik nézetre.

Az önálló és a készletezett adatbázisok és rugalmas készletek Azure SQL Analytics monitorozási irányítópultjának megtekintéséhez kattintson a csempe felső részén. A felügyelt példányok és példány-adatbázisok Azure SQL Analytics monitorozási irányítópultjának megtekintéséhez kattintson a csempe alsó részére.

### <a name="viewing-azure-sql-analytics-data"></a>Az Azure SQL Analytics-adatok megtekintése

Az irányítópult programban különböző szempontok szerint keresztül figyelt összes adatbázis áttekintését tartalmazza. A különböző perspektívák működéséhez engedélyeznie kell a megfelelő metrikákat vagy naplókat az SQL-erőforrásokon a Log Analytics munkaterületre való továbbításhoz.

Ha egyes mérőszámok vagy naplók nem áramlanak be Azure Monitorba, a Azure SQL Analytics csempéi nem lesznek kitöltve a figyelési információkkal.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Önálló és készletezett adatbázisok és rugalmas készletek nézet

Miután az Azure SQL Analytics csempére az adatbázishoz ki van jelölve, a figyelési irányítópult látható.

![Az Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

![Az Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics.png)

Az ebben a nézetben szereplő összes perspektíva összefoglalókat biztosít az előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázis szintjén. Emellett minden szempontból bemutatja egy perspektíva vonatkozó a jelentésben a jobb oldalon. A Lehatolás előfizetés, a kiszolgáló, a készlet vagy az adatbázis kiválasztása a listából továbbra is.

### <a name="managed-instance-and-instances-databases-view"></a>Felügyelt példányok és példányok adatbázisainak nézete

Ha az adatbázisok az Azure SQL Analytics csempére van kijelölve, a figyelési irányítópult látható.

![Az Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview-mi.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

A felügyelt példány nézet kiválasztásával megtekintheti a felügyelt példányok kihasználtságának részleteit, a benne lévő adatbázisokat és a példányon végrehajtott lekérdezések telemetria.

![Az Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligent Insights jelentés

Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) segítségével megtudhatja, mi történik az összes Azure SQL-adatbázis teljesítményével. Gyűjtött összes Intelligent Insights is formájában jelenik meg, és az elemzések szempontjából keresztül érhetők el.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Rugalmas készletek és adatbázis-jelentések

A rugalmas készletek és adatbázisok saját jelentésekkel rendelkeznek, amelyek az adott időszakban az adott erőforráshoz összegyűjtött összes adatokat megjelenítik.

![Az Azure SQL Analytics adatbázis](./media/azure-sql/azure-sql-sol-database.png)

![Az Azure SQL rugalmas készlet](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Lekérdezés jelentések

A lekérdezés időtartama és a lekérdezési várakozási idő alapján a lekérdezési jelentésen keresztül összekapcsolhatók a lekérdezések teljesítménye. Ez a jelentés a lekérdezési teljesítmény összehasonlítja a különböző adatbázisok között, és megkönnyíti a rögzítési ponthoz, amely a kijelölt lekérdezést is, amelyet a lassú és adatbázisok.

![Az Azure SQL Analytics-lekérdezések](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Engedélyek

Az Azure SQL Analytics használatához a felhasználóknak meg kell az Olvasó szerepkörhöz az Azure-ban, egy minimális engedéllyel kell rendelkezni. Ez a szerepkör azonban nem engedélyezése a felhasználóknak a lekérdezés szövegének megtekintéséhez, vagy bármely automatikus hangolási műveletek végrehajtása. Az Azure-ban több, a tulajdonos, a közreműködő, az SQL-adatbázis közreműködője vagy a SQL Server közreműködő általi használatának lehetővé tételét engedélyező, az Azure-ban elérhetővé teszi az Azure SQL Analytics. Érdemes azt is érdemes figyelembe venni, egyéni szerepkörök létrehozása a portálon csak az Azure SQL Analytics használatához szükséges konkrét engedélyeket, és nincs hozzáférése az egyéb erőforrások kezelése.

### <a name="creating-a-custom-role-in-portal"></a>Egyéni szerepkörök létrehozása a portálon

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

FELISMERVE, hogy az egyes szervezetek kényszerítése szigorú engedély szabályozza az Azure-ban, keresse meg a következő PowerShell-parancsfájl engedélyezése "Az SQL Analytics Alkalmazásfigyelési operátor" az Azure Portalon a minimális olvasási és írási engedély szükséges egy egyéni szerepkör létrehozása a lehető legnagyobb mértékben az Azure SQL Analytics használatával.

Cserélje le a "{SubscriptionId}" az az alábbi szkriptet az Azure-előfizetése Azonosítóját, és hajtsa végre a parancsfájl van bejelentkezve: egy tulajdonosi vagy közreműködői szerepkörhöz az Azure-ban.

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

Az új szerepkör létrehozása után ez a szerepkör hozzárendelése minden felhasználóhoz, az Azure SQL Analytics használandó egyéni engedélyek megadása szükséges.

## <a name="analyze-data-and-create-alerts"></a>Adatok elemzése és riasztások létrehozása

Az adatelemzés Azure SQL Analytics az egyéni lekérdezés és jelentéskészítés [log Analytics nyelvén](../log-query/get-started-queries.md) alapul. Az adatbázis-erőforrásból összegyűjtött rendelkezésre álló adatok leírását az egyéni lekérdezésekhez a [mérőszámok és naplók elérhetővé](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)tételében találja.

A Azure SQL Analytics automatikus riasztásai olyan Log Analytics-lekérdezés írásán alapulnak, amely egy adott feltétel teljesülése esetén riasztást indít el. Az alábbi példákban talál néhány példát Log Analytics lekérdezésekre, amelyeken a riasztás beállítható Azure SQL Analyticsban.

### <a name="creating-alerts-for-azure-sql-database"></a>Riasztások létrehozása az Azure SQL Database

Egyszerűen létrehozhat Azure SQL Database erőforrásokból érkező adatokkal kapcsolatos [riasztásokat](../platform/alerts-metric.md) . Íme néhány hasznos, a log-riasztással használható [naplózási lekérdezés](../log-query/log-query-overview.md) :

#### <a name="high-cpu-on-azure-sql-database"></a>Az Azure SQL Database magas CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok az alapszintű mérőszámokat a Azure SQL Analyticsra továbbítsák.
> - Cserélje le a MetricName érték cpu_percent dtu_consumption_percent inkább juthat magas DTU-eredményeket.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Az Azure SQL Database rugalmas készletei magas CPU

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok az alapszintű mérőszámokat a Azure SQL Analyticsra továbbítsák.
> - Cserélje le a MetricName érték cpu_percent dtu_consumption_percent inkább juthat magas DTU-eredményeket.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Az Azure SQL Database storage átlag felett 95 %-ot az elmúlt 1 óra

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
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok az alapszintű mérőszámokat a Azure SQL Analyticsra továbbítsák.
> - Ez a lekérdezés szükséges egy riasztási szabályt úgy, hogy eltávolítja a lekérdezésből, így jelölve az, hogy létezik-e a feltétel az egyes adatbázisokon Ha léteznek az eredmények (> 0 eredmény) ki a riasztás aktiválódik. A kimenet az adatbázis-erőforrások, amely meghaladja a meghatározott time_range belül a storage_threshold listája.
> - A kimenet az adatbázis-erőforrások, amely meghaladja a meghatározott time_range belül a storage_threshold listája.

#### <a name="alert-on-intelligent-insights"></a>Az Intelligent insights riasztás

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
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok stream SQLInsights diagnosztikai naplója Azure SQL Analytics.
> - Ez a lekérdezés egy riasztási szabályt úgy, hogy a Futtatás alert_run_interval gyakorisággal ismétlődő eredmények elkerülése érdekében van szükség. A szabály tűznek ki a riasztást, ha léteznek az eredmények (> 0 eredmény) a lekérdezés kell beállítani.
> - A alert_run_interval testreszabásával megadhatja az időtartományt, amely alapján ellenőrizhető, hogy a feltétel bekövetkezett-e olyan adatbázisokon, amelyek a SQLInsights-naplónak a Azure SQL Analytics.
> - Testre szabhatja a insights_string rögzítheti a kimenet a Insights alapvető ok elemzési szöveg. Ez az Azure SQL Analytics felhasználói felületén megjelenő szöveg, amelyet a meglévő adatokból használhat. Az alábbi lekérdezés segítségével azt is megteheti, tekintse meg az előfizetéshez létrehozott összes Insights szövegét. A lekérdezés kimenetének használatával gyűjtsön a distinct karakterláncok Insights riasztások beállításához.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Riasztások létrehozása a felügyelt példányokhoz

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
>
> - A riasztás beállításának előfeltétele, hogy a felügyelt példány figyelése a ResourceUsageStats-napló folyamatos átvitelét teszi lehetővé Azure SQL Analytics.
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
>
> - A riasztás beállításának előfeltétele, hogy a figyelt felügyelt példányon engedélyezve legyen a ResourceUsageStats-napló folyamatos átvitele Azure SQL Analytics.
> - Ehhez a lekérdezéshez riasztási szabályt kell beállítani a riasztás kikapcsolásához, ha a lekérdezés eredménye (> 0 eredmény) létezik, jelezve, hogy a feltétel létezik a felügyelt példányon. A kimenet a felügyelt példányon megadott időszakra vonatkozó átlagos CPU-kihasználtság százalékos aránya.

### <a name="pricing"></a>Díjszabás

Míg a Azure SQL Analytics ingyenesen használható, a diagnosztika telemetria az egyes hónapokban lefoglalt ingyenes adatfeldolgozási egységeken felüli felhasználást lásd: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/monitor). Az adatbetöltés foglalt ingyenes egységek ingyenes figyelés engedélyezése több adatbázis minden hónapban. Nagyobb mennyiségű aktív adatbázis több adatot és üresjárati adatbázist is felhasznál. A Azure SQL Analytics navigációs menüjében a OMS munkaterület elem kiválasztásával, majd a használat és a becsült költségek lehetőség kiválasztásával egyszerűen figyelheti az adatok betöltésének felhasználását Azure SQL Analytics.

## <a name="next-steps"></a>Következő lépések

- A Azure Monitorban található [naplók](../log-query/log-query-overview.md) használatával részletes Azure SQL-információk jeleníthetők meg.
- [Saját irányítópultokat hozhat létre](../learn/tutorial-logs-dashboards.md) , amelyek az Azure SQL-adatait jelenítik meg.
- [Riasztások létrehozása](../platform/alerts-overview.md) , ha adott Azure SQL-események történnek.
