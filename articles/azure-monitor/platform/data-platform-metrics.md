---
title: Metrikák az Azure Monitorban | Microsoft dokumentumok
description: Ismerteti metrikák az Azure Monitor, amelyek könnyű figyelési adatok, amelyek képesek támogatni a közel valós idejű forgatókönyvek.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: a02b3df02d455db8a7dfd21f63d659f75a66e446
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457314"
---
# <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

> [!NOTE]
> Az Azure Monitor adatplatform két alapvető adattípuson alapul: metrikák és naplók. Ez a cikk a metrikákat ismerteti. A naplók részletes leírását az [Azure Monitor naplói ban című naplóban,](data-platform-logs.md) a kettő összehasonlítását az [Azure Monitor adatplatformjában](data-platform.md) olvassa el.

Metrikák az Azure Monitorban könnyű, és képes támogatni a közel valós idejű forgatókönyvek, így különösen hasznos a riasztások és a problémák gyors észlelése. Ez a cikk ismerteti, hogyan metrikák strukturált, mit tehet velük, és azonosítja a különböző adatforrások, amelyek adatokat tárolnak a metrikákban.

## <a name="what-are-metrics"></a>Mik azok a metrikák?
A metrikák numerikus értékek, amelyek egy rendszer valamely elemét egy adott időpontra vonatkozóan írják le. Metrikák rendszeres időközönként gyűjtik, és hasznos a riasztási, mert gyakran mintavételezhetők, és egy riasztást lehet gyorsan kilőni viszonylag egyszerű logika.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Mire használható az Azure Monitor metrikák?
Az alábbi táblázat felsorolja a metrikaadatok azure Monitor különböző módon használhatja.

|  |  |
|:---|:---|
| Elemzés | A [metrikakezelő](metrics-charts.md) vel elemezheti a diagramon összegyűjtött mutatókat, és összehasonlíthatja a különböző erőforrásokból származó mutatókat. |
| Vizualizáció | A metrikakezelőből egy [Azure-irányítópultra](../learn/tutorial-app-dashboards.md)rögzíthet egy diagramot.<br>Hozzon létre [egy munkafüzetet,](../app/usage-workbooks.md) amelyet interaktív jelentésben több adathalmazsal kombinálhat. Exportálja a lekérdezés eredményeit a [Grafanába,](grafana-plugin.md) hogy kihasználja az irányítópultok és más adatforrásokkal való kombinálását. |
| Riasztás | Konfiguráljon egy [metrikariasztási szabályt,](alerts-metric.md) amely értesítést küld, vagy [automatikus műveletet tesz,](action-groups.md) amikor a metrikaérték átlép egy küszöbértéket. |
| Automatizálás |  [Az automatikus skálázás](autoscale-overview.md) használatával növelheti vagy csökkentheti az erőforrásokat a küszöbértéket átlépő metrikaérték alapján. |
| Exportálás | [Metrikák a naplók](resource-logs-collect-storage.md) adatok elemzéséhez az Azure Monitor metrikák adatok at Azure Monitor naplók és a metrikaértékek 93 napnál hosszabb ideig tárolja.<br>Metrikák streamelése egy [eseményközpontba,](stream-monitoring-data-event-hubs.md) hogy külső rendszerekre irányítsa őket. |
| Elhoz | Metrikaértékek elérése parancssorból [PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Metrikaértékek elérése egyéni alkalmazásból [a REST API használatával.](rest-api-walkthrough.md)<br>Metrikaértékek elérése parancssorból a [CLI](/cli/azure/monitor/metrics)használatával. |
| Archívum | [Archiválja](..//learn/tutorial-archive-data.md) az erőforrás teljesítményét vagy állapotát megfelelőségi, naplózási vagy offline jelentéskészítési célokra. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Hogyan épülnek fel az adatok az Azure Monitor metrikákban?
Az Azure Monitor metrics által gyűjtött adatok egy idősorozat-adatbázisban tárolódnak, amely az időbélyegzett adatok elemzésére van optimalizálva. A metrikaértékek minden egyes készlete a következő tulajdonságokkal rendelkező idősorozat:

* Az érték beszedésének időpontja
* Az az erőforrás, amelyhez az érték társítva van
* Olyan névtér, amely a mérőszám kategóriájaként működik
* A metrika neve
* Maga az érték
* Egyes mutatók több dimenzióval is rendelkezhetnek a [többdimenziós metrikákban leírtak szerint.](#multi-dimensional-metrics) Az egyéni metrikák legfeljebb 10 dimenzióval rendelkezhetnek.

## <a name="multi-dimensional-metrics"></a>Többdimenziós metrikák
A metrikaadatok egyik kihívása, hogy gyakran korlátozott információkkal rendelkezik az összegyűjtött értékek kontextusának biztosításához. Az Azure Monitor többdimenziós metrikákkal kezeli ezt a kihívást. A metrika dimenziói név-érték párok, amelyek további adatokat hordoznak a metrikaérték leírásához. Például egy _metrika Elérhető lemezterület_ lehet egy dimenzió nevű _Meghajtó_ értékek _C:_, _D:_, amely lehetővé tenné, hogy megtekinthesse vagy a rendelkezésre álló lemezterület az összes meghajtón, vagy minden meghajtó külön-külön.

Az alábbi példa két adatkészletet mutat be egy hálózati átviteli teljesítmény nevű hipotetikus _metrikához._ Az első adatkészletnek nincsenek dimenziói. A második adatkészlet két dimenzióval rendelkező értékeket jelenít meg: _IP-cím_ és _irány:_

### <a name="network-throughput"></a>Hálózati átviteli-átmenő

| Időbélyeg     | Metrikus érték |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1 331,8 Kb/s |
| 8/9/2017 8:15 | 1 141,4 Kb/s |
| 8/9/2017 8:16 | 1 110,2 Kb/s |

Ez a nem dimenziós metrika csak egy olyan alapvető kérdésre tud válaszolni, mint például: "mi volt a hálózati átviteli teljesítmény egy adott időpontban?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Hálózati átviteli - + két dimenzió ("IP" és "Irány")

| Időbélyeg     | "IP" dimenzió   | "Irány" dimenzió | Metrikus érték|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Irány="Küldés"    | 646,5 kb/s |
| 8/9/2017 8:14 | IP="192.168.5.2" | Irány="Fogadás" | 420,1 kb/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Irány="Küldés"    | 150,0 kb/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Irány="Fogadás" | 115,2 kb/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Irány="Küldés"    | 515,2 kb/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Irány="Fogadás" | 371,1 kbit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Irány="Küldés"    | 155,0 kb/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Irány="Fogadás" | 100,1 kb/s |

Ez a mérőszám olyan kérdésekre tud válaszolni, mint például a "mi volt a hálózati átviteli teljesítmény az egyes IP-címhez?", és "mennyi adatot küldött és kapott?" A többdimenziós metrikák további analitikai és diagnosztikai értéket hordoznak a nem dimenziós mérőszámokhoz képest.

## <a name="interacting-with-azure-monitor-metrics"></a>Az Azure Monitor-metrikák használata
A [Metrics Explorer](metrics-charts.md) segítségével interaktívan elemezheti a metrika-adatbázis adatait, és idővel több mutató értékeit is feltérképezheti. A diagramokat az irányítópultra rögzítheti, hogy más vizualizációkkal együtt megtekinthesse őket. A metrikákat az [Azure monitoring REST API](rest-api-walkthrough.md)használatával is lekérheti.

![Metrikaböngésző](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Az Azure Monitor metrikák forrásai
Az Azure Monitor által gyűjtött metrikák három alapvető forrása van. Miután ezeket a metrikákat az Azure Monitor metrika-adatbázisban gyűjtötték, a forrásuktól függetlenül együtt értékelhetők.

**A platformmérő kretrikákat** az Azure-erőforrások hozták létre, és betekintést nyújtanak az állapotukba és teljesítményükbe. Minden erőforrástípus hoz létre egy [külön metrikák](metrics-supported.md) konfiguráció nélkül. A platformmetrikák egyperces gyakorisággal kerülnek összegyűjtésre az Azure-erőforrásokból, kivéve, ha a metrika definíciója másként rendelkezik. 

**Vendég operációs rendszer metrikák** gyűjtik a vendég operációs rendszer egy virtuális gép. Vendég operációsrendszer-mérőszámok engedélyezése [Windows diagnosztikai bővítővel (WAD)](../platform/diagnostics-extension-overview.md) rendelkező Windows virtuális gépekhez és [Az InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/)szolgáltatással rendelkező Linux-alapú virtuális gépekhez.

**Az alkalmazásmetrikákat** az Application Insights a figyelt alkalmazásokhoz hozlétre, és segít a teljesítményproblémák észlelésében és az alkalmazás használati trendjeinek nyomon követésében. Ez olyan értékeket tartalmaz, mint _a szerverválaszidő_ és _a tallókivétel._

**Az egyéni metrikák** olyan metrikák, amelyeket az automatikusan elérhető szabványos metrikák mellett határoz meg. Egyéni [metrikákat határozhat meg az alkalmazásban,](../app/api-custom-events-metrics.md) amelyeket az Application Insights figyel, vagy egyéni metrikákat hozhat létre egy Azure-szolgáltatáshoz az [egyéni metrikák API használatával.](metrics-store-custom-rest-api.md)

## <a name="retention-of-metrics"></a>Metrikák megőrzése
Az Azure legtöbb erőforrása esetén a metrikák 93 napig tárolódnak. Vannak kivételek:

**Vendég operációs rendszer metrikák**
-   **Klasszikus vendég operációsrendszer-metrikák.** Ezek a Windows diagnosztikai [bővítmény (WAD)](../platform/diagnostics-extension-overview.md) vagy a [Linux diagnosztikai bővítmény (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) által gyűjtött és egy Azure-tárfiókba irányított teljesítményszámlálók. Ezek a metrikák megőrzése 14 nap.
-   **Az Azure Monitor metrikáknak küldött vendég operációsrendszer-metrikák.** Ezek a Windows diagnosztikai [bővítmény (WAD)](diagnostics-extension-overview.md) által gyűjtött és az [Azure Monitor adatgyűjtőbe](diagnostics-extension-overview.md#data-destinations)küldött teljesítményszámlálók, vagy a Linux-gépeken lévő [InfluxData Telegraf ügynöken](https://www.influxdata.com/time-series-platform/telegraf/) keresztül. Ezek a metrikák megőrzése 93 nap.
-   **A Log Analytics-ügynök által gyűjtött vendég operációsrendszer-mérőszámok.** Ezek a Log Analytics-ügynök által gyűjtött és a Log Analytics-munkaterületre küldött teljesítményszámlálók. Ezek a metrikák megőrzése 31 nap, és legfeljebb 2 évre meghosszabbítható.

**Az Application Insights naplóalapú mérőszámai.** 
- A jelenet mögött a [naplóalapú metrikák naplólekérdezésekké](../app/pre-aggregated-metrics-log-metrics.md) alakulnak. A megőrzési adatok megegyezik az alapul szolgáló naplókban lévő események megőrzésével. Az Application Insights-erőforrások esetén a naplók 90 napig tárolódnak.


> [!NOTE]
> Az [Azure Monitor-erőforrások platformmetrikákat küldhet a Log Analytics-munkaterületre](resource-logs-collect-storage.md) a hosszú távú felkapott.





## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitor adatplatformjáról.](data-platform.md)
- További információ [a naplóadatokról az Azure Monitorban.](data-platform-logs.md)
- Ismerje meg a [figyelési adatok at az](data-sources.md) Azure-ban rendelkezésre álló különböző erőforrások.
