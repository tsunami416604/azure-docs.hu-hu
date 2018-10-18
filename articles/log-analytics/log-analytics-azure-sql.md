---
title: Az Azure SQL Analytics megoldás a Log Analytics Rendszereben |} A Microsoft Docs
description: Az Azure SQL Analytics megoldás segítségével az Azure SQL-adatbázisok kezelése
services: log-analytics
documentationcenter: ''
author: danimir
manager: carmonm
ms.reviewer: carlrab
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: v-daljep
ms.component: ''
ms.openlocfilehash: d16f9add2cd31eb5a8db650798c241c3dcf2610f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49379304"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Az Azure SQL Database megfigyelése az Azure SQL Analytics (előzetes verzió)

![Az Azure SQL Analytics szimbólum](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Az Azure SQL Analytics egy felhőalapú megoldást kínál a teljesítmény figyelése Azure SQL Database-adatbázisok, rugalmas készletek és a felügyelt példányok ipari méretekben, és több előfizetésre kiterjedő figyelési. Azt gyűjti elérhetővé, és fontos Azure SQL Database teljesítmény-mérőszámok – teljesítménnyel kapcsolatos hibaelhárítás a beépített intelligenciával.

A megoldással gyűjtött metrikák használatával létrehozhat egyéni figyelési szabályokkal és riasztásokkal. A megoldás segítségével azonosíthatja a problémákat az alkalmazáscsoportokat az egyes rétegben. Adatokat az összes az Azure SQL adatbázisok, rugalmas készletek és adatbázisok szerepelnek a felügyelt példányok egyetlen Log Analytics-munkaterületet a Log Analytics nézeteivel együtt az Azure diagnosztikai metrikák használ. A log Analytics segítségével összegyűjtését, összekapcsolását és megjelenítését a strukturált és strukturálatlan adatok.

A gyakorlati áttekintése az Azure SQL Analytics megoldás használatával és a jellemző használati forgatókönyvei: a beágyazott videót:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Összekapcsolt források

Az Azure SQL Analytics egy felhőben csak a megoldást támogató adatfolyamként diagnostics telemetriai adatainak az Azure SQL Database felügyelt példány adatbázisok és rugalmas készletek figyelése.

A megoldás nem használja az ügynökök a Log Analytics szolgáltatáshoz való csatlakozáshoz, mivel a megoldás nem támogatja a helyszíni SQL Server tárolt vagy a virtuális gépek figyelése, az alábbi tábla a kompatibilitási.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| **[Az Azure Diagnostics](log-analytics-azure-storage.md)** | **Igen** | Az Azure metrika- és naplózási adatok közvetlenül az Azure Log Analytics érkeznek. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A log Analytics egy storage-fiókból az adatok nem olvashatók. |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Nem | A megoldás a közvetlen Windows-ügynökök nem használja. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | A megoldás a közvetlen Linux-ügynökök nem használja. |
| [Az SCOM felügyeleti csoport](log-analytics-om-agents.md) | Nem | A megoldás nem használja az SCOM-ügynöktől a Log Analyticshez való közvetlen kapcsolatot. |

## <a name="configuration"></a>Konfiguráció

Hajtsa végre az alábbi lépéseket az Azure SQL Analytics megoldás hozzáadása az Azure-irányítópulton.

1. Az Azure SQL Analytics megoldás hozzáadni a munkaterülethez [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Az Azure Portalon kattintson a **+ erőforrás létrehozása**, majd keresse meg az **Azure SQL Analytics**.  
    ![Felügyelet és kezelés](./media/log-analytics-azure-sql/monitoring-management.png)
3. Válassza ki **Azure SQL Analytics (előzetes verzió)** a listából
4. Az a **Azure SQL Analytics (előzetes verzió)** területen kattintson a **létrehozás**.  
    ![Létrehozás](./media/log-analytics-azure-sql/portal-create.png)
5. Az a **új megoldás létrehozása** területen létrehozhat új, vagy válasszon ki egy meglévő munkaterületet, adja hozzá a megoldást, és kattintson a kívánt **létrehozás**.

    ![munkaterület hozzáadása](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-elastic-pools-and-managed-instances-to-stream-diagnostics-telemetry"></a>Az Azure SQL Database, a rugalmas készletek és a felügyelt példányok stream diagnostics telemetriai adatainak konfigurálása

Miután létrehozta az Azure SQL Analytics megoldás a munkaterületen, annak érdekében, hogy az Azure SQL Database-adatbázisok, a felügyelt példányok adatbázisai és a rugalmas készletek teljesítményének figyelése kell **konfigurálja** szeretné ezen erőforrások közül Ez a figyelő a diagnostics telemetriai adatainak a megoldáshoz továbbításához.

- Az Azure-diagnosztika engedélyezése az Azure SQL Database, a felügyelt példányok adatbázisai és a rugalmas készletek az [diagnostics telemetriai adatainak az Azure SQL Analytics adatfolyam](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Több Azure-előfizetés konfigurálása
 
Több előfizetés is támogatja, használja a PowerShell-szkript [engedélyezése az Azure resource metrikák naplózás PowerShell-lel](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Adja meg a munkaterület erőforrás-azonosító paramétert egy Azure-előfizetéshez tartozó erőforrások diagnosztikai adatokat küldeni egy munkaterületet egy másik Azure-előfizetésben a parancsfájl végrehajtása közben.

**Példa**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>A megoldás használata

A munkaterület a megoldás hozzáadásakor az Azure SQL Analytics csempére a munkaterületéhez kerül, és akkor jelenik meg, az Áttekintés. A csempe a felügyelt példányok, a megoldás diagnosztikai telemetriáját fogadó Azure SQL-adatbázisok, rugalmas készletek, felügyelt példányok és adatbázisok számát jeleníti meg.

![Az Azure SQL Analytics csempére](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

A megoldás két különböző – egy Azure SQL Database-adatbázisok és rugalmas készletek és a többi nézet a figyelési felügyelt példányt, és a felügyelt példányok adatbázisai figyelés nézetet biztosít.

Az Azure SQL Database-adatbázisok és rugalmas készletek Azure SQL Analytics figyelési irányítópult megtekintéséhez kattintson a csempére felső részén. Az Azure SQL Analytics figyelési irányítópult felügyelt példányt, és a felügyelt példány adatbázisok megtekintéséhez kattintson a csempe alsó részén.

### <a name="viewing-azure-sql-analytics-data"></a>Az Azure SQL Analytics-adatok megtekintése

Az irányítópult programban különböző szempontok szerint keresztül figyelt összes adatbázis áttekintését tartalmazza. A programban különböző szempontok szerint működjön engedélyeznie kell megfelelő metrikákat vagy naplókat az SQL-erőforrások Azure Log Analytics-munkaterületre is streamelhetők.

Vegye figyelembe, hogy néhány metrikákat vagy naplókat az Azure Log Analytics nem streameli, ha a megoldás a csempék nem tölti fel figyelési információkat.

### <a name="azure-sql-database-and-elastic-pool-view"></a>Az Azure SQL Database és a rugalmas készlet megtekintése

Az Azure SQL Analytics csempére az Azure SQL Database, és a rugalmas készletek van kijelölve, a figyelési irányítópult látható.

![Az Azure SQL Analytics áttekintése](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

![Az Azure SQL Analytics időtúllépések](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Ebben a nézetben minden egyes perspektívát nyújt összefoglaló előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázisszintű. Emellett minden szempontból bemutatja egy perspektíva vonatkozó a jelentésben a jobb oldalon. A Lehatolás előfizetés, a kiszolgáló, a készlet vagy az adatbázis kiválasztása a listából továbbra is.

### <a name="managed-instance-and-databases-in-managed-instance-view"></a>A felügyelt példánynak és a felügyelt példány adatbázisai megtekintése

Felügyelt példány az Azure SQL Analytics csempére, és a felügyelt példány adatbázisok be van jelölve, a figyelési irányítópult látható.

![Az Azure SQL Analytics áttekintése](./media/log-analytics-azure-sql/azure-sql-sol-overview-mi.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

Részleteket a felügyelt példány kihasználtságáról, a benne található adatbázisok és a példány között végrehajtott lekérdezések vonatkozó telemetriai adatokat a felügyelt példány nézetek kijelölése jeleníti meg.

![Az Azure SQL Analytics időtúllépések](./media/log-analytics-azure-sql/azure-sql-sol-metrics-mi.png)

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

Az Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) lehetővé teszi, hogy tudja, mi történik az Azure SQL Database és a felügyelt példány adatbázisok teljesítményét. Gyűjtött összes Intelligent Insights is formájában jelenik meg, és az elemzések szempontjából keresztül érhetők el.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rugalmas készlet és az adatbázis-jelentések

SQL-adatbázisok és rugalmas készletek rendelkezik a saját konkrét jelentések megjelenítése az erőforrás a megadott időtartam alatt gyűjtött összes adat.

![Az Azure SQL Analytics adatbázis](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Az Azure SQL rugalmas készlet](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Lekérdezés jelentések

A lekérdezés időtartama és a lekérdezés vár perspektívák összehasonlíthatja lekérdezés jelentés használatával minden lekérdezés teljesítményére. Ez a jelentés a lekérdezési teljesítmény összehasonlítja a különböző adatbázisok között, és megkönnyíti a rögzítési ponthoz, amely a kijelölt lekérdezést is, amelyet a lassú és adatbázisok.

![Az Azure SQL Analytics-lekérdezések](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="pricing"></a>Díjszabás

Bár a megoldás használata ingyenes, vonatkozik-e az diagnostics telemetriai adatainak fent az adatbetöltés lefoglalva minden hónapban ingyenes egységek felhasználását, lásd: [Log Analytics díjszabása](https://azure.microsoft.com/en-us/pricing/details/monitor). Az adatbetöltés foglalt ingyenes egységek ingyenes figyelés engedélyezése több adatbázis minden hónapban. Vegye figyelembe, hogy a nagyobb számítási feladatok több aktív adatbázisok inaktív adatbázisokat és több adatot képes feldolgozni. Az adathasználat Adatbetöltési a megoldás az OMS-munkaterület kiválasztásával az Azure SQL Analytics, a navigációs menü, és kiválasztja a használat és becsült költségek egyszerűen figyelheti.

### <a name="analyze-data-and-create-alerts"></a>Adatok elemzése és riasztások létrehozása

### <a name="creating-alerts-for-azure-sql-database"></a>Riasztások létrehozása az Azure SQL Database

Könnyen [riasztásokat hozhat létre](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) az Azure SQL Database-erőforrásokat érkező adatokat. Íme néhány hasznos [naplóbeli keresés](log-analytics-log-searches.md) lekérdezéseket, amelyek egy riasztás használhatja:

*Az Azure SQL Database magas CPU*

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

*Az Azure SQL Database rugalmas készletei magas CPU*

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

*Az Azure SQL Database storage átlag felett 95 %-ot az elmúlt 1 óra*

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

*Az Intelligent insights riasztás*

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

* A felügyelt példány tárolási 90 % felett van

```
let storage_percentage_treshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_treshold
```

> [!NOTE]
> - Üzem előtti beállítása után ez a riasztás azért, hogy a figyelt felügyelt példány rendelkezik a streamelési ResourceUsageStats napló engedélyezve van a megoldáshoz.
> - Ezt a lekérdezést úgy, hogy a rendszer értesíti ki riasztást, ha léteznek eredmények (> 0 eredmény) a lekérdezésből, így jelölve az, hogy létezik-e a feltétel a felügyelt példányon a riasztási szabály szükséges. A kimenet a tárhelyhasználat százalékos aránya a felügyelt példányon.

## <a name="next-steps"></a>További lépések

- Használat [Naplókeresések](log-analytics-log-searches.md) a Log Analytics az Azure SQL részletes adatainak megtekintéséhez.
- [Saját irányítópult létrehozásával](log-analytics-dashboards.md) Azure SQL-adatainak megjelenítése.
- [Riasztások létrehozása](log-analytics-alerts.md) amikor adott Azure SQL-események történnek.
