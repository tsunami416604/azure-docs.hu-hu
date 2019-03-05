---
title: Elemezheti a naplófájlok adatait az Azure Monitor |} A Microsoft Docs
description: Naplóadatok lekérése az Azure Monitor log lekérdezés van szüksége.  Ez a cikk azt ismerteti, hogyan új naplózási lekérdezést használ az Azure monitorban, és biztosítja a fogalmakat, amelyek egy létrehozása előtt ismernie kell.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: 3e0cc41b2bb9b5c8193e64ccec767e551b3525e1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307394"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Elemezheti a naplófájlok adatait az Azure monitorban

Log Analytics-munkaterületet, amely alapján a tárolt naplóadatokat az Azure Monitor által gyűjtött [Azure adatkezelő](/azure/data-explorer). Különböző forrásokból származó telemetriai adatokat gyűjti és használja a [Kusto-lekérdezés nyelvi](/azure/kusto/query) beolvasni a és elemezhet adatokat az adatkezelő használják.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="log-queries"></a>Naplólekérdezések

A napló lekérdezése bármely naplóadatokat az Azure Monitor van szüksége.  Van-e [adatelemzés a portálon](portals.md), [egy riasztási szabály konfigurálása](../platform/alerts-metric.md) egy adott feltétel, vagy lekérése során adatokat az értesítést a [Azure Monitor naplók API](https://dev.loganalytics.io/) , a lekérdezés használandó adja meg a kívánt adatokat.  Ez a cikk ismerteti az Azure Monitor log-lekérdezések használata, és biztosítja a fogalmakat, amelyek egy létrehozása előtt ismerje meg.



## <a name="where-log-queries-are-used"></a>Ha a napló lekérdezést használ


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A különböző módon, hogy az Azure Monitor lekérdezések használja a következők:


- **Portal.** Interaktív elemzés céljából, a naplóadatok hajthat végre a [az Azure portal](portals.md).  Ez lehetővé teszi, hogy módosítsa a lekérdezést, és elemezze az eredményeket a különböző formátumok és a Vizualizációk.  
- **Riasztási szabályok.** [Riasztási szabályok](../platform/alerts-overview.md) proaktív módon azonosíthatja a problémákat a munkaterületen lévő adatai.  Minden riasztási szabály naplókeresést, amely rendszeres időközönként automatikusan fut alapul.  Határozza meg, ha egy riasztást kell létrehozni a ellenőrzik az eredményeket.
- **Az irányítópultok.** Kitűzheti, minden lekérdezés eredményeit egy [Azure irányítópultján](../learn/tutorial-logs-dashboards.md) Ez lehetővé teszi annak együtt megjelenítheti a napló-és metrikaadatokat, és szükség esetén megoszthatja más Azure-felhasználóval. 
- **Nézetek.**  Adatok foglalandó felhasználói irányítópultokat és vizualizációkat hozhat létre [adatforrásnézet-tervezőből](../platform/view-designer.md).  Naplólekérdezések adja meg az adatok által használt [csempék](../platform/view-designer-tiles.md) és [Vizualizáció részek](../platform/view-designer-parts.md) az egyes nézetek.  

- **Exportálás.**  Importálásakor Teljesítménynapló-adatok az Azure Monitor az Excelbe vagy [Power BI](../platform/powerbi.md), az adatok exportálása meghatározásához log lekérdezés létrehozása.
- **PowerShell.** PowerShell-szkriptet a parancssorban vagy egy Azure Automation-runbook által használt futtatható [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) naplóadatok lekérése az Azure Monitor.  Ez a parancsmag egy lekérdezést a lekérni kívánt meghatározásához szükséges.
- **Az Azure Monitor naplók API.**  A [Azure Monitor naplók API](../platform/alerts-overview.md) lehetővé teszi, hogy bármilyen REST API-ügyfél naplóadatok lekérése a munkaterületen.  Az API-kérelem tartalmaz egy lekérdezést, amely a lekérni kívánt meghatározni az Azure Monitor vonatkozóan fut le.

![Naplókeresések](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>A lekérdezés írása
Használja az Azure Monitor [egy a Kusto-lekérdezés nyelvi verzióját](get-started-queries.md) lekérésére és elemezheti a naplófájlok adatait egy számos különböző módon.  Általában megkezdi az alapvető lekérdezések, és majd a Speciális függvények igényeinek egyre összetettebb halad.

Az alapszintű struktúrát egy lekérdezés az operátorok függőleges vonás karakterrel elválasztott sorozatát követ forrástábla `|`.  Láncolhatja össze több operátor pontosítsa az adatokat, és végezze el a speciális funkciók.

Például tegyük fel, hogy keresse meg a legtöbb hiba eseményekkel rendelkező első tíz számítógépek az elmúlt nap során.

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

Vagy esetleg szeretné keresése a számítógépeken, amelyek még nem volt a szívverés az elmúlt egy napon belül.

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

Mi a helyzet vonaldiagramot a processzorhasználat, minden számítógépen, a múlt héten?

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

Megjelenik a gyors a példákon, függetlenül attól, milyen típusú adatok, amelyek dolgozik, a lekérdezés szerkezete hasonlít.  Oszthatja azt, ahol a kapott adatokban egyik parancs keresztül zajlik a folyamat a következő parancsot a különálló lépésre.

Adatok Log Analytics-munkaterületek között az adott előfizetéshez tartozó is lekérdezheti.

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Az Azure Monitor log-adatok
Ha egy lekérdezést hoz létre, akkor meghozatalához melyik táblákhoz, amely a keresett adatokat tartalmaznak. Különböző típusú adatok meg vannak osztva az egyes dedikált táblák [Log Analytics-munkaterület](../learn/quick-create-workspace.md).  Különböző adatforrásokhoz tartozó dokumentációk az adattípus, amely létrehozza a nevére, és az egyes a tulajdonságok leírását tartalmazza.  Több lekérdezés csak egyetlen tábla adatait, de mások használatával számos lehetőség több tábla adatait tartalmazza.

Miközben [Application Insights](../app/app-insights-overview.md) tárolók alkalmazásadatok, például kérelmeket, kivételeket, nyomkövetéseket, és használat az Azure Monitor naplóira, ezeket az adatokat, mint a naplózási adatokat egy másik partíció tárolja. Ugyanazt a lekérdezési nyelvet használja az adatok elérésére, de kell használnia a [Application Insights-konzol](../app/analytics.md) vagy [Application Insights REST API](https://dev.applicationinsights.io/) az eléréséhez. Használhat [cross-erőforrások lekérdezések](../log-query/cross-workspace-query.md) úgy, hogy más naplóadatokat az Azure Monitor az Application Insights-adatok.


![Táblák](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>További lépések
- A megismerése [létrehozásához és szerkesztéséhez a Log Analytics naplóbeli kereséseivel](../log-query/portals.md).
- Tekintse meg a [oktatóanyag lekérdezések írásáról](../log-query/get-started-queries.md) az új lekérdezési nyelv segítségével.
