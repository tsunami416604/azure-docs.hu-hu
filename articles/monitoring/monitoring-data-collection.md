---
title: Begyűjtheti az adatokat az Azure-figyelése |} A Microsoft Docs
description: A monitorozási adatok, alkalmazások és szolgáltatások Azure-ral, és a segítségével elemezheti az összegyűjtött áttekintése.
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
ms.openlocfilehash: 35580d71aa2592fa94f42cfdbad3c192acc303c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363985"
---
# <a name="collect-monitoring-data-in-azure"></a>Az Azure monitorozási adatok gyűjtése
Ez a cikk a monitorozási adatok, alkalmazások és szolgáltatások az Azure-ban összegyűjtött áttekintést nyújt. Bemutatja az eszközöket, amelyek segítségével elemezheti az adatokat is. 

## <a name="types-of-monitoring-data"></a>Monitorozási adatok típusai
Az összes monitorozási adat megfelel egy két alapvető típusok, metrikákat vagy naplókat. Minden egyes típusa eltérő jellemzőkkel rendelkezik, és a leginkább megfelelő, adott helyzetekben.

### <a name="metrics"></a>Mérőszámok
Metrikák numerikus értékek írja le a rendszer bizonyos elemeit egy adott időpontban. Ezek a következők:

* Különböző adatokat, beleértve magát a értékét.
* Az érték gyűjtötte a program idő.
* A mérték értékét jelölő típusa.
* Az erőforrás, amely az érték társítva van. 

Rendszeres időközönként metrikákat gyűjt az érték-e. Például processzorhasználat percenként összegyűjtött előfordulhat, hogy egy virtuális gépet, vagy az alkalmazás 10 percenként bejelentkezett felhasználók száma.

Metrikák, könnyen használható, és képes a közel valós idejű feldolgozásához. Hasznos, mert metrikák gyakran kell mintát, és riasztást gyorsan is fired viszonylag egyszerű logikával riasztási zajlik. Például egy riasztás előfordulhat, hogy értesíti, ha egy mérőszám meghalad egy küszöbértéket. Vagy riasztás előfordulhat, hogy aktiválódik, amikor a különbség a két mérőszám eléri egy adott értéket.

Egyéni metrikák általában kevés betekintést nyújt a saját. Bármilyen környezetben és a egy egyszerű küszöbértéket összehasonlítása eltérő nélkül egyetlen értéket biztosítanak. Ezek már értékes, minták és trendek azonosítását más metrikákkal együtt vagy kombinálva a naplókat, amelyek kontextusba adott értékek körül. 

Például az alkalmazás egy adott időben felhasználók bizonyos számú kiderülhet, kevés az alkalmazás állapotáról. Azonban a felhasználók, az azonos metrika több érték jelzi a hirtelen esést kapcsolatos problémára utalhat. Az alkalmazásban fellépő, és a egy külön metrika által jelzett túlzott kivételek előfordulhat, hogy azonosítsa alkalmazás hibát okoz a legördülő menüből. Az alkalmazás által létrehozott összetevőit a hibák azonosításához események segítségével gyökerének azonosítása.

A riasztások alapján nem riasztásai teljesítménymetrikák alapján, rugalmas, de összetettebb logika is tartalmazhatnak. Bármely a több forrásból származó adatok összetett elemzésére végző lekérdezési eredmények alapján riasztást hozhat létre.

### <a name="logs"></a>Logs
Naplók tartalmaznak különböző típusú adatokkal rendelkező különböző tulajdonságokat az egyes rekordok vannak rendezve. Naplók előfordulhat, hogy például metrikák numerikus értékeket tartalmaz, de rendszerint a szöveges adatok részletes leírását tartalmazza. Azok további különböznek metrikák, azok szerkezetét, változó és gyakran nem gyűjtött rendszeres időközönként.

A naplóbejegyzés gyakori típus egy eseményt. Csak időnként eseményeket gyűjti. Ezek a már létrehozott egy alkalmazás vagy szolgáltatás, és általában a teljes kontextusba saját elegendő információt tartalmaznak. Például az esemény azt jelzi, hogy egy adott erőforrás létrejött, és módosíthatók, megnövekedett forgalmának indított új gazdagépre, vagy hiba történt az alkalmazások.

Naplók különösen hasznosak a különböző forrásokból, az összetett elemzésekre származó adatok összevonása és időbeli alakulását. Mivel az adatok formátumát változhat, alkalmazások segítségével hozhat létre egyéni naplók szükségük van a struktúrát. Metrikák is replikálhatók kombinálhatja őket a többi monitorozási adattal trendelemzés és egyéb adatok elemzése a naplókban.


## <a name="monitoring-tools-in-azure"></a>Figyelési eszközök az Azure-ban
Figyelési adatok az Azure-ban gyűjti, és a következő források segítségével elemezheti.

### <a name="azure-monitor"></a>Azure Monitor
Azure-erőforrások és alkalmazásokból érkező metrikákat gyűjt az Azure Monitor szolgáltatásba. Metrikaadatok integrálva van az oldalak az Azure Portalon az Azure-erőforrásokhoz. A virtuális gépek esetében a gráfok ilyen mérőszámok a Processzor- és hálózatkihasználtságát, a kiválasztott gép jelennek meg. 

Adatok elemzése a [Metrikaböngésző](../monitoring-and-diagnostics/monitoring-metric-charts.md), amely több mérőszámok értékeit diagram idővel. A diagramok megjelenítése interaktív módon, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket. Metrikák használatával is lekérhet a [Azure REST API-val figyelési](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Az Azure-erőforrások különböző típusú összegyűjtő metrikaadatok kapcsolatos további információkért lásd: [figyelési adatok az Azure-ban forrásai](monitoring-data-sources.md). 

![Metrikaböngésző](media/monitoring-data-collection/metrics-explorer.png)


### <a name="activity-log"></a>Tevékenységnapló 
A [Azure-tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) tárolja a naplókat a konfigurációs és az Azure-szolgáltatások állapotát. Activity Log Explorer segítségével ezek a naplók megtekintése az Azure Portalon, de gyakran [másolva az Azure Log Analyticshez](../log-analytics/log-analytics-activity.md) más naplózott adatokat elemezni.

Activity Log Explorer segítségével megtekintése a tevékenységnaplóban a szűrt megfelel bizonyos feltételeknek. A legtöbb erőforrást is rendelkezik egy **tevékenységnapló** a menüben, az Azure Portalon. Megjeleníti a Activity Log Explorer szűri az adott erőforráshoz. A Tevékenységnaplók segítségével is lekérhet a [Azure Monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Activity Log Explorer](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
A log Analytics kezelését az Azure-ban közös adatplatformot biztosítja. A tárolás és az elemzési naplók az Azure-ban használt elsődleges szolgáltatás. Azt gyűjti az adatokat különböző forrásokból, beleértve az ügynököket a virtual machines, a felügyeleti megoldások és az Azure-erőforrások is. Más forrásokból, beleértve a metrikák és a egy teljes körű központi tárházban figyelési adatok létrehozása a tevékenységnapló adatokat másolja.

A log Analytics egy részletes lekérdezési nyelvet, amely összegyűjti az adatelemzés számára rendelkezik. Használhat [naplókeresési portálok](../log-analytics/log-analytics-log-search-portals.md) interaktív módon írása és tesztelése a lekérdezések és elemzésére szolgáló az eredményeiket. Emellett [nézeteket hozhat létre](../log-analytics/log-analytics-view-designer.md) megjelenítheti az eredményeket naplókeresését, vagy illessze be a közvetlenül egy Azure-irányítópultot, egy lekérdezés eredményeit.  

Felügyeleti megoldások tartalmaznak naplókereséseken és nézeteken a Log Analytics gyűjtött adatok elemzéséhez. Más szolgáltatások, például az Azure Application Insights tárolnak adatokat a Log Analytics, és biztosítja a további elemzés.  

![Logs](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Az Application Insights telemetria többféle platformon telepítve webalkalmazások gyűjti. Az Azure Monitor és a Log Analytics adatokat tárolja. És az eszközök számos képletkategória biztosít jelenítenek meg adatokat és elemzésére. Ezek a képességek lehetővé teszik használata a szolgáltatások, például a riasztások, naplókeresések és az egyéb figyelési használt irányítópultok.


![Application Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Szolgáltatástérkép
A Service Map biztosít a virtuális gépeket, amelyek a saját folyamatok és a függőségek ábrázolása. Ezeket az adatokat a legtöbb tárolja a Log Analyticsben, így más felügyeleti adatokkal elemezheti. A Szolgáltatástérkép-konzol is lekéri az adatokat a Log Analyticsben, hogy megjeleníti azt a virtuális gép az elemezni kívánt kontextusában.

![Szolgáltatástérkép](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Figyelési adatok átvitele

### <a name="metrics-to-logs"></a>Naplók, metrikák
Metrikák, a Log Analyticsben, hogy más típusú adatokat tartalmazó összetett elemzéseket végezhet a funkciókban gazdag lekérdezési nyelv használatával replikálhatja. Naplóadatok is, mint a mérőszámok, amely lehetővé teszi, hogy időbeli alakulását hosszabb ideig őrizheti meg. Amikor mérőszámok vagy egyéb teljesítményadatokat tárolódik, amely egy naplót indextáblaként adatok Log Analytics. Metrikák használatával támogatja a közel valós idejű elemzés és riasztás céljából trendelemzés és más elemzési naplók használata során.

Útmutató: Azure-erőforrások metrikáinak begyűjtését kap [gyűjtése az Azure naplói és a Log Analytics használati metrikái](../log-analytics/log-analytics-azure-storage.md). Útmutató az erőforrások metrikáinak begyűjtését Azure PaaS erőforrására [konfigurálhatja az Azure PaaS-erőforrás-mérőszámok gyűjtését a Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Naplók, metrikák
A fentebb leírt módon metrikák, gyorsabb, mint a naplókat, így kisebb késéssel és a egy alacsonyabb költségek létrehozhat riasztásokat. A log Analytics jelentős mennyiségű, metrikák alkalmas lenne, de nem tárolja az Azure monitorban numerikus adatokat gyűjt. 

Ilyenek például az ügynökök és felügyeleti megoldásokat összegyűjtött teljesítményadatok. Ezek az értékek némelyike az Azure Monitor, ahol elérhetők a riasztás és a metrikaböngésző elemzéshez másolódnak.

A magyarázat, ez a funkció elérhető legyen [naplók jelenleg korlátozott nyilvános előzetes verzióban gyorsabban metrika riasztások](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Az értékek támogatási listája érhető el: [új metrikákhoz kapcsolódó riasztások a metrikák és létrehozási módszerek támogatott](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hubs"></a>Event Hubs
Az eszközök használata az Azure monitorozási adatok elemzéséhez, mellett érdemes továbbítja azt egy külső eszköz, például a biztonsági információk és az esemény (SIEM) termékben. A továbbítási általában tárfiókon keresztül valósítható [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Útmutatást kaphat a különböző típusú monitorozási adatait a [Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>További lépések

- További információ a [monitorozási adatok elérhető](monitoring-data-sources.md) a különböző erőforrásokat az Azure-ban. 