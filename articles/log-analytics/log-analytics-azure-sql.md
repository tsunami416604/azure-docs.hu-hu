---
title: A Naplóelemzési Azure SQL elemzési megoldások |} Microsoft Docs
description: Az Azure SQL elemzési megoldások kezelheti az Azure SQL Database adatbázisok.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 722a10e853f6d61bb5349e92754954e3bb199225
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Azure SQL adatbázis Azure SQL elemzés (előzetes verzió) Naplóelemzési figyelése

![Az Azure SQL elemzés szimbólum](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Az Azure SQL elemzési megoldás az Azure Naplóelemzés gyűjt, és fontos SQL Azure teljesítménymutatók visualizes. A metrikák gyűjtött megoldás segítségével létrehozhat egyéni ellenőrzési szabályok és értesítések. Figyelheti, hogy az Azure SQL Database és a rugalmas készlet metrikák több Azure-előfizetések és rugalmas készletek, és jelenítheti meg őket. A megoldás is segítséget az alkalmazás-készlet minden egyes rétegben problémák azonosításához.  Használja [Azure diagnosztikai metrikák](log-analytics-azure-storage.md) Log Analytics-nézetet megjeleníteni az adatokat az Azure SQL-adatbázisok és rugalmas készletek egyetlen Naplóelemzési munkaterület együtt.

Jelenleg ez preview megoldás legfeljebb 150 000 Azure SQL-adatbázisok és 5000 SQL rugalmas készletek / munkaterület támogatja.

Az Azure SQL elemzési megoldás, mások számára elérhető Naplóelemzési, például segítségével megfigyelheti és az Azure-erőforrások értesítéseket állapotával kapcsolatos – ebben az esetben az Azure SQL Database. A Microsoft Azure SQL Database egy méretezhető relációs adatbázis-szolgáltatás, amely az Azure felhőben futó alkalmazások ismerős SQL-kiszolgáló-szerű képességeket biztosít. A Naplóelemzési segítségével gyűjtése, összefüggéseket és strukturált és strukturálatlan adatok megjelenítése.

Az Azure SQL elemzési megoldás használatával gyakorlati áttekintés és a jellemző használati forgatókönyvei tekintse meg a beágyazott videó:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Összekapcsolt források

Az Azure SQL elemzési megoldások ügynökök nem használ, a Naplóelemzés szolgáltatáshoz való kapcsolódáshoz.

Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Nem | A közvetlen Windows-ügynökök nem használják a megoldás. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | Közvetlen Linux-ügynökök nem használják a megoldás. |
| [SCOM felügyeleti csoport](log-analytics-om-agents.md) | Nem | Közvetlen kapcsolat az SCOM-ügynököt a szolgáltatáshoz a megoldás nem használja. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A Naplóelemzési nem beolvasni az adatokat egy tárfiókot. |
| [Azure Diagnostics](log-analytics-azure-storage.md) | Igen | Azure metrika és naplózási adatok küldése a Naplóelemzési közvetlenül az Azure-ban. |

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetéssel. Ha még nincs fiókja, létrehozhat egy a [szabad](https://azure.microsoft.com/free/).
- A Naplóelemzési munkaterület. Egy meglévő is használhatja, vagy beállíthatja a [hozzon létre egy újat](log-analytics-quick-create-workspace.md) Ez a megoldás használata előtt.
- Azure Diagnostics engedélyezése az Azure SQL-adatbázisok és rugalmas készletek és [úgy konfigurálja azokat az adatokat küldeni a Naplóelemzési](../sql-database/sql-database-metrics-diag-logging.md).

## <a name="configuration"></a>Konfiguráció

A következő lépésekkel adja hozzá az Azure SQL elemzési megoldások a munkaterületre.

1. Az Azure SQL elemzési megoldások hozzáadása a munkaterület [Azure piactér](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. Az Azure portálon kattintson **hozzon létre egy erőforrást** > **figyelés + felügyeleti**.  
    ![Felügyelet és kezelés](./media/log-analytics-azure-sql/monitoring-management.png)
3. Az a **figyelés + felügyeleti** listában kattintson **láthatja az összes**.
4. Az a **ajánlott** listában, kattintson **további**, majd új listájában keresse meg **Azure SQL elemzés (előzetes verzió)** meg és jelölje ki.  
    ![Az Azure SQL elemzési megoldások](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. Az a **Azure SQL elemzés (előzetes verzió)** területen kattintson a **létrehozása**.  
    ![Létrehozás](./media/log-analytics-azure-sql/portal-create.png)
6. Az a **hozzon létre új megoldás** területen, válassza ki, amely hozzá szeretné adni, hogy a munkaterület, és kattintson **létrehozása**.  
    ![munkaterület felvétele](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Több Azure-előfizetések beállítása

Több előfizetések támogatásához a PowerShell parancsfájl használatát [engedélyezése Azure erőforrás metrikáit naplózás PowerShell-lel](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Adja meg a munkaterület-azonosító paramétert, a diagnosztikai adatokat küldeni a források egy Azure-előfizetésben más Azure-előfizetések munkaterületeinek a parancsfájl végrehajtása közben.

**Példa**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>A megoldás használata

A megoldás a munkaterülethez való hozzáadásakor az Azure SQL elemzés csempe hozzáadódik a munkaterület, és megjeleníti a áttekintése. A csempe az Azure SQL-adatbázisok és az Azure SQL rugalmas készletek a megoldás csatlakozik a számát mutatja.

![Az Azure SQL elemzés csempe](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Azure SQL analitikai adatok megtekintése

Kattintson a **Azure SQL elemzés** csempére kattintva nyissa meg az Azure SQL-elemzések irányítópultján. Az irányítópult különböző szempontok szerint keresztül figyelt összes adatbázis áttekintését tartalmazza. Különböző szempontok szerint működjön engedélyeznie kell megfelelő metrikák vagy naplók az Azure Naplóelemzés munkaterületet közzétett az SQL-erőforrások.

![Az Azure SQL elemzés áttekintése](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Az adott perspektíva kiválasztásával a csempéket, részletes jelentés megnyitása. A perspektíva kijelölése után a részletes jelentés meg van nyitva.

![Az Azure SQL elemzés időtúllépések](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Minden szempontból ezekkel az előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázis szintje biztosít. Emellett minden perspektíva látható perspektívát a jelentés az adott jobb. A részletezés előfizetés, a kiszolgáló, a készlet vagy az adatbázis listáról továbbra is.

| Perspektíva | Leírás |
| --- | --- |
| Erőforrás típus szerint | Megjeleníti a figyelt összes erőforrást perspektívát. Részletezés biztosít DTU és GB metrikák összegzését. |
| Insights | Hierarchikus leásási biztosít az intelligens Insights. További tudnivalók az intelligens insights. |
| Hibák | Hierarchikus leásási biztosít, és ismételje meg az adatbázisok SQL hibákba. |
| Időtúllépések | Hierarchikus leásási nyújt az SQL-időtúllépések, az adatbázisok történt. |
| Blockings | Hierarchikus leásási biztosít, és ismételje meg az adatbázisok SQL blockings be. |
| Adatbázis vár | Hierarchikus leásási biztosít az SQL várakozási statisztikák az adatbázis szintjén. A teljes várakozási idő és a várakozási idő várakozási típusonkénti összegzését tartalmazza. |
| lekérdezés időtartama | Hierarchikus leásási biztosítja azokat a lekérdezés-végrehajtási statisztikákról, például a lekérdezés időtartama, a CPU-használat, a adat IO kihasználtsága, a napló IO kihasználtsága. |
| Lekérdezés | Hierarchikus leásási biztosítja azokat a lekérdezés várakozási statisztika várakozási kategória szerint. |

### <a name="intelligent-insights-report"></a>Intelligens Insights jelentés

Az Azure SQL Database [intelligens Insights](../sql-database/sql-database-intelligent-insights.md) jelzi, mi történik az adatbázis-teljesítménnyel. Gyűjti az összes intelligens Insights ábrázolt, és az elemzések szempontjából keresztül érhetők el.

![Azure SQL Analytics Insights](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>A rugalmas készlet-és adatbázis

Rugalmas készletek és adatbázisokat is rendelkezzen a saját konkrét jelentéseket, amelyek megjelenítése az erőforrás a megadott idő alatt gyűjtött összes adat.

![Az Azure SQL elemzés adatbázis](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Az Azure SQL elemzés rugalmas készlet](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Lekérdezés-jelentések

A lekérdezés időtartam és a lekérdezés vár perspektívák hozhatók lekérdezés jelentés használatával lekérdezés teljesítményét. Ez a jelentés a lekérdezési teljesítmény hasonlítja különböző adatbázist, és megkönnyíti a rögzítési ponthoz az adatbázisok, hajtsa végre a kijelölt lekérdezés jól és azokat, amelyek lassú.

![Az Azure SQL elemzési lekérdezések](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Adatok elemzése és értesítések

Egyszerűen [létre riasztásokat](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) az Azure SQL adatbázis-erőforrások érkező adatokat. Íme néhány hasznos [naplófájl-keresési](log-analytics-log-searches.md) lekérdezéseket, amelyekkel a napló-riasztások használata:



*Az Azure SQL Database magas DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Magas, az Azure SQL Database rugalmas készlet DTU*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>További lépések

- Használjon [napló keresések](log-analytics-log-searches.md) a Log Analyticshez Azure SQL részletes adatainak megtekintéséhez.
- [Hozzon létre egy saját irányítópultok](log-analytics-dashboards.md) Azure SQL-adatok jelennek meg.
- [Hozzon létre a riasztások](log-analytics-alerts.md) Ha meghatározott Azure SQL-esemény történik.
