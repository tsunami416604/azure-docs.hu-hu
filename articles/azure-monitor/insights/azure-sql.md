---
title: Az Azure SQL Analytics megoldás a Log Analytics Rendszereben |} A Microsoft Docs
description: Az Azure SQL Analytics megoldás segítségével az Azure SQL-adatbázisok kezelése
services: log-analytics
ms.service: log-analytics
ms.subservice: performance
ms.custom: ''
ms.topic: conceptual
author: danimir
ms.reviewer: carlrab
manager: craigg
ms.date: 11/26/2018
ms.author: v-daljep
ms.openlocfilehash: bf2cbdb57276fccd31af61f2df1b76eeba816e66
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341626"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Az Azure SQL Database megfigyelése az Azure SQL Analytics (előzetes verzió)

![Az Azure SQL Analytics szimbólum](./media/azure-sql/azure-sql-symbol.png)

Az Azure SQL Analytics egy felhőalapú megoldást kínál a teljesítmény figyelése Azure SQL adatbázisok, rugalmas készletek és a felügyelt példányok ipari méretekben és tekinthesse át egyetlen több előfizetésre kiterjedő figyelési. Azt gyűjti elérhetővé, és fontos Azure SQL Database teljesítmény-mérőszámok – teljesítménnyel kapcsolatos hibaelhárítás a beépített intelligenciával.

A megoldással gyűjtött metrikák használatával létrehozhat egyéni figyelési szabályokkal és riasztásokkal. A megoldás segítségével azonosíthatja a problémákat az alkalmazáscsoportokat az egyes rétegben. Adatokat az összes az Azure SQL adatbázisok, rugalmas készletek és adatbázisok szerepelnek a felügyelt példányok egyetlen Log Analytics-munkaterületet a Log Analytics nézeteivel együtt az Azure diagnosztikai metrikák használ. A log Analytics segítségével összegyűjtését, összekapcsolását és megjelenítését a strukturált és strukturálatlan adatok.

A gyakorlati áttekintése az Azure SQL Analytics megoldás használatával és a jellemző használati forgatókönyvei: a beágyazott videót:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Összekapcsolt források

Az Azure SQL Analytics csak egy felhőalapú megoldást támogató adatfolyamként diagnostics telemetriai adatainak az Azure SQL Database-adatbázisok figyelése: egyetlen, összevont és felügyelt példányok adatbázisai. A megoldás nem használja az ügynökök a Log Analytics szolgáltatáshoz való csatlakozáshoz, mivel a megoldás nem támogatja a helyszíni SQL Server tárolt vagy a virtuális gépek figyelése, az alábbi tábla a kompatibilitási.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Azure Diagnostics](../../azure-monitor/platform/collect-azure-metrics-logs.md) | **Igen** | Az Azure metrika- és naplózási adatok közvetlenül az Azure Log Analytics érkeznek. |
| [Azure Storage-fiók](../../azure-monitor/platform/collect-azure-metrics-logs.md) | Nem | A log Analytics egy storage-fiókból az adatok nem olvashatók. |
| [Windows-ügynökök](../../azure-monitor/platform/agent-windows.md) | Nem | A megoldás a közvetlen Windows-ügynökök nem használja. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem | A megoldás a közvetlen Linux-ügynökök nem használja. |
| [System Center Operations Manager felügyeleti csoport](../platform/om-agents.md) | Nem | Közvetlen kapcsolat legyen az Operations Manager-ügynök a Log Analytics szolgáltatáshoz a megoldás nem használja. |

## <a name="configuration"></a>Konfiguráció

Hajtsa végre az alábbi lépéseket az Azure SQL Analytics megoldás hozzáadása az Azure-irányítópulton.

1. Az Azure SQL Analytics megoldás hozzáadni a munkaterülethez [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Az Azure Portalon kattintson a **+ erőforrás létrehozása**, majd keresse meg az **Azure SQL Analytics**.  
    ![Felügyelet és kezelés](./media/azure-sql/monitoring-management.png)
3. Válassza ki **Azure SQL Analytics (előzetes verzió)** a listából
4. Az a **Azure SQL Analytics (előzetes verzió)** területen kattintson a **létrehozás**.  
    ![Létrehozás](./media/azure-sql/portal-create.png)
5. Az a **új megoldás létrehozása** területen létrehozhat új, vagy válasszon ki egy meglévő munkaterületet, adja hozzá a megoldást, és kattintson a kívánt **létrehozás**.

    ![munkaterület hozzáadása](./media/azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Az Azure SQL Database, a rugalmas készletek és a felügyelt példányok stream diagnostics telemetriai adatainak konfigurálása

Miután létrehozta az Azure SQL Analytics megoldás a munkaterületen található, meg kell **konfigurálja** közvetítése a diagnostics telemetriai adatainak a megoldás a figyelni kívánt erőforrásokat. Az alábbi részletes útmutatás:

- Az Azure-diagnosztika engedélyezése az Azure SQL Database [diagnostics telemetriai adatainak az Azure SQL Analytics adatfolyam](../../sql-database/sql-database-metrics-diag-logging.md).

A fenti lapot is útmutatás több Azure-előfizetéssel egyetlen Azure SQL Analytics-munkaterületről monitorozásra tekinthesse egyetlen ablaktábla támogatásának engedélyezése.

## <a name="using-the-solution"></a>A megoldás használata

A munkaterület a megoldás hozzáadásakor az Azure SQL Analytics csempére a munkaterületéhez kerül, és akkor jelenik meg, az Áttekintés. A csempe a felügyelt példányok, a megoldás diagnosztikai telemetriáját fogadó Azure SQL-adatbázisok, rugalmas készletek, felügyelt példányok és adatbázisok számát jeleníti meg.

![Az Azure SQL Analytics csempére](./media/azure-sql/azure-sql-sol-tile.png)

A megoldás két különböző – egy Azure SQL Database-adatbázisok és rugalmas készletek és a többi nézet a figyelési felügyelt példányt, és a felügyelt példányok adatbázisai figyelés nézetet biztosít.

Az Azure SQL Database-adatbázisok és rugalmas készletek Azure SQL Analytics figyelési irányítópult megtekintéséhez kattintson a csempére felső részén. Az Azure SQL Analytics figyelési irányítópult felügyelt példányt, és a felügyelt példány adatbázisok megtekintéséhez kattintson a csempe alsó részén.

### <a name="viewing-azure-sql-analytics-data"></a>Az Azure SQL Analytics-adatok megtekintése

Az irányítópult programban különböző szempontok szerint keresztül figyelt összes adatbázis áttekintését tartalmazza. A programban különböző szempontok szerint működjön engedélyeznie kell megfelelő metrikákat vagy naplókat az SQL-erőforrások Azure Log Analytics-munkaterületre is streamelhetők.

Vegye figyelembe, hogy néhány metrikákat vagy naplókat az Azure Log Analytics nem streameli, ha a csempéket a megoldásban nincsenek feltöltve adattal figyelési információkat.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Az Azure SQL Database és a rugalmas készlet megtekintése

Miután az Azure SQL Analytics csempére az adatbázishoz ki van jelölve, a figyelési irányítópult látható.

![Az Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

![Az Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics.png)

Ebben a nézetben minden egyes perspektívát nyújt összefoglaló előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázisszintű. Emellett minden szempontból bemutatja egy perspektíva vonatkozó a jelentésben a jobb oldalon. A Lehatolás előfizetés, a kiszolgáló, a készlet vagy az adatbázis kiválasztása a listából továbbra is.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>A felügyelt példánynak és a felügyelt példány adatbázisai megtekintése

Ha az adatbázisok az Azure SQL Analytics csempére van kijelölve, a figyelési irányítópult látható.

![Az Azure SQL Analytics áttekintése](./media/azure-sql/azure-sql-sol-overview-mi.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

Részleteket a felügyelt példány kihasználtságáról, a benne található adatbázisok és a példány között végrehajtott lekérdezések vonatkozó telemetriai adatokat a felügyelt példány nézetek kijelölése jeleníti meg.

![Az Azure SQL Analytics időtúllépések](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="perspectives"></a>Perspektívák

Az alábbi táblázat ismerteti a perspektívák az irányítópulton, így az Azure SQL database és a rugalmas készletek és a másik egy felügyelt példány két verziója támogatott.

| Perspektíva | Leírás | Támogatja az SQL Database és a rugalmas készletek | Felügyelt példány támogatása |
| --- | ------- | ----- | ----- |
| Erőforrás típusa szerint | Adott összes, a figyelt erőforrások szempontjából. | Igen | Igen |
| Insights | Hierarchikus Lehatolás biztosít intelligens elemzésekké teljesítményéről. | Igen | Igen |
| Hibák | Hierarchikus Lehatolás biztosít, és ismételje meg az adatbázisok SQL hibákká. | Igen | Igen |
| Időtúllépések | Hierarchikus Lehatolás biztosít az SQL-időtúllépések, az adatbázisok történt. | Igen | Nem |
| Letiltások | Hierarchikus Lehatolás biztosít, és ismételje meg az adatbázisok SQL blockings be. | Igen | Nem |
| Adatbázis-várakozások | SQL várakozási statisztikák adatbázisszintű hierarchikus Lehatolás szolgál kapcsolatban. Teljes várakozási idő és a várakozási idő várakozási típusonként összegzéseket tartalmaz. |Igen | Igen |
| Lekérdezés időtartama | Hierarchikus Lehatolás biztosít, például a lekérdezések időtartama, CPU-használat, adat IO kihasználtsága, naplózási IO kihasználtsága lekérdezés végrehajtási statisztikák. | Igen | Igen |
| Lekérdezési várakozások | Hierarchikus Lehatolás biztosít, a lekérdezés várakozási statisztikák várakozási kategória szerint. | Igen | Igen |

### <a name="intelligent-insights-report"></a>Intelligent Insights jelentés

Az Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) lehetővé teszi, hogy tudja, mi történik az összes Azure SQL-adatbázisok teljesítményét. Gyűjtött összes Intelligent Insights is formájában jelenik meg, és az elemzések szempontjából keresztül érhetők el.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rugalmas készlet és az adatbázis-jelentések

SQL-adatbázisok és rugalmas készletek rendelkezik a saját konkrét jelentések megjelenítése az erőforrás a megadott időtartam alatt gyűjtött összes adat.

![Az Azure SQL Analytics adatbázis](./media/azure-sql/azure-sql-sol-database.png)

![Az Azure SQL rugalmas készlet](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Lekérdezés jelentések

A lekérdezés időtartama és a lekérdezés vár perspektívák összehasonlíthatja lekérdezés jelentés használatával minden lekérdezés teljesítményére. Ez a jelentés a lekérdezési teljesítmény összehasonlítja a különböző adatbázisok között, és megkönnyíti a rögzítési ponthoz, amely a kijelölt lekérdezést is, amelyet a lassú és adatbázisok.

![Az Azure SQL Analytics-lekérdezések](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Engedélyek

Az Azure SQL Analytics használatához a felhasználóknak meg kell az Olvasó szerepkörhöz az Azure-ban, egy minimális engedéllyel kell rendelkezni. Ez a szerepkör azonban nem engedélyezése a felhasználóknak a lekérdezés szövegének megtekintéséhez, vagy bármely automatikus hangolási műveletek végrehajtása. Megengedőbb szerepkörök az Azure-ban, amelyek lehetővé teszik a megoldás segítségével a lehető legnagyobb mértékben a tulajdonos, közreműködő, SQL-Adatbázisok Közreműködője vagy SQL Server Közreműködője. Érdemes azt is érdemes figyelembe venni, egyéni szerepkörök létrehozása a portálon csak az Azure SQL Analytics használatához szükséges konkrét engedélyeket, és nincs hozzáférése az egyéb erőforrások kezelése.

### <a name="creating-a-custom-role-in-portal"></a>Egyéni szerepkörök létrehozása a portálon

FELISMERVE, hogy az egyes szervezetek kényszerítése szigorú engedély szabályozza az Azure-ban, keresse meg a következő PowerShell-parancsfájl engedélyezése "Az SQL Analytics Alkalmazásfigyelési operátor" az Azure Portalon a minimális olvasási és írási engedély szükséges egy egyéni szerepkör létrehozása a lehető legnagyobb mértékben az Azure SQL Analytics használatával.

Cserélje le a "{SubscriptionId}" az az alábbi szkriptet az Azure-előfizetése Azonosítóját, és hajtsa végre a parancsfájl van bejelentkezve: egy tulajdonosi vagy közreműködői szerepkörhöz az Azure-ban.

   ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription {SubscriptionId}
    $role = Get-AzureRmRoleDefinition -Name Reader
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
    $role.Actions.Add("Microsoft.Sql/servers/databases/*");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzureRmRoleDefinition $role
   ```

Az új szerepkör létrehozása után ez a szerepkör hozzárendelése minden felhasználóhoz, az Azure SQL Analytics használandó egyéni engedélyek megadása szükséges.

## <a name="analyze-data-and-create-alerts"></a>Adatok elemzése és riasztások létrehozása

Adatok elemzése az Azure SQL Analytics alapján [Log Analytics nyelvi](../log-query/get-started-queries.md) az egyéni lekérdezése és jelentéskészítés. Egyéni lekérdezéséhez az adatbázis-erőforrás keresése leírását az elérhető adatokat gyűjtött [metrikák és naplók elérhető](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

A megoldás automatikus riasztások teljesül egy feltétel alapján riasztást Log Analytics-lekérdezés írása alapul. Alább néhány példa a Log Analytics-lekérdezések található, mely riasztás esetén beállíthatja a megoldásban.

### <a name="creating-alerts-for-azure-sql-database"></a>Riasztások létrehozása az Azure SQL Database

Könnyen [riasztásokat hozhat létre](../../azure-monitor/platform/alerts-metric.md) az Azure SQL Database-erőforrásokat érkező adatokat. Íme néhány hasznos [lekérdezések naplózását](../../azure-monitor/log-query/log-query-overview.md) naplóriasztás használható:

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
> - Ez a riasztás létrehozása előtti követelmény, a figyelt adatbázisok stream diagnosztikai metrikák ("Összes metrikák" lehetőség) a megoldáshoz.
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
> - Ez a riasztás létrehozása előtti követelmény, a figyelt adatbázisok stream diagnosztikai metrikák ("Összes metrikák" lehetőség) a megoldáshoz.
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
> - Ez a riasztás létrehozása előtti követelmény, a figyelt adatbázisok stream diagnosztikai metrikák ("Összes metrikák" lehetőség) a megoldáshoz.
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
> - Ez a riasztás létrehozása előtti követelmény, a figyelt adatbázisok stream SQLInsights diagnosztikai napló a megoldáshoz.
> - Ez a lekérdezés egy riasztási szabályt úgy, hogy a Futtatás alert_run_interval gyakorisággal ismétlődő eredmények elkerülése érdekében van szükség. A szabály tűznek ki a riasztást, ha léteznek az eredmények (> 0 eredmény) a lekérdezés kell beállítani.
> - Testre szabhatja a alert_run_interval, megadhatja az időtartományt, ellenőrizze, hogy ha a körülmény stream SQLInsights naplóba a megoldás konfigurált adatbázisok.
> - Testre szabhatja a insights_string rögzítheti a kimenet a Insights alapvető ok elemzési szöveg. Ez a megoldás, amely a meglévő insightsból is használhatja a felhasználói felületen megjelenített ugyanazt a szöveget. Az alábbi lekérdezés segítségével azt is megteheti, tekintse meg az előfizetéshez létrehozott összes Insights szövegét. A lekérdezés kimenetének használatával gyűjtsön a distinct karakterláncok Insights riasztások beállításához.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instance"></a>Riasztások létrehozása a felügyelt példány

#### <a name="managed-instance-storage-is-above-90"></a>Felügyelt példány tárolási 90 % felett van

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
> - Üzem előtti beállítása után ez a riasztás azért, hogy a figyelt felügyelt példány rendelkezik a streamelési ResourceUsageStats napló engedélyezve van a megoldáshoz.
> - Ezt a lekérdezést úgy, hogy a rendszer értesíti ki riasztást, ha léteznek eredmények (> 0 eredmény) a lekérdezésből, így jelölve az, hogy létezik-e a feltétel a felügyelt példányon a riasztási szabály szükséges. A kimenet a tárhelyhasználat százalékos aránya a felügyelt példányon.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Felügyelt példány átlagos processzorhasználatot van 95 % fölé az elmúlt 1 óra

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
> - Üzem előtti beállítása után ez a riasztás azért, hogy a figyelt felügyelt példány rendelkezik a streamelési ResourceUsageStats napló engedélyezve van a megoldáshoz.
> - Ezt a lekérdezést úgy, hogy a rendszer értesíti ki riasztást, ha léteznek eredmények (> 0 eredmény) a lekérdezésből, így jelölve az, hogy létezik-e a feltétel a felügyelt példányon a riasztási szabály szükséges. A kimenet a átlagos kihasználtság százalékos processzorhasználatot meghatározott időszakban a felügyelt példányon.

### <a name="pricing"></a>Díjszabás

Bár a megoldás használata ingyenes, vonatkozik-e az diagnostics telemetriai adatainak fent az adatbetöltés lefoglalva minden hónapban ingyenes egységek felhasználását, lásd: [Log Analytics díjszabása](https://azure.microsoft.com/en-us/pricing/details/monitor). Az adatbetöltés foglalt ingyenes egységek ingyenes figyelés engedélyezése több adatbázis minden hónapban. Vegye figyelembe, hogy a nagyobb számítási feladatok több aktív adatbázisok betöltési inaktív adatbázisokat és további adatokat. Az adathasználat Adatbetöltési a megoldás az OMS-munkaterület kiválasztásával az Azure SQL Analytics, a navigációs menü, és kiválasztja a használat és becsült költségek egyszerűen figyelheti.

## <a name="next-steps"></a>További lépések

- Használat [Naplókeresések](../../azure-monitor/log-query/log-query-overview.md) a Log Analytics az Azure SQL részletes adatainak megtekintéséhez.
- [Saját irányítópult létrehozásával](../../azure-monitor/platform/dashboards.md) Azure SQL-adatainak megjelenítése.
- [Riasztások létrehozása](../../azure-monitor/platform/alerts-overview.md) amikor adott Azure SQL-események történnek.
