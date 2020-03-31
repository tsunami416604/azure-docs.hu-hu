---
title: Naplólekérdezések áttekintése az Azure Monitorban | Microsoft dokumentumok
description: Megválaszolja a naplólekérdezésekkel kapcsolatos gyakori kérdéseket, és megkezdheti azok használatát.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670117"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Naplólekérdezések áttekintése az Azure Monitorban
A naplólekérdezések segítségével teljes mértékben kihasználhatja az [Azure Monitor naplókban](../platform/data-platform-logs.md)gyűjtött adatok értékét. A hatékony lekérdezési nyelv lehetővé teszi, hogy több táblából származó adatokat egyesítsen, nagy adathalmazokat összesítsen, és összetett műveleteket hajtson végre minimális kóddal. Gyakorlatilag bármilyen kérdésre lehet válaszolni, és elemzés történik, amíg a támogató adatokat gyűjtötték, és ön megérti, hogyan kell létrehozni a megfelelő lekérdezést.

Az Azure Monitor egyes funkciói, például [az elemzések](../insights/insights-overview.md) és [a megoldások](../insights/solutions-inventory.md) anélkül dolgozzák fel a naplóadatokat, hogy az alapul szolgáló lekérdezéseknek kitennék. Az Azure Monitor egyéb funkcióinak teljes kihasználásához ismerje meg, hogyan épülnek fel a lekérdezések, és hogyan használhatja őket az Azure Monitor-naplók ban lévő adatok interaktív elemzésére.

Használja ezt a cikket, mint egy kiindulási pont a naplólekérdezések az Azure Monitorban. Megválaszolja a gyakori kérdéseket, és hivatkozásokat tartalmaz más dokumentációra, amely további részleteket és tanulságokat tartalmaz.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hogyan tanulhatok meg lekérdezéseket írni?
Ha azt szeretnénk, hogy ugrik jobbra a dolgokat, akkor kezdődik a következő oktatóanyagok:

- [Ismerkedés a Log Analytics szolgáltatással az Azure Monitorban.](get-started-portal.md)
- [Első lépések az Azure Monitor naplólekérdezéseivel.](get-started-queries.md)

Miután lekaptad az alapokat, több leckén is végighaladhatsz a saját adataid vagy a bemutatókörnyezetből származó adatok használatával, kezdve a következőkkel: 

- [Karakterláncok kezelése az Azure Monitor naplólekérdezéseiben](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Milyen nyelvet használnak a naplólekérdezések?
Az Azure Monitor Naplók alapul [Azure Data Explorer,](/azure/data-explorer)és a napló lekérdezések használatával írt azonos Kusto lekérdezési nyelv (KQL). Ez egy gazdag nyelv célja, hogy könnyen olvasható és a szerző, és akkor képesnek kell lennie arra, hogy elkezd használ ez-val minimális útmutatást.

Lásd: [Azure Data Explorer KQL dokumentáció](/azure/kusto/query) teljes dokumentációt a KQL és a különböző funkciók elérhető.<br>
Az Azure Monitor naplóiból származó adatok használatával a [naplólekérdezések első lépései](get-started-queries.md) az Azure Monitor figyelőjében című témakörben található.
Tekintse meg az [Azure Monitor naplózási lekérdezési nyelvi különbségek](data-explorer-difference.md) az Azure Monitor által használt KQL verziójában kisebb különbségeket.

## <a name="what-data-is-available-to-log-queries"></a>Milyen adatok érhetők el a lekérdezések naplózásához?
Az Azure Monitor naplók ban gyűjtött összes adat elérhető a naplólekérdezések beolvasásához és elemzéséhez. A különböző adatforrások különböző táblákba írják az adataikat, de több táblát is felvehet egyetlen lekérdezésbe, hogy több forrásban elemezhesük az adatokat. Amikor létrehoz egy lekérdezést, először határozza meg, hogy mely táblák rendelkeznek a keresett adatokkal, így legalább egy alapvető ismereteket kell készítenie az Azure Monitor Naplók ban lévő adatok felépítéséről.

Az [Azure-figyelőnaplók forrásai](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), az Azure Monitor naplók feltöltő különböző adatforrások listáját.<br>
[Az Azure Figyelőnaplók szerkezete](logs-structure.md) című témakörben ismerteti, hogyan épül fel az adatok.

## <a name="what-does-a-log-query-look-like"></a>Hogyan néz ki egy naplólekérdezés?
A lekérdezés lehet olyan egyszerű, mint egy tábla neve a tábla összes rekordjának beolvasásához:

```Kusto
Syslog
```

Vagy szűrhet bizonyos rekordokra, összegezheti őket, és megjelenítheti az eredményeket egy diagramon:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Összetettebb elemzéshez előfordulhat, hogy több táblából is lekérheti az adatokat illesztés használatával az eredmények együttes elemzéséhez.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Még akkor is, ha nem ismeri a KQL, akkor képesnek kell lennie arra, hogy legalább kitalálni az alapvető logikát, hogy ezek a lekérdezések által használt. Egy tábla nevével kezdődnek, majd több parancsot adnak hozzá az adatok szűréséhez és feldolgozásához. A lekérdezés tetszőleges számú parancsot használhat, és összetettebb lekérdezéseket is írhat, amint megismerkedik a rendelkezésre álló különböző KQL-parancsokkal.

Az [Azure Monitor naplólekérdezéseinek első lépései](get-started-queries.md) című témakörben a nyelvi és gyakori függvényeket bevezető naplólekérdezésekkel kapcsolatos oktatóanyagról.<br>


## <a name="what-is-log-analytics"></a>Mi az a Log Analytics?
A Log Analytics az Azure Portal elsődleges eszköze a naplólekérdezések írásához és az eredmények interaktív elemzéséhez. Még akkor is, ha egy naplólekérdezést máshol az Azure Monitor, általában írni és tesztelni a lekérdezést először a Log Analytics használatával.

A Log Analytics az Azure Portalon több helyről is elindítható. A Log Analytics számára elérhető adatok hatókörét a kezdési lehetőségek határozzák meg. További részleteket a [Lekérdezéshatókör](scope.md) ben talál.

- Válassza a **Naplók lehetőséget** az **Azure Monitor** menüben vagy a **Log Analytics-munkaterületek** menüben.
- Válassza az **Analytics** lehetőséget az Application Insights-alkalmazások **Áttekintés lapján.**
- Válassza **a Naplók lehetőséget** egy Azure-erőforrás menüjében.

![Log Analytics](media/log-query-overview/log-analytics.png)

A [Log Analytics azure-figyelőben](get-started-portal.md) című témakörben a Log Analytics számos funkcióját bevezető oktatóanyag-forgatókönyvét című témakörben olvassa el.

## <a name="where-else-are-log-queries-used"></a>Hol használják még a naplólekérdezéseket?
A naplólekérdezésekkel és azok eredményeivel való interaktív munka mellett az Azure Monitor azon területei, ahol a lekérdezéseket használni fogja, a következőket tartalmazzák:

- **Riasztási szabályok.** [A riasztási szabályok](../platform/alerts-overview.md) proaktív módon azonosítják a munkaterületadataiból származó problémákat.  Minden riasztási szabály egy naplókeresésen alapul, amely automatikusan rendszeres időközönként fut.  A rendszer megvizsgálja az eredményeket, hogy megállapítsa, létre kell-e hozni egy riasztást.
- **Irányítópultok.** Bármely lekérdezés eredményét rögzítheti egy [Azure-irányítópulton,](../learn/tutorial-logs-dashboards.md) amely lehetővé teszi a napló- és metrikaadatok együttes megjelenítését és más Azure-felhasználókkal való megosztást.
- **Kilátás nyílik.**  A [Nézettervező](../platform/view-designer.md)segítségével létrehozhatja a felhasználói irányítópultokba bevonandó adatok vizualizációit.  A naplólekérdezések biztosítják a [csempék](../platform/view-designer-tiles.md) és [vizualizációs részek](../platform/view-designer-parts.md) által az egyes nézetekben használt adatokat.  
- **Exportálás.**  Amikor naplóadatokat importál az Azure Monitorból az Excelbe vagy a [Power BI-ba,](../platform/powerbi.md)létrehoz egy naplólekérdezést az exportálandó adatok meghatározásához.
- **Powershell.** PowerShell-parancsfájlt parancssorból vagy Egy Azure Automation-runbookból futtathat, amely [get-azoperationalinsightssearchresults-t](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) használ a naplóadatok azure-figyelőből történő lekéréséhez.  Ehhez a parancsmaghoz lekérdezésre van szükség a beolvasni szükséges adatok meghatározásához.
- **Azure Monitor Logs API.**  Az [Azure Monitor Naplók API](https://dev.loganalytics.io) lehetővé teszi, hogy bármely REST API-ügyfél lekérni naplóadatokat a munkaterületről.  Az API-kérelem tartalmaz egy lekérdezést, amely az Azure Monitor on futtatott a beolvasni kívánt adatok meghatározásához.


## <a name="next-steps"></a>További lépések
- A [Log Analytics azure-portálon való használatának oktatóanyaga.](get-started-portal.md)
- Séta a [bemutató írásban lekérdezések](get-started-queries.md).
