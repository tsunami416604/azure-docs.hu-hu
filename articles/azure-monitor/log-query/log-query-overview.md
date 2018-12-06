---
title: Az Azure monitorban Log Analytics-adatok elemzése |} A Microsoft Docs
description: Adatok lekérése a Log Analytics-Naplókeresés van szüksége.  Ez a cikk ismerteti a keresések szolgálnak a Log Analytics új naplófájl, és megadja a fogalmakat, amelyek egy létrehozása előtt ismernie kell.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 3015db350b8011ccd328369732c5af3fa028a438
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963555"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Az Azure monitorban Log Analytics-adatok elemzése

Log Analytics-munkaterületet, amely alapján a tárolt naplóadatokat az Azure Monitor által gyűjtött [Azure adatkezelő](/azure/data-explorer). Különböző forrásokból származó telemetriai adatokat gyűjti és használja a [lekérdezési nyelv az adatkezelő](/azure/kusto/query) lekérése és elemzéséhez.

> [!NOTE]
> A log Analytics korábban volt kezelni saját szolgáltatása az Azure-ban. Az Azure Monitor egy részének most számít és a tárolási és elemzése a lekérdezési nyelv segítségével naplóadatok összpontosít. Log Analytics Windows és Linux-ügynökök az adatgyűjtés, a nézetek megjelenítése a meglévő adatok és riasztások segítségével proaktívan kaphat értesítést problémák, például részének tekintendők funkciók nem változtak, de most az Azure Monitor részének tekinthetők.



## <a name="log-queries"></a>Napló lekérdezések

Adatok lekérése a Log Analytics naplózási lekérdezés van szüksége.  Van-e [adatelemzés a portálon](../../azure-monitor/log-query/portals.md), [egy riasztási szabály konfigurálása](../../monitoring-and-diagnostics/alert-metric.md) egy adott feltétel, vagy lekérése során adatokat az értesítést a [Log Analytics API](https://dev.loganalytics.io/), akkor egy lekérdezést fogja használni, adja meg a kívánt adatokat.  Ez a cikk ismerteti a Log Analytics naplóbeli lekérdezések használata, és megadja a fogalmakat, amelyek egy létrehozása előtt ismerje meg.



## <a name="where-log-queries-are-used"></a>Ha a napló lekérdezést használ

A különböző módon, hogy a Log Analytics lekérdezések használja a következők:

- **Portálok.** Interaktív elemzés céljából, a naplóadatok hajthat végre a [az Azure portal](../../azure-monitor/log-query/portals.md).  Ez lehetővé teszi, hogy módosítsa a lekérdezést, és elemezze az eredményeket a különböző formátumok és a Vizualizációk.  
- **Riasztási szabályok.** [Riasztási szabályok](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktív módon azonosíthatja a problémákat a munkaterületen lévő adatai.  Minden riasztási szabály naplókeresést, amely rendszeres időközönként automatikusan fut alapul.  Határozza meg, ha egy riasztást kell létrehozni a ellenőrzik az eredményeket.
- **Az irányítópultok.** Kitűzheti, minden lekérdezés eredményeit egy [Azure irányítópultján](../../azure-monitor/platform/dashboards.md) Ez lehetővé teszi annak együtt megjelenítheti a napló-és metrikaadatokat, és szükség esetén megoszthatja más Azure-felhasználóval. 
- **Nézetek.**  Adatok foglalandó felhasználói irányítópultokat és vizualizációkat hozhat létre [adatforrásnézet-tervezőből](../../azure-monitor/platform/view-designer.md).  Naplólekérdezések adja meg az adatok által használt [csempék](../../azure-monitor/platform/view-designer-tiles.md) és [Vizualizáció részek](../../azure-monitor/platform/view-designer-parts.md) az egyes nézetek.  
- **Exportálás.**  Ha importál adatokat a Log Analytics-munkaterületet az Excel vagy [Power BI](../../log-analytics/log-analytics-powerbi.md), az adatok exportálása meghatározásához log lekérdezés létrehozása.
- **PowerShell.** PowerShell-szkriptet a parancssorban vagy egy Azure Automation-runbook által használt futtatható [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) adatokat lekérni a Log Analytics.  Ez a parancsmag egy lekérdezést a lekérni kívánt meghatározásához szükséges.
- **Log Analytics API-t.**  A [Log Analytics naplóbeli keresési API](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) lehetővé teszi, hogy bármilyen REST API-ügyfél naplóadatok lekérése a munkaterületen.  Az API-kérelem tartalmaz egy lekérdezést, amely a lekérni kívánt meghatározni a Log Analytics vonatkozóan fut le.

![Naplókeresések](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>A lekérdezés írása
Log Analytics használati [az adatkezelő lekérdezési nyelv egy verziója](../../azure-monitor/log-query/get-started-queries.md) lekérésére és elemezheti a naplófájlok adatait egy számos különböző módon.  Általában megkezdi az alapvető lekérdezések, és majd a Speciális függvények igényeinek egyre összetettebb halad.

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

## <a name="how-log-analytics-data-is-organized"></a>Hogyan vannak rendszerezve a Log Analytics-adatok
Ha egy lekérdezést hoz létre, akkor meghozatalához melyik táblákhoz, amely a keresett adatokat tartalmaznak. Különböző típusú adatok meg vannak osztva az egyes dedikált táblák [Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md).  Különböző adatforrásokhoz tartozó dokumentációk az adattípus, amely létrehozza a nevére, és az egyes a tulajdonságok leírását tartalmazza.  Több lekérdezés csak egyetlen tábla adatait, de mások használatával számos lehetőség több tábla adatait tartalmazza.

Miközben [Application Insights](../../application-insights/app-insights-overview.md) tárolók adatokat, mint a kérelmeket, kivételeket, nyomkövetéseket és a Log Analytics, az adatok használati tárolva van, mint a naplózási adatokat egy másik partíció. Ugyanazt a lekérdezési nyelvet használja az adatok elérésére, de kell használnia a [Application Insights-konzol](../../application-insights/app-insights-analytics.md) vagy [Application Insights REST API](https://dev.applicationinsights.io/) az eléréséhez. Használhat [cross-erőforrások lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md) úgy, hogy más adatokat a Log Analytics az Application Insights-adatok.


![Táblák](media/log-query-overview/queries-tables.png)







## <a name="next-steps"></a>További lépések

- További információ a [naplókeresések létrehozására és szerkesztésére használt portálok](../../azure-monitor/log-query/portals.md).
- Tekintse meg a [oktatóanyag lekérdezések írásáról](../../azure-monitor/log-query/get-started-queries.md) az új lekérdezési nyelv segítségével.
