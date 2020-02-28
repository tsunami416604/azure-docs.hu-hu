---
title: A Azure Monitor naplózásának áttekintése | Microsoft Docs
description: A naplózási lekérdezésekkel kapcsolatos gyakori kérdésekre válaszol, és megkezdi a használatot.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670117"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>A Azure Monitor lévő naplók áttekintése
A naplók lekérdezései segítségével teljes mértékben kihasználhatja [Azure monitor naplókban](../platform/data-platform-logs.md)összegyűjtött adatok értékét. Egy hatékony lekérdezési nyelv lehetővé teszi több táblázatból származó adatok összekapcsolását, nagy mennyiségű adat összesítését, valamint a minimális kóddal rendelkező összetett műveletek végrehajtását. Gyakorlatilag bármilyen kérdés megválaszolható és elemezhető mindaddig, amíg a támogatási adatok gyűjtése megtörtént, és tisztában van a megfelelő lekérdezés létrehozásával is.

A Azure Monitor egyes funkciói, [például](../insights/insights-overview.md) az elemzések és a [megoldások](../insights/solutions-inventory.md) feldolgozzák az adatokat, és nem teszik közzé az alapul szolgáló lekérdezéseket. A Azure Monitor egyéb funkcióinak teljes körű kihasználása érdekében ismernie kell a lekérdezések összeépítésének módját, valamint azt, hogy miként használhatja őket interaktív módon elemezni Azure Monitor naplókban.

Ez a cikk kiindulási pontként használható a Azure Monitor naplózási lekérdezésének megismeréséhez. A gyakori kérdésekre ad választ, és más dokumentációra mutató hivatkozásokat is tartalmaz, amelyek további részleteket és tanulságokat biztosítanak.

## <a name="how-can-i-learn-how-to-write-queries"></a>Honnan tudhatom meg, Hogyan írhatok le lekérdezéseket?
Ha a dolgokra szeretne ugrani, kezdje a következő oktatóanyagokkal:

- [Ismerkedjen meg a Azure Monitor log Analyticsával](get-started-portal.md).
- [Ismerkedjen meg a Azure monitor-naplózási lekérdezésekkel](get-started-queries.md).

Miután megtörtént az alapok leállása, a bemutató környezetből származó saját adatokkal vagy adatokkal akár több leckét is elindíthat: 

- [Karakterláncok használata Azure Monitor naplózási lekérdezésekben](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Milyen nyelveket használ a naplók lekérdezése?
Azure Monitor naplók az [Azure Adatkezelőon](/azure/data-explorer)alapulnak, és a napló lekérdezéseit ugyanazzal a Kusto lekérdezési nyelvvel (KQL) kell írni. Ez egy olyan gazdag nyelv, amely könnyen olvasható és létrehozható, és a lehető legkevesebb útmutatást kell biztosítania a használatához.

Tekintse meg az [Azure adatkezelő KQL dokumentációját](/azure/kusto/query) , amely a KQL és az elérhető különböző funkciókra vonatkozó dokumentációt tartalmazza.<br>
A Azure Monitor naplókból származó adatokkal a nyelv gyors áttekintéséhez tekintse [meg a Azure monitor a naplózási lekérdezések használatának első lépéseit](get-started-queries.md) .
A Azure Monitor által használt KQL-verzióban található kisebb eltérések esetében tekintse meg a [Azure monitor naplózási lekérdezés nyelvi különbségeit](data-explorer-difference.md) .

## <a name="what-data-is-available-to-log-queries"></a>Milyen adatforrások érhetők el a lekérdezések naplózásához?
A Azure Monitor naplókban összegyűjtött összes adatok beolvashatók és elemezhetők a naplók lekérdezésében. A különböző adatforrások különböző táblákba írják az állapotukat, de több táblát is megadhat egyetlen lekérdezésben, hogy több forrásból elemezze az adatforrásokat. Amikor létrehoz egy lekérdezést, először határozza meg, hogy mely táblák rendelkeznek a keresett adatokkal, ezért legalább egy alapvető ismeretekkel kell rendelkeznie arról, hogyan épülnek fel Azure Monitor naplókba az adatok.

Tekintse meg [Azure monitor naplók forrásait](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)a Azure monitor naplók feltöltésére szolgáló különböző adatforrások listájáért.<br>
Az adatstrukturálás módjáról az [Azure monitor naplók struktúrája](logs-structure.md) című témakörben olvashat.

## <a name="what-does-a-log-query-look-like"></a>Mire hasonlít a naplók lekérdezése?
A lekérdezés lehet egy egyszerű tábla neve, amellyel az adott táblából származó összes rekord beolvasható:

```Kusto
Syslog
```

Vagy szűrheti az egyes rekordokat, összefoglalhatja őket, és megjelenítheti az eredményeket egy diagramon:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Összetettebb elemzések esetén több táblázat adatait is lekérheti egy JOIN paranccsal, és elemezheti az eredményeket együtt.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Még ha nem ismeri a KQL-t, akkor is képesnek kell lennie legalább a lekérdezések által használt alapszintű logika kitalálása. A tábla nevével kezdődnek, majd több parancsot is hozzáadhatnak az adatszűréshez és a feldolgozáshoz. Egy lekérdezés tetszőleges számú parancsot használhat, és összetettebb lekérdezéseket is írhat, miközben megismerheti a különböző elérhető KQL-parancsokat.

A nyelv és a gyakori függvények bevezetésére szolgáló naplózási lekérdezésekkel kapcsolatos oktatóanyagért lásd: Ismerkedés [a naplók Azure Monitorával](get-started-queries.md) .<br>


## <a name="what-is-log-analytics"></a>Mi az a Log Analytics?
A Log Analytics a Azure Portal elsődleges eszköze a naplók írásához és az eredmények interaktív elemzéséhez. Akkor is, ha a naplózási lekérdezést Azure Monitorban máshol használják, általában a Log Analytics használatával fogja írni és tesztelni a lekérdezést.

A Azure Portal több helyről is elindítható Log Analytics. A Log Analytics számára elérhetővé tett adatmennyiséget az elindításának módját határozza meg. További részletekért lásd a [lekérdezési hatókört](scope.md) .

- Válassza a **naplók** lehetőséget a **Azure Monitor** menüből vagy **log Analytics munkaterületek** menüből.
- Válassza az **elemzés** lehetőséget egy Application Insights alkalmazás **Áttekintés** lapján.
- Válassza ki a **naplókat** egy Azure-erőforrás menüjéből.

![Log Analytics](media/log-query-overview/log-analytics.png)

A számos funkciójának bevezetéséhez tekintse meg a [Azure Monitor log Analyticsának első lépései](get-started-portal.md) című log Analytics oktatóanyagot.

## <a name="where-else-are-log-queries-used"></a>Hol használják a naplózási lekérdezéseket?
A naplózási lekérdezésekkel és azok eredményeivel való interaktív együttműködésen kívül a Log Analytics Azure Monitor azon területei, ahol a lekérdezéseket fogja használni, a következők:

- **Riasztási szabályok.** A [riasztási szabályok](../platform/alerts-overview.md) proaktív módon azonosítják a munkaterületen lévő adatokkal kapcsolatos problémákat.  Minden riasztási szabály egy naplóbeli keresésen alapul, amely rendszeres időközönként automatikusan fut.  Az eredményeket a rendszer ellenőrzi, hogy létre kell-e hozni egy riasztást.
- **Irányítópultok.** Bármely lekérdezés eredményét rögzítheti egy [Azure-irányítópulton](../learn/tutorial-logs-dashboards.md) , amely lehetővé teszi a naplók és a metrikai adatok együttes megjelenítését, és opcionálisan megoszthatja őket más Azure-felhasználókkal.
- **Kilátással.**  Létrehozhat olyan vizualizációkat, amelyek a [tervezővel](../platform/view-designer.md)felhasználói irányítópultokon is szerepelni fognak.  A naplók lekérdezései biztosítják a [csempék](../platform/view-designer-tiles.md) és a [vizualizációs részek](../platform/view-designer-parts.md) által az egyes nézetekben használt adatforrásokat.  
- **Exportálás.**  Amikor Azure Monitorba importálja az adatnapló adatait az Excelbe vagy [Power BIba](../platform/powerbi.md), létrehoz egy napló-lekérdezést az exportálandó adatok definiálásához.
- **PowerShell.** PowerShell-szkriptet futtathat egy parancssorból vagy egy Azure Automation runbook, amely a [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) használatával kérdezi le a naplófájlok adatait Azure monitorról.  Ehhez a parancsmaghoz lekérdezés szükséges a lekérdezni kívánt adatmeghatározáshoz.
- **Azure Monitor naplózó API-t.**  A [Azure monitor logs API](https://dev.loganalytics.io) lehetővé teszi, hogy bármely REST API ügyfél beolvassa a naplózási adatait a munkaterületről.  Az API-kérelem tartalmaz egy lekérdezést, amely az Azure Monitoron fut, hogy meghatározza a lekérdezni kívánt adatgyűjtést.


## <a name="next-steps"></a>Következő lépések
- [Útmutató a Azure Portal log Analyticsjának használatáról](get-started-portal.md).
- [Útmutató a lekérdezések írásához](get-started-queries.md).
