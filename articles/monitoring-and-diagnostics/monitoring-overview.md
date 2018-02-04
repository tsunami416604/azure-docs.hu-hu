---
title: "Azure-alkalmazások és erőforrások figyelése |} Microsoft Docs"
description: "A különböző Microsoft szolgáltatásokat és funkciókat, amelyek a teljes felügyeleti stratégia az Azure-szolgáltatások és alkalmazások áttekintése."
author: robb
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: robb,bwren
ms.openlocfilehash: 3ab7d2d5c3b95d215f3ee9eb9346e8a7895e734c
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="monitoring-azure-applications-and-resources"></a>Azure-alkalmazások és erőforrások figyelése

Figyelési összegyűjtése és annak meghatározásához, a teljesítmény, állapotának és rendelkezésre állását, valamint az üzleti alkalmazás attól függ, az erőforrások elemzéséhez. Egy hatékony felügyeleti stratégia segítenek megérteni a különböző összetevőket az alkalmazás és a hasznos üzemidő növeléséhez a proaktív értesítés, kritikus fontosságú problémáit, hogy a megoldásuk mielőtt azok veszélyeztetnék részletes működését.

Azure több szolgáltatást, amely külön-külön végrehajtani egy adott szerepkör vagy a feladatot a figyelés munkaterületen, és együttesen hoznak létre működő gyűjtése, elemzése és az alkalmazás és az alapjául szolgáló Azure-erőforrások telemetriai ható átfogó megoldást tartalmaz támogató őket.  Ezek is működnek, ahhoz, hogy adja meg a figyelési környezet hibrid kritikus a helyszíni erőforrások figyelése.   Az eszközök és a rendelkezésre álló adatok ismertetése első lépése az alkalmazás teljes felügyeleti stratégia kidolgozásában. 

Az alábbi ábrán látható a különböző összetevőket, amelyek együttműködve biztosítják az Azure-erőforrások figyelése konceptuális ábrázolása.  Ezek leírását a következő szakaszok a részletes műszaki információkra mutató hivatkozásokat tartalmaz.

![Figyelés – áttekintés](media/monitoring-overview/overview.png)

## <a name="basic-monitoring"></a>Alapszintű figyelés
Alapvető figyelését teszi lehetővé alapvető szükség Azure-erőforrások között.  Ezek a szolgáltatások minimális konfigurációs és gyűjthet mag, hogy a rendszer elkészítéséhez használja a Premium-szolgáltatások figyelése.    

### <a name="azure-monitor"></a>Azure Monitor
[Az Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) lehetővé teszi, hogy alapvető figyelési Azure Service gyűjteménye tételével [metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), és [diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).  Például a tevékenységnapló megtudhatja, amikor új erőforrásokat létrehozni vagy módosítani.  Metrikák érhetők el, amely teljesítménystatisztikáit. Adja meg a különböző erőforrások és még az operációs rendszer egy virtuális gépen belül.  Az adat megtekintéséhez az Azure-portálon szoftverkategóriák valamelyikét, elküldi a Log Analyticshez trendekkel és részletes elemzéséhez vagy proaktív értesítik a kritikus fontosságú problémáit riasztási szabályok létrehozásához.

### <a name="service-health"></a>Service Health
Az alkalmazás állapotát az Azure-szolgáltatásokhoz, amelyektől függ támaszkodik.  [Az Azure szolgáltatás állapota](../service-health/service-health-overview.md) azonosítja a probléma merül fel az Azure-szolgáltatásokkal, amelyek hatással lehetnek az alkalmazás- és is segítséget nyújt bármely ütemezés karbantartási tervet.

### <a name="azure-advisor"></a>Azure Advisor
[Az Azure Advisor](../advisor/advisor-overview.md) folyamatosan figyeli a erőforrás konfigurációs és használati telemetriai ajánlott eljárásai alapján javaslatok testreszabás biztosításához.  Követően ezek az ajánlások növelheti a teljesítményt, biztonsági és az alkalmazásokat támogató erőforrásokban rendelkezésre állását.


## <a name="premium-monitoring-services"></a>Prémium szintű figyelési szolgáltatásokat
Az Azure-szolgáltatásokat gyűjtése és elemzése a figyelési adatok gazdag képességeit adja meg.  Ezek alapvető figyelési és használja ki az általános funkciókkal Azure készítse el és hatékony analytics biztosítson Önnek egyedi insights az alkalmazásaikat és infrastruktúrájukat az összegyűjtött adatokat.  Ezek megjeleníteni az adatokat különböző irányuló adott helyzetekben kontextusában.

### <a name="application-insights"></a>Application Insights
[Az Application Insights](http://azure.microsoft.com/documentation/services/application-insights) rendelkezésre állás figyelése, teljesítmény és az alkalmazás használati lehetővé teszi, hogy a felhőben, vagy a helyszínen található.  Az alkalmazás működéséhez az Application insights szolgáltatással leírására, érhet mélyebben elemezheti, így gyorsan azonosíthatja és hibák diagnosztizálása a felhasználót, hogy ezeket várakozás nélkül. Gyűjtött adatokat hogy az alkalmazás karbantartása és fejlesztése megalapozott döntések.  A széles körű összegyűjti az adatokat az eszközök mellett a Application Insights megosztott funkciók, például a riasztások, irányítópultok és a Log Analytics lekérdezési nyelv mélyreható elemzéseket kihasználhatják a közös helyen tárolja az adatokat.

### <a name="log-analytics"></a>Log Analytics
[Naplófájl Analytics](http://azure.microsoft.com/documentation/services/log-analytics) begyűjtenie az adatokat a különböző erőforrások egy tárházba ahol elemzése hatékony lekérdezési nyelv Azure figyelési központi szerepet játszik.  Application insights szolgáltatással és az Azure Security Center tárolja az adatokat a Naplóelemzési adatokat tárolja és használja ki az analytics motor.  Ezzel együtt a következővel Azure figyelő, megoldásokkal, gyűjtött adatokat, és a felhőben, vagy a helyszíni virtuális gépeken telepített ügynökök lehetővé teszik a teljes környezet átfogó képet alkotnak. 


### <a name="service-map"></a>Szolgáltatástérkép
[Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md) IaaS környezetét betekintést biztosít a virtuális gépek a különböző folyamatok és a más számítógépeken és a külső folyamatok függőségek elemzésével.  Integrálható eseményeket, teljesítményadatokat és felügyeleti megoldásokat a Naplóelemzési, hogy minden számítógép és a környezet többi részére viszonya környezetében megtekintheti ezeket az adatokat.  Szolgáltatástérkép hasonlít a [Application Insights az alkalmazás-hozzárendelés](../application-insights/app-insights-app-map.md) , de az alkalmazásokat támogató infrastruktúra-összetevőihez összpontosít.

### <a name="network-watcher"></a>Network Watcher
[Hálózati figyelő](../network-watcher/network-watcher-monitoring-overview.md) forgatókönyv-alapú figyelési és diagnosztika biztosít a különböző hálózati forgatókönyvek az Azure-ban.  Azure metrikák és diagnosztika további elemzés céljából tárolja az adatokat, és együttműködik a [Naplóelemzési felügyeleti megoldásokat](../log-analytics/log-analytics-azure-networking-analytics.md) teljes figyelésére a hálózati erőforrásokhoz.


### <a name="management-solutions"></a>Felügyeleti megoldások
[Megoldások](../log-analytics/log-analytics-add-solutions.md) logika, amely áttekintést adnak a egy adott alkalmazás vagy szolgáltatás csomagolt csoportja.  Log Analytics tárolását és elemzését a figyelési adatokat gyűjtenek támaszkodnak.  Felügyeleti megoldások érhetők el a Microsoft és a partnerek figyelésére szolgáló különböző Azure és a harmadik fél számára. Példa figyelési megoldásoknak tartalmaznak [tároló figyelési](../log-analytics/log-analytics-containers.md) segítségével megtekintheti és kezelheti a tároló gazdagépek és [Azure SQL elemzés](../log-analytics/log-analytics-azure-sql.md) gyűjt, és amely teljesítménymutatók visualizes az SQL Az Azure adatbázisok.


## <a name="shared-functionality"></a>Megosztott funkció
A következő Azure eszközök kritikus ellátni a Premium-szolgáltatások figyelése.  A megosztott által több olyan szolgáltatás, amely lehetővé teszi, hogy kihasználja a közös funkciókat és konfigurációk több szolgáltatásban.

### <a name="alerts"></a>Riasztások
[Az Azure riasztások](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktív értesíti kritikus feltételek és vélhetően intézkedéseket.  A riasztási szabályok kihasználhatják a metrikák és a naplók beleértve több forrásból származó adatok. Használata [művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md) tartalmazó címzettek és a riasztás válaszul műveletek egyedi beállítása.  A követelmények alapján, indítsa el a webhookok külső műveleteket, és a ITSM eszközök integrálása riasztások rendelkezik.

### <a name="dashboards"></a>Irányítópultok
[Az Azure irányítópultok](../azure-portal/azure-portal-dashboards.md) lehetővé teszik a különböző típusú adatok egyesítése egyetlen az Azure portál és a megosztás ablaktábla Azure másokkal.  Például létrehozhat egy irányítópultot, amely egy grafikont a metrikákat, tevékenységi naplóit táblázatát, az Application Insights használati diagramot és egy naplófájl-keresési kimenete jelenik meg a Naplóelemzési csempék egyesíti.

Naplóelemzési adatokat emellett exportálhatja [Power BI](https://docs.microsoft.com/power-bi/) további képi megjelenítést kihasználásához, valamint, hogy az adatok belül, mind a szervezeten kívül másokkal elérhető.

### <a name="metrics-explorer"></a>Metrics Explorer
[Metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md) az Azure-erőforrások, amelyek segítenek által generált számértékeket ismeri, a művelet és a teljesítmény az erőforrás. Elküldheti metrikák Naplóelemzési analitikai adatok más forrásból.



### <a name="activity-logs"></a>Tevékenységnaplók
[Tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) adja meg a műveletet az Azure-erőforrások adatait.  Tartalmazzák az ilyen információkat az erőforrás, a szolgáltatás állapotát az incidensek, a javaslatok konfigurációs módosítás jobb használata az erőforrás és az automatikus skálázás műveleteivel kapcsolatos információkat.  Egy adott erőforráshoz naplók az Azure portál vagy a nézet naplóban tevékenység napló Explorer erőforrásairól a lapon tekintheti meg.  Is küldhet tevékenységi naplóit Naplóelemzési, megoldások, az ügynököt a virtuális gépek és a más forrásokból gyűjtött adatok elemezhetők.


## <a name="example-scenarios"></a>Példaforgatókönyvek
Az alábbiakban a magas szintű példák bemutatják, hogyan különböző Hálózatfigyelő eszközök az Azure-ban a különböző alkalmazási helyzetek volna használni.

### <a name="monitoring-a-web-application"></a>A webes alkalmazás figyelése
Fontolja meg egy webalkalmazást az Azure App Service szolgáltatások, Azure Storage és az SQL-adatbázis telepítve.  Sikerült megkezdése elérésével [metrikák](../monitoring-and-diagnostics/monitoring-overview-metrics.md) és [tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) az egyes Azure-portálon a felhasználók ezeket az egyéni erőforrásokat.  Ebbe beletartozik a fontos információkat, például az alkalmazás és az átlagos válaszidő kívül végrehajtott bármilyen konfigurációs módosításokat azonosító kérelmek száma.

Ön tudta folytassa a figyelő a portálon metrikák és a naplókat a különböző erőforrások együtt megtekintéséhez.  A metrikák szabványos paramétereinek meghatározása akkor [riasztási szabályok létrehozásához](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) proaktív értesítse arról, ha például növeli az átlagos válaszideje meghaladja a küszöbértéket.  Ahhoz, hogy az alkalmazás napi teljesítmény gyors áttekintést kap, hozzon létre egy Azure irányítópult a kritikus KPI-k képviselő mérőszámok diagramok megjelenítéséhez.

Az alkalmazás figyelést végrehajtásához, [konfigurálja az Application Insights](../application-insights/quick-monitor-portal.md).  Most gyűjtheti további adatokat biztosító további betekintést a művelet és az alkalmazás teljesítményét.  Az Application Insights lehetővé teszi a vizuális ábrázolását keresztül az alkalmazás-összetevők alapul szolgáló kapcsolatai észleli a [alkalmazás-hozzárendelés](../application-insights/app-insights-app-map.md) alapján kialakulhat [végpont nyomkövetés](../application-insights/app-insights-transaction-diagnostics.md) felderítéséhez a pontos összetevő, függőség és ahol probléma történt kivétel.  Létrehozhat [rendelkezésreállás figyelésére szolgáló tesztek](../application-insights/app-insights-monitor-web-app-availability.md) proaktív a különböző régiókban az alkalmazás teszteléséhez.  Segítségével a fejlesztők akkor [engedélyezése a Profilkészítő](../application-insights/enable-profiler-compute.md) , nyomon követheti az kérelmek és egy adott kódsort le kivételek.  

Ahhoz, hogy hogy további az alkalmazásban használt szolgáltatások lássák, vegye fel a [SQL elemzési megoldások](../log-analytics/log-analytics-azure-sql.md) Log Analyticshez való további adatok gyűjtéséért felelős ügyfélfeladatot. Némi várakozás után úgy dönt, hogy vizsgálja ki az időszakot, amikor a hely teljesítményére küszöbérték alá csökken az alapvető okát.  Írt Naplóelemzési a használati és teljesítményadatokat adatai közötti összefüggések keresésére lekérdezést által gyűjtött Application Insights teljesítményt és a konfiguráció a adatok az alkalmazás támogató Azure-erőforrások között.


### <a name="monitoring-virtual-machines"></a>Virtuális gépek figyelése
Lehetősége van a Windows és Linux rendszerű virtuális gépek az Azure-beli kombinációját.  Azure Monitor segítségével megtekintheti [tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) és [szintű metrikák gazdagép](../monitoring-and-diagnostics/monitoring-overview-metrics.md) majd adja hozzá a [Azure Diagnostics bővítmény](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) ahhoz, hogy gyűjtéséhez virtuális gépekhez a vendég operációs rendszerből.  Hoz majd létre [riasztási szabályok](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) proaktív értesítése, ha az alapvető metrikák ilyen processzorhasználat és a memória cross küszöbértékeket.

Egy üzleti alkalmazás futó virtuális gépek kapcsolatos további adatokat gyűjthet, [Naplóelemzési munkaterület létrehozása és a Virtuálisgép-bővítmény engedélyezése](../log-analytics/log-analytics-quick-collect-azurevm.md) minden egyes számítógépen.  Konfigurálja [különböző adatforrások gyűjteménye](../log-analytics/log-analytics-data-sources.md) az alkalmazáshoz és [nézeteket hozhat létre](../log-analytics/log-analytics-view-designer.md) számára, a napi művelet és a teljesítményt.  Majd [riasztási szabályok létrehozásához](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) értesítse arról, ha adott hibaesemények érkezik.  Ahhoz, hogy folyamatosan a telepített ügynök állapotának figyelésére, vegye fel a [ügyfélállapot-kezelési megoldás](../operations-management-suite/oms-solution-agenthealth.md).

Ahhoz, hogy további betekintést az alkalmazás akkor [adja hozzá a függőségi ügynök](../operations-management-suite/operations-management-suite-service-map-configure.md) ahhoz, hogy azok hozzáadása a virtuális gépek [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md).  Felderíti a folyamatokat, és más szolgáltatásokkal gépek közötti kapcsolatok azonosítja.  Egy jelentett leállás után a Szolgáltatástérkép az adott azon gépek azonosításához, a problémát tapasztalt Törvényszéki végrehajtásához használhatja.  Ezután létrehozhat egy [a Log Analytics-adatok lekérdezése](../log-analytics/log-analytics-log-search-new.md) a későbbiekben azonosítani a problémát, és proaktív értesítést küldenek, ha a feltétel már telepítve van a riasztási szabályt létrehozni.



## <a name="next-steps"></a>További lépések
További információ

* [Az ignite-on 2016 videó az Azure figyelője](https://myignite.microsoft.com/videos/4977)
* [Ismerkedés az Azure-figyelő](monitoring-get-started.md)
* [Az Azure Diagnostics](../azure-diagnostics.md) Ha a felhőalapú szolgáltatás, a virtuális gépet, a problémák diagnosztizálásához kívánt virtuális gép skálázása állítsa be, vagy a Service Fabric-alkalmazás.
* [Az Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) Ha kívánt diagnosztikai problémák az App Service Web app alkalmazásban.
* [Naplófájl Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) összegyűjtött figyelési adatok elemzéséhez.
