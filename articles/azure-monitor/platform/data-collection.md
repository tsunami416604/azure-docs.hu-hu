---
title: Az Azure Monitor által gyűjtött adatok figyelése |} A Microsoft Docs
description: Figyelési adatok az Azure Monitor által gyűjtött metrikák, amelyek könnyen használható és képes a közel valós idejű felhasználói helyzetek, és a Log Analytics speciális elemzésekre szolgáló tárolt naplók elkülönített.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: bwren
ms.openlocfilehash: 7eabe50ed1069a6027d5ec387f0c1dba45feb58e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828542"
---
# <a name="monitoring-data-collected-by-azure-monitor"></a>Az Azure Monitor által gyűjtött adatok figyelése
[Az Azure Monitor](../../azure-monitor/overview.md) egy szolgáltatás, amellyel figyelheti az alkalmazások és az erőforrások támaszkodnak. Figyelt erőforrások központi az alábbi függvényre van telemetriai és egyéb adatok tárolására. A cikk ismerteti, hogyan az adatok tárolásának és az Azure Monitor által használt teljes leírását.

Az Azure Monitor által gyűjtött összes adat illeszkedik a két alapvető típusok, [metrikák](#metrics) és [naplók](#logs). Metrikák numerikus értékek időben bizonyos elemeit a rendszer egy adott időpontban ismertetik. Azok a könnyen használható, és képes a közel valós idejű feldolgozásához. Naplók tartalmaznak különböző típusú adatokkal rendelkező különböző tulajdonságokat az egyes rekordok vannak rendezve. Telemetriai adatokat – például az események és nyomok formájában tárolja naplók emellett teljesítményadatokká úgy, hogy azt az összes kombinálható is elemzés céljából.

![Az Azure Monitor áttekintése](media/data-collection/overview.png)

## <a name="metrics"></a>Mérőszámok
Metrikák numerikus értékek írja le a rendszer bizonyos elemeit egy adott időpontban. Azok a könnyen használható, és képes a közel valós idejű feldolgozásához. Rendszeres időközönként metrikákat gyűjt az érték-e. Hasznos, mert azok gyakran kell mintát, és riasztást gyorsan is fired viszonylag egyszerű logikával riasztási zajlik. 

Például processzorhasználat percenként összegyűjtött előfordulhat, hogy egy virtuális gépet, vagy az alkalmazás 10 percenként bejelentkezett felhasználók száma. Riasztás sikerült értesíti, ha azok közül az összegyűjtött értékek, vagy akár a két érték közötti különbségének meghaladja a meghatározott küszöbértéket.

Az Azure-ban mérőszámok adott attribútumok a következők:

* Gyűjtött perces gyakorisággal, hacsak nincs másképpen megadva a metrika-definícióban.
* Egyedileg azonosítja a metrika neve és a egy névtér, amely egy kategóriát funkcionál.
* 93 napig tárolja. A Log Analytics metrikák másolhatja a hosszú távú trendek.

Minden egyes Átjárómetrika értékeként a következő tulajdonságokkal rendelkezik:
* A ideje gyűjtötte a program az értéket.
* A mérték értékét típusát jelöli.
* Az erőforrás értéke társítva van.
* Maga az érték.
* Bizonyos metrikák rendelkezhet több dimenzióban, a következő szakaszban leírtak szerint. Egyéni metrikák dimenziók legfeljebb 10 lehet.

### <a name="multi-dimensional-metrics"></a>Többdimenziós metrikák
Dimenzió egy metrikát a név-érték párok megadásával írhatja le a metrikaérték további adatot áll. Például egy metrika _rendelkezésre álló lemezterület_ rendelkezhet egy nevű dimenziót _meghajtó_ értékekkel _C:_, _D:_, amelyek lehetővé teszik a megtekintése vagy összes rendelkezésre álló lemezterület-meghajtók, vagy minden meghajtó külön-külön. 

Az alábbi példában két adatkészletet egy képzeletbeli metrika nevű _hálózati átviteli sebesség_. Az első adatkészletet nincs dimenzió rendelkezik. A második adatkészlet jeleníti meg az értékeket két dimenzió, I_P Address_ és _iránya_:

### <a name="network-throughput"></a>Hálózati átviteli sebessége

 |Időbélyeg        | Metrikaérték | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kb/s | 
   | 8/9/2017 8:15 | 1,141.4 KB/s |
   | 8/9/2017 8:16 | 1,110.2 KB/s |

Ez a metrika dimenziók nélküli is csak egy alapszintű kérdésre választ, például "mennyi volt a saját hálózati átviteli sebességet egy adott időpontban?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Hálózati átviteli sebesség és a két dimenzió ("IP" és "Iránya")

| Időbélyeg          | Dimenzió "IP-címnél | "Iránya" dimenzió | Metrikaérték| 
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

### <a name="value-of-metrics"></a>Metrikák értékét
Egyéni metrikák általában kevés betekintést nyújt a saját. Bármilyen környezetben és a egy egyszerű küszöbértéket összehasonlítása eltérő nélkül egyetlen értéket biztosítanak. Ezek már értékes, minták és trendek azonosítását más metrikákkal együtt vagy kombinálva a naplókat, amelyek kontextusba adott értékek körül. 

Például az alkalmazás egy adott időben felhasználók bizonyos számú kiderülhet, kevés az alkalmazás állapotáról. Azonban a felhasználók, az azonos metrika több érték jelzi a hirtelen esést kapcsolatos problémára utalhat. Az alkalmazásban fellépő, és a egy külön metrika által jelzett túlzott kivételek előfordulhat, hogy azonosítsa alkalmazás hibát okoz a legördülő menüből. Az alkalmazás által létrehozott összetevőit a hibák azonosításához események segítségével gyökerének azonosítása.

### <a name="sources-of-metric-data"></a>Metrikus adatok forrásai
Nincsenek Azure-figyelése által gyűjtött metrikák három alapvető forrásai. Az összes metrikák érhetők el a metrika tárolóban, ahol azok kiértékelése együtt forrásától függetlenül.

**Platform metrikák** hozhatók létre az Azure-erőforrások és azok állapotát és teljesítményét betekintést biztosítanak. Különböző típusú erőforrást hoz létre egy [metrikák meghatározott készletét](../../monitoring-and-diagnostics/monitoring-supported-metrics.md) szükséges konfiguráció nélkül. 

**Alkalmazásmetrikák** a figyelt alkalmazások és a teljesítménybeli problémák észlelése, és nyomon követheti a trendeket, hogyan használják az alkalmazását az Application Insights által létrehozott. Ez magában foglalja az ilyen értékek _kiszolgáló válaszideje_ és _böngészőkivételek_.

**Egyéni metrikák** vannak metrikák felül a standard mérőszám meghatározó automatikusan elérhető. Egyéni metrikák egy egyetlen erőforráson ennek az erőforrásnak ugyanabban a régióban kell létrehoznia. Egyéni metrikák a következő módszerekkel hozhat létre:
    - [Az alkalmazás egyéni-metrikáinak definiálása](../../application-insights/app-insights-api-custom-events-metrics.md) , amely figyel az Application Insights. Ezek mellett a standard beállítása, alkalmazásmetrikák.
    - Közzététel használata Windows virtuális gépekről származó egyéni metrikákat [Windows diagnosztikai bővítmény (WAD)](../../monitoring-and-diagnostics/azure-diagnostics.md).
    - Egyéni metrikák használata a Linux rendszerű virtuális gépek a közzététel [InfluxData Telegraf ügynök](https://www.influxdata.com/time-series-platform/telegraf/).
    - Egyéni metrikák az Azure-szolgáltatások az egyéni mérőszámok API segítségével írhat.
    
![Méretmetrikák – áttekintés](media/data-collection/metrics-overview.png)

### <a name="what-can-you-do-with-metrics"></a>Tudja, mire a metrikák?
Metrikákkal elvégezhető feladatok a következők:

- Használat [metrikaböngésző](../../monitoring-and-diagnostics/monitoring-metric-charts.md) elemezheti az összegyűjtött metrikák és a egy diagramon jeleníti meg őket. Egy erőforrás (például egy virtuális Gépet, a webhely vagy a logikai alkalmazás) teljesítményének nyomon követésével diagramok rögzítésével egy [Azure irányítópultján](../../azure-portal/azure-portal-dashboards.md).
- Konfigurálja egy [a metrikaalapú riasztási szabály](../../monitoring-and-diagnostics/alert-metric.md) , amely elküld egy értesítést vagy veszi [művelet automatikus](../../monitoring-and-diagnostics/monitoring-action-groups.md) mikor a metrika átlép egy küszöbértéket.
- Használat [automatikus skálázási](../../monitoring-and-diagnostics/monitoring-overview-autoscale.md) növelése vagy csökkentése érdekében a küszöbérték átlépését metrika alapján erőforrásokat.
- Metrikák irányíthatja a Log Analytics metrikai adatok és naplóadatok elemzéséhez és metrikaértékek 93 napnál hosszabb ideig tárolja. 
- Metrikák Stream- [Eseményközpont](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) irányíthatja őket [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) vagy a külső rendszerekkel.
- [Archív](../../monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data.md) az erőforrás megfelelőségét, naplózás, vagy offline jelentéskészítésre teljesítmény vagy egészségügyi előzményeit.
- Egy parancssorból vagy egy egyéni alkalmazást a metrikaértékek eléréséhez [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.insights/?view=azurermps-6.7.0) vagy [REST API-val](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).



### <a name="viewing-metrics"></a>Metrikák megtekintése
Az Azure-ban metrikákat gyűjt az Azure Monitor-metrikák adatbázisban. Ez az idősor lépésközi 93 napig gyors lekérési és a tárolók metrikaértékek optimalizált adatbázis. Másolja a Log Analytics metrikák hosszú távú elemzésekhez és népszerű.

Metrikaadatok többféle módon szerepel, a fent leírt módon. Használat [metrikaböngésző](../../monitoring-and-diagnostics/monitoring-metric-charts.md) közvetlenül elemezheti az adatokat a metrika-tárolót, és több mérőszámok értékeit diagram idővel. A diagramok megjelenítése interaktív módon, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket. Metrikák használatával is lekérhet a [Azure REST API-val figyelési](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Metrikaböngésző](media/data-collection/metrics-explorer.png)





## <a name="logs"></a>Logs
Naplók tartalmaznak különböző típusú adatokkal rendelkező különböző tulajdonságokat az egyes rekordok vannak rendezve. Naplók például metrikák numerikus értékeket tartalmaz, de rendszerint a szöveges adatok részletes leírását tartalmazza. Azok további különböznek metrikák, azok szerkezetét, változó és gyakran nem gyűjtött rendszeres időközönként.

A naplóbejegyzés gyakori típus egy eseményt, amely időnként gyűjtött. Ezek a már létrehozott egy alkalmazás vagy szolgáltatás, és általában a teljes kontextusba saját elegendő információt tartalmaznak. Például az esemény azt jelzi, hogy egy adott erőforrás létrejött, és módosíthatók, megnövekedett forgalmának indított új gazdagépre, vagy hiba történt az alkalmazások.

Naplók különösen hasznosak a különböző forrásokból származó adatok összevonása, az összetett elemzésekre és az időbeli alakulását. Mivel az adatok formátumát változhat, alkalmazások segítségével hozhat létre egyéni naplók szükségük van a struktúrát. Metrikák még replikált kombinálhatja őket más figyelési adatokhoz trendelemzés és egyéb adatok elemzése a naplókat.



### <a name="log-analytics"></a>Log Analytics
Az Azure Monitor által gyűjtött naplók vannak tárolva a Log Analytics szolgáltatásnak, amely telemetriai és egyéb adatokat gyűjt különböző forrásokból. Olyan elemzési motor, amely betekintést nyerhet az alkalmazások és erőforrások működését, és a egy részletes lekérdezési nyelvet biztosít. Más Azure szolgáltatásokkal – például [az Azure Security Center](../../security-center/security-center-intro.md) annak érdekében, hogy egy közös adatplatformot biztosíthat az Azure felügyeleti tárolják az adataikat a Log Analyticsben.

> [!IMPORTANT]
> Az Application Insights tároljuk a Log Analytics például más naplóadatok azzal a különbséggel, hogy egy külön partíció tárolja. Ez más Log Analytics-adatok azonos funkciókat támogatja, de kell használnia a [Application Insights-konzol](/application-insights/app-insights-analytics.md) vagy a [Application Insights API](https://dev.applicationinsights.io/) az adatok elérésére. Használhat egy [erőforrások közötti lekérdezési](../../log-analytics/log-analytics-cross-workspace-search.md) alkalmazás adatok és más naplóadatok elemzéséhez.


### <a name="sources-of-log-data"></a>Naplózási adatok forrásai
A log Analytics adatokat gyűjthet, különböző forrásokból is Azure-ban és a helyszíni erőforrásokhoz. A Log Analytics szolgáltatásba írt adatok forrásai az alábbiak:

- [Tevékenységnaplók](../../log-analytics/log-analytics-activity.md) az Azure-erőforrások, amelyek tartalmazzák az információkat a konfigurációjukról és egészségügyi és [diagnosztikai naplók](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) termék működésével kapcsolatos információkat tartalmaznak, amelyek.
- Az ügynökök [Windows](../../log-analytics/log-analytics-windows-agent.md) és [Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) virtuális gépeket, amelyek a vendég operációs rendszer és alkalmazások a következők szerint a Log Analytics számára küldött telemetriai adatokra [adatforrások](../../log-analytics/log-analytics-data-sources.md) , konfigurálja.
- Alkalmazásadatok által gyűjtött [Application Insights](https://docs.microsoft.com/azure/application-insights/).
- Az adatok egy adott alkalmazás vagy szolgáltatás betekintést nyújtó [figyelési megoldások](../insights/solutions.md) vagy szolgáltatások, például a Container Insights, a virtuális gép Insights vagy a csoport Insights erőforrás.
- Biztonsági adatok által gyűjtött [az Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- [Metrikák](#metrics) Azure-erőforrásokból. Ez lehetővé teszi, hogy a 93 napnál hosszabb mérőszámokat tárol, és más naplózási adatokat elemezhet.
- Telemetria írt [Azure Storage](../../log-analytics/log-analytics-azure-storage-iis-table.md).
- Egyéni adatokat bármely REST API-ügyfél használatával a [HTTP-adatgyűjtő API](../../log-analytics/log-analytics-data-collector-api.md) ügyfél és a egy [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) munkafolyamat.

![A Log Analytics összetevői](media/data-collection/logs-overview.png)




### <a name="what-can-you-do-with-logs"></a>Felhasználási lehetőségei a naplók segítségével?
A naplók segítségével elvégezhető feladatok a következők:

- Használja a [Log Analytics lapot](../../log-analytics/query-language/get-started-analytics-portal.md) Teljesítménynapló-adatok elemzése lekérdezéseket írhat az Azure Portalon.  Eredmény jelenik meg a táblázatokat vagy diagramokat való rögzítése egy [Azure irányítópultján](../../azure-portal/azure-portal-dashboards.md).
- Konfigurálja egy [riasztási szabály](../../monitoring-and-diagnostics/alert-log.md) , amely elküld egy értesítést vagy veszi [művelet automatikus](../../monitoring-and-diagnostics/monitoring-action-groups.md) mikor a a lekérdezés eredménye megfelel-e egy adott eredményt.
- A munkafolyamat az a Log Analytics-adatok alapján hozhat létre [Logic Apps]().
- A lekérdezés eredményeinek exportálására [Power BI](../../log-analytics/log-analytics-powerbi.md) különböző Vizualizációk használata és megosztása Azure-on kívüli felhasználókkal.
- Egy parancssorból vagy egy egyéni alkalmazást a metrikaértékek eléréséhez [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/?view=azurermps-6.8.1) vagy [REST API-val](https://dev.loganalytics.io/).

### <a name="viewing-log-data"></a>Teljesítménynapló-adatok megtekintése
Minden adatot töröl a Log Analytics használatával lekéri a [naplólekérdezés](../../log-analytics/log-analytics-queries.md) , amely meghatározza, hogy egy adott adatkészletet. Lekérdezések használatával írt a [Log Analytics lekérdezési nyelvre](../../log-analytics/query-language/get-started-queries.md) Ez egy teljes funkcionalitású lekérdezésnyelvet gyorsan lekérni, konszolidálhatja és elemezheti az összegyűjtött adatokat. Használja a [Log Analytics lapot](../../log-analytics/log-analytics-log-search-portals.md) közvetlenül elemezheti az Azure Portalon a metrika az adatok tárolására, és több mérőszámok értékeit diagram idővel. A diagramok megjelenítése interaktív módon, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket. Metrikák használatával is lekérhet a [Azure REST API-val figyelési](../../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Logs](media/data-collection/logs.png)

## <a name="convert-monitoring-data"></a>Monitorozási adatok átalakítása

### <a name="metrics-to-logs"></a>Naplók, metrikák
Metrikák másolhatja a Log Analyticsben, hogy más típusú adatokat tartalmazó összetett elemzéseket végezhet a funkciókban gazdag lekérdezési nyelv használatával. Naplóadatok is, mint a mérőszámok, amely lehetővé teszi, hogy időbeli alakulását hosszabb ideig őrizheti meg. Amikor mérőszámok vagy egyéb teljesítményadatokat tárolódik, amely egy naplót indextáblaként adatok Log Analytics. Metrikák használatával támogatja a közel valós idejű elemzés és riasztás céljából trendelemzés és más elemzési naplók használata során.

Útmutató: Azure-erőforrások metrikáinak begyűjtését kap [gyűjtése az Azure naplói és a Log Analytics használati metrikái](../../log-analytics/log-analytics-azure-storage.md). Útmutató az erőforrások metrikáinak begyűjtését Azure PaaS erőforrására [konfigurálhatja az Azure PaaS-erőforrás-mérőszámok gyűjtését a Log Analytics](../../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Naplók, metrikák
A fentiekben ismertetettek szerint metrikák, gyorsabb, mint a naplókat, így kisebb késéssel és a egy alacsonyabb költségek létrehozhat riasztásokat. A log Analytics jelentős mennyiségű, metrikák alkalmas lenne, de nem az Azure-metrikagyűjtéshez adatbázisban tárolt numerikus adatokat gyűjt.  Ilyenek például az ügynökök és felügyeleti megoldásokat összegyűjtött teljesítményadatok. Ezek az értékek némelyike átmásolható a metrikák adatbázisba, ahol elérhetők a riasztás és a metrikaböngésző elemzéshez.

A magyarázat, ez a funkció elérhető legyen [metrika riasztások létrehozása az Azure Monitor-naplók](../../monitoring-and-diagnostics/monitoring-metric-alerts-logs.md). Az értékek támogatási listája érhető el: [az Azure monitorban támogatott mérőszámok](../../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces).

## <a name="stream-data-to-external-systems"></a>Stream data a külső rendszerekkel
Mellett az Azure-ban az eszközök használatával elemezheti a monitorozási adatok, szükség lehet a követelmény, hogy egy külső eszköz, például a biztonsági információk és az esemény (SIEM) termékben továbbítja. Közvetlenül a figyelt erőforrások keresztül általában történik a továbbító [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Útmutatást kaphat a különböző típusú monitorozási adatait a [Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>További lépések

- További információ a [monitorozási adatok elérhető](data-sources.md) a különböző erőforrásokat az Azure-ban.
