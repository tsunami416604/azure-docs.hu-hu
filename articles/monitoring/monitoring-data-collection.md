---
title: Gyűjtését monitorozási adatok az Azure-ban |} A Microsoft Docs
description: A monitorozási adatok, alkalmazások és szolgáltatások az Azure-ral és a segítségével elemezheti az összegyűjtött áttekintése.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: efbf0907f3ed75957159c38a536bd31e88a0dbb3
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213299"
---
# <a name="collecting-monitoring-data-in-azure"></a>Az Azure monitorozási adatok gyűjtésére
Ez a cikk a monitorozási adatok, alkalmazások és szolgáltatások az Azure-ral és a segítségével elemezheti az összegyűjtött áttekintést nyújt. 

## <a name="types-of-monitoring-data"></a>Monitorozási adatok típusai
Az összes monitorozási adat megfelel egy két alapvető típusok, metrikákat vagy naplókat. Minden egyes típusa eltérő jellemzőkkel rendelkezik, és olyan feladatokra ajánljuk adott, az alább ismertetett.

### <a name="metrics"></a>metrikák
Metrikák numerikus értékek időben bizonyos elemeit a rendszer egy adott időpontban ismertetik. Ezek tartalmazzák a különböző adatokat, beleértve magát, az érték a ideje gyűjtötte a program az értéket, a mérték értékét jelöli, és az adott erőforrás, amely az érték társítva van. Rendszeres időközönként metrikákat gyűjt az érték-e. Például előfordulhat, hogy összegyűjtött processzorhasználat egy virtuális gépet, az alkalmazás 10 percenként rögzíti minden perc vagy a felhasználók száma.

Metrikák, könnyen használható, és képes a közel valós idejű feldolgozásához. Különösen hasznos, mivel metrikák gyakran kell mintát, és a egy riasztás gyorsan is fired viszonylag egyszerű logikával riasztási legyenek. Például előfordulhat, hogy egy riasztás értesíti, ha egy mérőszám meghalad egy küszöbértéket, vagy riasztás aktiválódik, amikor két mérőszám értéke közötti különbség eléri egy adott érték.

Egyéni metrikák általában kevés betekintést nyújt a saját. Bármilyen környezetben és a egy egyszerű küszöbértéket összehasonlítása eltérő nélkül egyetlen értéket biztosítanak. Olyan értékes azonban minták és trendek azonosítását más metrikákkal együtt vagy kombinálva a naplókat, amelyek kontextusba adott értékek körül. Például az alkalmazás egy adott időben felhasználók bizonyos számú előfordulhat, hogy jelzi, hogy kevés az alkalmazás állapotával kapcsolatos. A felhasználók ugyanazon mérőszám több érték jelzi, ha hirtelen esést problémájára utalhat. Túl sok kivételeket az alkalmazásban fellépő, és a egy külön metrika által jelzett előfordulhat, hogy azonosítsa alkalmazás hibát okoz a legördülő menüből. Az alkalmazás összetevőinek hiba azonosítása különösen az alkalmazás által létrehozott események is segítséget nyújt a alapvető okainak azonosítása.

A riasztások alapján nem riasztásai teljesítménymetrikák alapján, rugalmas, de összetettebb logika is tartalmazhatnak. Bármely a több forrásból származó adatok összetett elemzésére végző lekérdezési eredmények alapján riasztást hozhat létre.

### <a name="logs"></a>Naplók
Naplók tartalmaznak különböző típusú adatokkal rendelkező különböző tulajdonságokat az egyes rekordok vannak rendezve. Naplók például metrikák numerikus értékeket tartalmaz, de rendszerint a szöveges adatok részletes leírását tartalmazza. Azok további különböznek metrikák, azok szerkezetét, változó és gyakran nem gyűjtött rendszeres időközönként.

A naplóbejegyzés gyakori típus egy eseményt. Események csak időnként gyűjtött egy alkalmazás vagy szolgáltatás által létrehozott, és általában a teljes kontextusba saját elegendő információt tartalmaznak.  Például egy esemény arra utalhat, hogy egy adott erőforrás létrehozásának vagy módosításának, egy új gazdagépre való megnövekedett forgalmának adott válaszként indítandók vagy egy alkalmazás hibát észlelt.

Naplók különösen hasznosak a különböző az összetett elemzésekre forrásokból származó adatok összevonása és időbeli alakulását. Adatok formátumát eltérőek lehetnek, mert a alkalmazásokat hozhat létre egyéni naplók használata a struktúra, amelyre szükségük. Metrikák is replikálható kombinálva trendelemzés más figyelési adatokhoz és egyéb adatok elemzése a naplókban.


## <a name="monitoring-tools-in-azure"></a>Figyelési eszközök az Azure-ban
Szolgáltatás által gyűjtött és elemzi a következő szakaszokban ismertetett több forrásból a figyelési adatok az Azure-ban.

### <a name="azure-metrics"></a>Az Azure-metrikák
Azure-erőforrások és alkalmazásokból érkező metrikákat gyűjt az Azure-mérőszámok. Metrikaadatok integrálva van az adott Azure-erőforrások például a virtuális gépek, többek között az ilyen mérőszámokat, mint a kiválasztott gép Processzor- és hálózatkihasználtságát gráfok az Azure Portalon oldalak. Is elemezhetők együtt a [Metrikaböngésző](../monitoring-and-diagnostics/monitoring-metric-charts.md) idővel amely is diagram több mérőszámok értékeit.  A diagramok megjelenítése interaktív módon, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket. Metrikák is lekérhet a [Azure REST API-val figyelési](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Megjelenik a részletek a különféle Azure-erőforrásokra által összegyűjtött metrikaadatok [figyelési adatok az Azure-ban forrásai](monitoring-data-sources.md). 

![Metrikaböngésző](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Azure-tevékenységnapló 
A [Azure-tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) tárolja a naplókat a konfigurációs és az Azure-szolgáltatások állapotát. Az Activity Log Explorer segítségével ezek a naplók megtekintése az Azure Portalon, de gyakran [másolva a Log Analytics](../log-analytics/log-analytics-activity.md) más naplózott adatokat elemezni.

Az Activity Log Explorer segítségével megtekintése a tevékenységnaplóban a szűrt megfelel bizonyos feltételeknek.  A legtöbb erőforrást is egy tevékenységnapló lehetőséget a menüben, az Azure Portalon, amely megjeleníti az Activity Log Explorer szűri az adott erőforráshoz. A vizsgálati naplók is lekérhet a [Azure monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Activity Log Explorer](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
A log Analytics kezelését az Azure-ban közös adatplatformot biztosítja. A szolgáltatás elsődleges tárolási és az elemzési naplók az Azure-ban, a teljesítményadatok gyűjtését. a különböző forrásokból, beleértve az ügynököket a virtuális gépek, a felügyeleti megoldások és a különböző Azure-erőforrások is. Más forrásokból, beleértve a metrikák és a tevékenységnapló adatait annak érdekében, hogy hozzon létre egy teljes körű központi tárházban figyelési adatok Log analyticsbe lehet másolni.

A log Analytics egy részletes lekérdezési nyelvet összegyűjti az adatelemzés számára rendelkezik.  Használhat [naplókeresési portálok](../log-analytics/log-analytics-log-search-portals.md) interaktív módon írása és tesztelése a lekérdezések és elemzésére szolgáló az eredményeiket. Emellett [nézeteket hozhat létre](../log-analytics/log-analytics-view-designer.md) megjelenítheti az eredményeket naplókeresését, vagy illessze be a közvetlenül egy Azure-irányítópultot, egy lekérdezés eredményeit.  Felügyeleti megoldások tartalmaznak naplókereséseken és nézeteken a Log Analytics az összegyűjtött adatok elemzéséhez. Más szolgáltatásokkal, például az Application Insights tárolnak adatokat a Log Analytics, és biztosítja a további elemzés.  

![Naplók](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Az Application Insights telemetria többféle platformon telepítve webalkalmazások gyűjti. Tárolja az adatokat az Azure-mérőszámok és a Log Analytics és a egy kiterjedt gazdag, a meglévő eszközöket, elemzése és vizualizációja az adatok elemzésére szolgáló eszközöket biztosít. Ez lehetővé teszi lehetővé a közös szolgáltatások, például a riasztások, naplókeresések és az egyéb figyelési használt irányítópultok.


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Service Map
A Service Map biztosít a virtuális gépeket, amelyek a saját folyamatok és a függőségek ábrázolása. Ezeket az adatokat a legtöbb tárolja a Log Analyticsben, így más felügyeleti adatokkal elemezheti. A Szolgáltatástérkép-konzol is lekéri az adatokat a Log Analyticsben, hogy megjeleníti azt a virtuális gép elemezni a környezetben.

![Service Map](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Figyelési adatok átvitele

### <a name="metrics-to-logs"></a>Naplók, metrikák
Metrikák más adattípusokkal a funkciókban gazdag lekérdezési nyelv használatával összetett elemzését a Log analyticsbe is lehet replikálni. Naplóadatok is, mint a mérőszámok, amely lehetővé teszi, hogy időbeli alakulását végrehajtása hosszabb ideig őrizheti meg. Amikor mérőszámok vagy egyéb teljesítményadatokat tárolódik, amely egy naplót indextáblaként adatok Log Analytics. Metrikák használatával támogatja a közel valós idejű elemzés és riasztás céljából trendelemzés és más elemzési naplók használata során.

Útmutató: Azure-erőforrások metrikáinak begyűjtését kap [gyűjtése az Azure naplói és a Log Analytics használati metrikái](../log-analytics/log-analytics-azure-storage.md). Útmutató az erőforrások metrikáinak begyűjtését Azure PaaS erőforrására [konfigurálhatja az Azure PaaS-erőforrás-mérőszámok gyűjtését a Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Naplók, metrikák
A fentiekben ismertetettek szerint mérőszám játszik rugalmasabb ügyfélkapcsolatok kialakítását, mint a naplókat, így kisebb késéssel és a egy alacsonyabb költségek riasztásokat hozhat létre. A log Analytics jelentős mennyiségű, metrikák alkalmas lenne, de nem tárol az Azure-mérőszámok numerikus adatokat gyűjt. Ilyenek például az ügynökök és felügyeleti megoldásokat összegyűjtött teljesítményadatok. Ezek az értékek némelyikét, hogy hol riasztások kezelése és elemzése a Metrikaböngészőben elérhető Azure-mérőszámok másolódnak.

A magyarázat, ez a funkció elérhető legyen [naplók jelenleg korlátozott nyilvános előzetes verzióban gyorsabban metrika riasztások](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Az értékek támogatási listája érhető el: [új metrikákhoz kapcsolódó riasztások a metrikák és létrehozási módszerek támogatott](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hub"></a>Eseményközpont
Mellett az Azure-ban az eszközök használatával elemezheti a monitorozási adatok, előfordulhat, hogy szeretné, hogy az ilyen biztonsági információk külső eszköz és esemény (SIEM) termékben továbbítja. Ez általában történik a [Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/). 

Útmutatást kaphat a különböző típusú monitorozási adatait a [Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>További lépések

- További információ a [monitorozási adatok elérhető](monitoring-data-sources.md) a különböző erőforrásokat az Azure-ban. 