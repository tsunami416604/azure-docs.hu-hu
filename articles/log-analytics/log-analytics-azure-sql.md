---
title: Az Azure SQL Analytics megoldás a Log Analytics Rendszereben |} A Microsoft Docs
description: Az Azure SQL Analytics megoldás segítségével az Azure SQL-adatbázisok kezelése
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 440e16416b8567178c61c3d6ce2155e0e331521c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216325"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>A figyelő Azure SQL Database-adatbázisok Azure SQL Analytics (előzetes verzió) használatával

![Az Azure SQL Analytics szimbólum](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Az Azure SQL Analytics figyelési megoldást figyelése Azure SQL Database-adatbázisok teljesítményét, több rugalmas készletek és az előfizetések felhő. Azt gyűjti elérhetővé, és fontos Azure SQL Database teljesítmény-mérőszámok, teljesítménnyel kapcsolatos hibaelhárítás felül a beépített intelligenciával. 

A megoldással gyűjtött metrikák használatával létrehozhat egyéni figyelési szabályokkal és riasztásokkal. A megoldás segítségével azonosíthatja a problémákat az alkalmazáscsoportokat az egyes rétegben. Az Azure SQL Database-adatbázisok és a egy Log Analytics-munkaterület az a rugalmas készletek kapcsolatos adatokat a Log Analytics nézeteivel együtt az Azure diagnosztikai metrikák használ. A log Analytics segítségével összegyűjtését, összekapcsolását és megjelenítését a strukturált és strukturálatlan adatok.

Jelenleg ez a megoldás előzetes legfeljebb 150 000 Azure SQL Database-adatbázisok és 5000 rugalmas SQL-készletek Munkaállomásonként.

A gyakorlati áttekintése az Azure SQL Analytics megoldás használatával és a jellemző használati forgatókönyvei: a beágyazott videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Összekapcsolt források

Az Azure SQL Analytics megoldás támogató streamelési diagnostics telemetriai adatainak az Azure SQL Database-adatbázisok és rugalmas készletek figyelése felhő. Azt nem használ ügynökök a Log Analytics szolgáltatáshoz való csatlakozáshoz, a megoldás nem támogatja a Windows, Linux-csatlakozási vagy az SCOM-erőforrásokat, tekintse meg az alábbi táblázat a kompatibilitási.

| Összekapcsolt forrás | Támogatás | Leírás |
| --- | --- | --- |
| **[Az Azure Diagnostics](log-analytics-azure-storage.md)** | **Igen** | Az Azure metrika- és naplózási adatok közvetlenül az Azure Log Analytics érkeznek. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A log Analytics nem beolvashatja az adatokat a tárfiókból. |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Nem | A közvetlen Windows-ügynökök nem használ a megoldással. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | Közvetlen Linux-ügynökök nem használ a megoldással. |
| [Az SCOM felügyeleti csoport](log-analytics-om-agents.md) | Nem | A megoldás nem használja az SCOM-ügynöktől a Log Analyticshez való közvetlen kapcsolatot. |

## <a name="configuration"></a>Konfiguráció

A következő lépésekkel adhat hozzá az Azure SQL Analytics megoldás a munkaterülethez.

1. Az Azure SQL Analytics megoldás hozzáadni a munkaterülethez [Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. Az Azure Portalon kattintson a **+ erőforrás létrehozása**, majd keresse meg az **Azure SQL Analytics**.  
    ![Felügyelet és kezelés](./media/log-analytics-azure-sql/monitoring-management.png)
3. Válassza ki **Azure SQL Analytics (előzetes verzió)** a listából
4. Az a **Azure SQL Analytics (előzetes verzió)** területen kattintson a **létrehozás**.  
    ![Létrehozás](./media/log-analytics-azure-sql/portal-create.png)
5. Az a **új megoldás létrehozása** területen létrehozhat új, vagy válasszon ki egy meglévő munkaterületet, adja hozzá a megoldást, és kattintson a kívánt **létrehozás**.  
    ![munkaterület hozzáadása](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Stream diagnostics telemetriai adatainak az Azure SQL Database-adatbázisok és rugalmas készletek konfigurálása

Ha létrehozta az Azure SQL Analytics megoldás a munkaterületen, annak érdekében, hogy az Azure SQL Database és/vagy rugalmas készletek teljesítményének figyelése kell **konfigurálja** Azure SQL Database és a rugalmas készlet egyenlő erőforrás szeretné a diagnostics telemetriai adatainak a megoldáshoz streamelésére figyelésére.

- Az Azure-diagnosztika engedélyezése az Azure SQL Database-adatbázisok és rugalmas készletek és [konfigurálhatók úgy, hogy küldik az adatokat a Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

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

A munkaterület a megoldás hozzáadásakor az Azure SQL Analytics csempére a munkaterületéhez kerül, és akkor jelenik meg, az Áttekintés. A csempe az Azure SQL Database-adatbázisok és rugalmas készletek Azure SQL a megoldáshoz csatlakoztatott számát jeleníti meg.

![Az Azure SQL Analytics csempére](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Az Azure SQL Analytics-adatok megtekintése

Kattintson a **Azure SQL Analytics** csempére kattintva nyissa meg az Azure SQL Analytics az irányítópultot. Az irányítópult programban különböző szempontok szerint keresztül figyelt összes adatbázis áttekintését tartalmazza. A programban különböző szempontok szerint működjön engedélyeznie kell megfelelő metrikákat vagy naplókat az SQL-erőforrások Azure Log Analytics-munkaterületre is streamelhetők.

![Az Azure SQL Analytics áttekintése](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Kiválasztásával a csempéket, megnyílik egy részletes jelentés az adott szempontjából. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

![Az Azure SQL Analytics időtúllépések](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Minden egyes perspektíva összegzéseket előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázisszintű nyújt. Emellett minden szempontból bemutatja egy perspektíva vonatkozó a jelentésben a jobb oldalon. A Lehatolás előfizetés, a kiszolgáló, a készlet vagy az adatbázis kiválasztása a listából továbbra is.

| Perspektíva | Leírás |
| --- | --- |
| Erőforrás típusa szerint | Adott összes, a figyelt erőforrások szempontjából. Részletezés a dtu-k és GB-os metrikák összegzést tartalmaz. |
| Megállapítások | Hierarchikus Lehatolás biztosít intelligens elemzésekké. További információ az intelligent insights. |
| Hibák | Hierarchikus Lehatolás biztosít, és ismételje meg az adatbázisok SQL hibákká. |
| Időtúllépések | Hierarchikus Lehatolás biztosít az SQL-időtúllépések, az adatbázisok történt. |
| Blockings | Hierarchikus Lehatolás biztosít, és ismételje meg az adatbázisok SQL blockings be. |
| Adatbázis-várakozások | SQL várakozási statisztikák adatbázisszintű hierarchikus Lehatolás szolgál kapcsolatban. Teljes várakozási idő és a várakozási idő várakozási típusonként összegzéseket tartalmaz. |
| Lekérdezések időtartama | Hierarchikus Lehatolás biztosít, például a lekérdezések időtartama, CPU-használat, adat IO kihasználtsága, naplózási IO kihasználtsága lekérdezés végrehajtási statisztikák. |
| Lekérdezési várakozások | Hierarchikus Lehatolás biztosít, a lekérdezés várakozási statisztikák várakozási kategória szerint. |

### <a name="intelligent-insights-report"></a>Intelligent Insights jelentés

Az Azure SQL Database [Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) jelzi, hogy mi történik az adatbázis teljesítményét. Gyűjtött összes Intelligent Insights is formájában jelenik meg, és az elemzések szempontjából keresztül érhetők el.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Rugalmas készlet és az adatbázis-jelentések

Rugalmas készletek és adatbázisok is rendelkezik saját adott jelentéseket, amelyeket az erőforrás a megadott időtartam alatt gyűjtött összes adat megjelenítéséhez.

![Az Azure SQL Analytics adatbázis](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Az Azure SQL Analytics rugalmas készlet](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Lekérdezés jelentések

A lekérdezés időtartama és a lekérdezés vár perspektívák összehasonlíthatja lekérdezés jelentés használatával minden lekérdezés teljesítményére. Ez a jelentés a lekérdezési teljesítmény összehasonlítja a különböző adatbázisok között, és megkönnyíti a rögzítési ponthoz, amely a kijelölt lekérdezést is, amelyet a lassú és adatbázisok.

![Az Azure SQL Analytics-lekérdezések](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Adatok elemzése és riasztások létrehozása

Könnyen [riasztásokat hozhat létre](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) az Azure SQL Database-erőforrásokat érkező adatokat. Íme néhány hasznos [naplóbeli keresés](log-analytics-log-searches.md) lekérdezéseket, amelyek egy riasztás használhatja:



*Az Azure SQL Database magas DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Az Azure SQL Database rugalmas készlet magas DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>További lépések

- Használat [Naplókeresések](log-analytics-log-searches.md) a Log Analytics az Azure SQL részletes adatainak megtekintéséhez.
- [Saját irányítópult létrehozásával](log-analytics-dashboards.md) Azure SQL-adatainak megjelenítése.
- [Riasztások létrehozása](log-analytics-alerts.md) amikor adott Azure SQL-események történnek.
