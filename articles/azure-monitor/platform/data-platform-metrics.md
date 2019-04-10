---
title: Metrikák az Azure monitorban |} A Microsoft Docs
description: Ismerteti az Azure monitorban mérőszámok, amelyek egyszerűsített monitorozási adatok közel valós idejű felhasználói helyzetek támogathatja.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 2646941e2384acf6d303615f564b65d616931180
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358872"
---
# <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

> [!NOTE]
> Az Azure Monitor adatplatform két alapvető adattípusok alapján: Metrikák és naplók. Ez a cikk ismerteti a metrikákat. Tekintse meg [naplók az Azure Monitor](data-platform-logs.md) naplók és a egy részletes leírása a [Azure Monitor adatok platforn](data-platform.md) a két összehasonlítását.


Metrikák az Azure monitorban, könnyen használható, és képes a közel valós idejű felhasználói helyzetek, így különösen a problémák riasztási és gyors észlelése. Ez a cikk azt ismerteti, hogyan metrikák struktúrája, mi mindent velük, és azonosítja az adatok tárolása a metrikák különböző adatforrásokból.

## <a name="what-are-metrics"></a>Mik azok a metrikák?
Metrikák numerikus értékek írja le a rendszer bizonyos elemeit egy adott időpontban. Metrikák legyenek gyűjtve rendszeres időközönként, és hasznosak, mert azok gyakran kell mintát, és riasztást gyorsan is fired viszonylag egyszerű logikával riasztási.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Felhasználási lehetőségei az Azure Monitor-metrikák?
Az alábbi táblázat a különböző módon, hogy használhatja az Azure monitorban metrikaadatok.

|  |  |
|:---|:---|
| Elemzés | Használat [metrikaböngésző](metrics-charts.md) elemezheti az összegyűjtött metrikák a diagramon, és hasonlítsa össze a különböző erőforrások metrikáit. |
| Vizualizáció | A metrikaböngésző diagram rögzítése egy [Azure irányítópultján](../learn/tutorial-app-dashboards.md).<br>Hozzon létre egy [munkafüzet](../app/usage-workbooks.md) egy interaktív jelentésben szereplő adatok több példányban egyesítenie. A lekérdezés eredményeinek exportálására [Grafana](grafana-plugin.md) annak dashboarding használatát, és más adatforrásokkal együtt. |
| Riasztás | Konfigurálja egy [a metrikaalapú riasztási szabály](alerts-metric.md) , amely elküld egy értesítést vagy veszi [művelet automatikus](action-groups.md) mikor a metrika értéke átlép egy küszöbértéket. |
| Automatizálás |  Használat [automatikus skálázási](autoscale-overview.md) növelése vagy csökkentése érdekében az erőforrásokat a küszöbérték átlépését egy metrikaérték alapján. |
| Exportálás | [Naplók átirányítása a metrikák](diagnostic-logs-stream-log-store.md) adatok elemzése az Azure Monitor-metrikák az Azure Monitor naplóira adatokkal együtt és metrikaértékek 93 napnál hosszabb ideig tárolja.<br>Metrikák Stream- [Eseményközpont](stream-monitoring-data-event-hubs.md) irányíthatja őket a külső rendszerekkel. |
| Lekérés | A parancssor használatával metrikaértékek elérését [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>Egyéni alkalmazást a metrikaértékek elérését [REST API-val](rest-api-walkthrough.md).<br>A parancssor használatával metrikaértékek elérését [CLI](/cli/azure/monitor/metrics). |
| Archívum | [Archív](..//learn/tutorial-archive-data.md) az erőforrás megfelelőségét, naplózás, vagy offline jelentéskészítésre teljesítmény vagy egészségügyi előzményeit. |


## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Hogyan alakul az Azure Monitor-metrikák strukturált adatok?
Az Azure-metrikák figyelése által gyűjtött adatokat egy idősorozat-adatbázis, amely időbélyeggel ellátott adatelemzés optimalizált tárolódik. Minden egyes metrikaértékek készlete áll egy idősorozat, az alábbi tulajdonságokkal:

* Az idő értékét gyűjtötte a program
* Az erőforrás értéke hozzá van rendelve.
* Egy névtér, amely úgy működik, mint a metrika-kategória
* Metrika neve
* Maga az érték
* Bizonyos metrikák rendelkezhet több dimenzióban leírtak szerint [többdimenziós metrikák](#multi-dimensional-metrics). Egyéni metrikák dimenziók legfeljebb 10 lehet.

Az Azure-ban mérőszámok 93 napig tárolódnak. Is [az erőforrások figyelése az Azure platform mérőszámok küldése a Log Analytics-munkaterülethez](diagnostic-logs-stream-log-store.md) a hosszú távú trendek.

## <a name="multi-dimensional-metrics"></a>Többdimenziós metrikák
A legnagyobb kihívás a metrikaadatok, hogy milyen gyakran korlátozott összegyűjtött értékek segítséget nyújt információt. Az Azure Monitor-címek a többdimenziós metrikák mutatták. Dimenzió egy metrikát a név-érték párok megadásával írhatja le a metrikaérték további adatot áll. Például egy metrika _rendelkezésre álló lemezterület_ nevű dimenzió rendelkezhet _meghajtó_ értékekkel _C:_, _D:_, amelyek lehetővé teszik a megtekintése vagy összes rendelkezésre álló lemezterület-meghajtók, vagy minden meghajtó külön-külön.

Az alábbi példában két adatkészletet egy képzeletbeli metrika nevű _hálózati átviteli sebesség_. Az első adatkészletet nincs dimenzió rendelkezik. A második adatkészlet jeleníti meg az értékeket két dimenzió _IP-cím_ és _iránya_:

### <a name="network-throughput"></a>Hálózati átviteli sebessége

| Időbélyeg     | Metrikaérték |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 kb/s |
| 8/9/2017 8:15 | 1,141.4 KB/s |
| 8/9/2017 8:16 | 1,110.2 KB/s |

Ez a metrika dimenziók nélküli is csak egy alapszintű kérdésre választ, például "mennyi volt a saját hálózati átviteli sebességet egy adott időpontban?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Hálózati átviteli sebesség és a két dimenzió ("IP" és "Iránya")

| Időbélyeg     | Dimenzió "IP-címnél   | "Iránya" dimenzió | Metrikaérték|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Iránya = "Küldése"    | 646.5 kb/s |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Iránya = "Jelenik meg" | 420.1 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Iránya = "Küldése"    | 150.0 KB/s |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Iránya = "Jelenik meg" | 115,2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Iránya = "Küldése"    | 515.2 KB/s |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Iránya = "Jelenik meg" | 371.1 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Iránya = "Küldése"    | 155.0 KB/s |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Iránya = "Jelenik meg" | 100.1 KB/s |

Ez a metrika segítségével választ kaphat a kérdéseket, például a "Mi volt a hálózat átviteli sebessége az egyes IP-cím?" és "mennyi adatot küldött és fogadott?" Többdimenziós metrikák biztosítunk további elemzési és diagnosztikai érték nem többdimenziós metrikák képest.

## <a name="interacting-with-azure-monitor-metrics"></a>Az Azure Monitor-metrikák használata
Használat [Metrikaböngésző](metrics-charts.md) interaktív elemezheti az adatokat a metrika adatbázisban, és több mérőszámok értékeit diagram idővel. A diagramok egy irányítópultot, megtekintheti őket más vizualizációkat is rögzíthet. Metrikák használatával is lekérhet a [Azure REST API-val figyelési](rest-api-walkthrough.md).

![Metrikaböngésző](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Források az Azure Monitor-metrikák
Nincsenek Azure-figyelése által gyűjtött metrikák három alapvető forrásai. Ha ezeket a metrikákat gyűjt az Azure Monitor metrika adatbázisban, azok együtt kiértékelése forrásától függetlenül.

**Platform metrikák** hozhatók létre az Azure-erőforrások és azok állapotát és teljesítményét betekintést biztosítanak. Különböző típusú erőforrást hoz létre egy [metrikák meghatározott készletét](metrics-supported.md) szükséges konfiguráció nélkül. Az Azure-erőforrások perces gyakorisággal platform metrikákat gyűjt hacsak nincs másképpen megadva a metrika-definícióban. 

**A vendég operációs rendszer metrikák** gyűjt a virtuális gép vendég operációs rendszerből. Windows virtuális gépek vendég operációs rendszer metrikáit engedélyezése [Windows diagnosztikai bővítmény (WAD)](../platform/diagnostics-extension-overview.md) és a Linux rendszerű virtuális gépek [InfluxData Telegraf ügynök](https://www.influxdata.com/time-series-platform/telegraf/).

**Alkalmazásmetrikák** a figyelt alkalmazások és a teljesítménybeli problémák észlelése, és nyomon követheti a trendeket, hogyan használják az alkalmazását az Application Insights által létrehozott. Ez magában foglalja az ilyen értékek _kiszolgáló válaszideje_ és _böngészőkivételek_.

**Egyéni metrikák** meghatározó felül a standard mérőszámok automatikusan elérhető metrikák vannak. Is [egyéni-metrikáinak definiálása az alkalmazásban](../app/api-custom-events-metrics.md) , amely figyel az Application Insights, vagy hozzon létre egy Azure-szolgáltatások használatára vonatkozó egyéni metrikákat az [egyéni metrikák API](metrics-store-custom-rest-api.md).


## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [Azure Monitor adatplatform](data-platform.md).
- Ismerje meg [adatok jelentkezzen be az Azure Monitor](data-platform-logs.md).
- További információ a [monitorozási adatok elérhető](data-sources.md) a különböző erőforrásokat az Azure-ban.
