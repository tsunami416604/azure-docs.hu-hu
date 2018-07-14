---
title: Figyelési adatok az Azure-ban források |} A Microsoft Docs
description: Ismerteti az adatok állapotát és teljesítményét az Azure-erőforrások és a rajtuk futó alkalmazások figyeléséhez másolatára.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 262099bbe45e483efd269445aa8042b30668ebe3
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036523"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Források a figyelési adatok az Azure-ban
Ez a cikk ismerteti az adatok állapotát és teljesítményét az Azure-erőforrások és a rajtuk futó alkalmazások figyeléséhez másolatára.  Adatokat gyűjthet és elemezhet az ismertetett eszközökkel [gyűjtését monitorozási adatok az Azure-ban](monitoring-data-collection.md)

Szint, legmagasabb szintű folyamatban van az alkalmazás és a legalacsonyabb szint alatt az Azure platform monitorozási adatok az Azure-ban a különböző forrásokból csoportosítható származnak. Ezt szemlélteti az alábbi ábrán az egyes szintek, a következő szakaszok részletesen ismertetjük.

![A monitorozási adatok szintje](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Azure-platform
Telemetriával kapcsolatos, az egészségügyi és működését az Azure maga a művelet és az Azure-előfizetés vagy a bérlő felügyeleti adatait tartalmazza. Ez magában foglalja a szolgáltatás egészségügyi adatok, tárolva az Azure-tevékenységnapló és a naplók az Azure Active Directoryból.

![Az Azure-gyűjtemény](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Az Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) az Azure-szolgáltatások az előfizetésében, amelyet az alkalmazás és-erőforrások állapotával kapcsolatos információkat nyújt. A jelenlegi és várt kritikus problémákra, amelyek hatással lehetnek az alkalmazás értesítést riasztásokat is létrehozhat. Tárolja a Service Health-rekordokat a [Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), így az Activity Log Explorer ezeket is megtekintheti, és másolja őket a Log analyticsbe.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló
A [Azure-tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) együtt az Azure-erőforrások konfigurációs módosításai a rekordok service health rekordokat tartalmaz. A tevékenységnapló érhető el az összes Azure-erőforrások és jelöli a _külső_ megtekintése. A megadott típusú rekordok a tevékenységnaplóban ismertetett [Azure-tevékenységnapló eseménysémája](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

A tevékenységnapló egy bizonyos erőforrás esetén több erőforrást az Azure portal vagy a nézet naplóit a oldalán is megtekintheti a [Activity Log Explorer](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Ez különösen hasznos a naplóbejegyzéseket átmásolása a Log Analyticsben, hogy más figyelési adatokhoz való összevonásához. Is küldhet nekik segítségével más helyekre [az Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).


### <a name="azure-active-directory-audit-logs"></a>Az Azure Active Directory-naplók
[Jelentéskészítés az Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md) bejelentkezési tevékenység és a naplózási beállításainak egy adott bérlőn belül végrehajtott módosítások előzményeit tartalmazza. Nem lehet jelenleg kombinálja az Azure Active Directory naplózási adatok más figyelési adatok, mert csak az Azure Active Directory-n keresztül elérhető és a [Azure Active Directory reporting API](../active-directory/active-directory-reporting-api-getting-started-azure-portal.md).


## <a name="azure-services"></a>Azure-szolgáltatások
Metrikák és erőforrás szintű diagnosztikai naplók kapcsolatos adatok megadása a _belső_ az Azure-erőforrás a művelet. A legtöbb Azure-szolgáltatásokhoz érhető el, és felügyeleti megoldások további háttérismereteinek megismeréséhez adott szolgáltatásokhoz.

![Az Azure erőforrás-gyűjtemény](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mérőszámok
Legtöbb Azure-szolgáltatás, amely a teljesítmény és működés metrikákat hoz létre. Az adott [metrikák változnak az olyan erőforrástípusok](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Elérhető a Metrikaböngészőben és népszerű és más elemzéshez másolhatók a Log Analytics szolgáltatásba.


### <a name="resource-diagnostic-logs"></a>Erőforrás-diagnosztikai naplók
Bár a tevékenységnapló egy Azure-erőforrások, erőforrásszintek simítása végrehajtott műveletek információt nyújt [diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) adja meg a műveletet az erőforrás magát betekintést.   A konfigurációs követelmények és ezek a naplók tartalmának [erőforrás típusa szerint változó](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Diagnosztikai naplók nem közvetlenül tekintheti meg az Azure Portalon, de Ön is [küldje el azokat az Azure storage alacsony költségű](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) és exportálhatja őket [Eseményközpont](../event-hubs/event-hubs-what-is-event-hubs.md) az átirányítás más szolgáltatások vagy [naplójába Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) elemzés céljából. Bizonyos erőforrások közvetlenül a Log Analytics írhat, míg mások írni egy storage-fiók mielőtt [importálja a Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="management-solutions"></a>Felügyeleti megoldások
 [Felügyeleti megoldások](../monitoring/monitoring-solutions.md) adatgyűjtéshez nyújt további betekintést egy adott szolgáltatás működését. Hol lehet a Log analyticsbe gyűjtenek adatokat elemzi a a [lekérdezési nyelvet](../log-analytics/log-analytics-log-search.md) vagy nézeteket, amelyek általában bekerülnek a megoldást.

## <a name="guest-operating-system"></a>Vendég operációs rendszer
Az összes Azure-szolgáltatások által létrehozott telemetriát, valamint compute-erőforrást egy vendég operációs rendszer figyelése. Egy vagy több ügynökök telepítésére a figyelési eszközök, az Azure-szolgáltatások magukat az gyűjthet telemetriai adatokat a vendégről származó.

![Azure-beli számítási erőforrás-gyűjtemény](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Diagnosztikai bővítmény
Az a [Azure Diagnostics bővítmény](../monitoring-and-diagnostics/azure-diagnostics.md), is gyűjthet naplókat, és a teljesítményadatokat az ügyfél operációs rendszerét az Azure számítási erőforrásokat. Metrikák és a naplók az ügyfelektől begyűjtött adatok alapján is az Azure storage-fiókhoz vannak tárolva [importálása a Log Analytics konfigurálása](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  A Metrikaböngésző olvasása a tárfiókból ismeri, és az egyéb összegyűjtött metrikák ügyfél metrikákat tartalmazza.


### <a name="log-analytics-agent"></a>Log Analytics-ügynököket
A Log Analytics-ügynököket telepítheti Windows vagy Linux rendszerű virtuális gép vagy fizikai számítógép. A virtuális gép az Azure, egy másik felhőalapú vagy helyszíni futtathat.  Az ügynök csatlakozik a Log Analytics vagy közvetlenül vagy egy [csatlakoztatott System Center Operations Manager felügyeleti csoport](../log-analytics/log-analytics-om-agents.md) és adatokat gyűjthet [adatforrások](../log-analytics/log-analytics-data-sources.md) konfigurált vagy [felügyeleti megoldások](../monitoring/monitoring-solutions.md) , amely a virtuális gépen futó alkalmazások további betekintést nyújtson.

### <a name="service-map"></a>Szolgáltatástérkép
[A Service Map](../operations-management-suite/operations-management-suite-service-map.md) igényel a függőségi ügynököt Windows és Linux rendszerű virtuális gépeken. Ez a módszer a Log Analytics-ügynököt a virtuális gép és a függőségek a külső folyamatok futó folyamatok adatokat gyűjt. Ez tárolja ezeket az adatokat a Log Analytics és a egy kívül más, a Log Analytics szolgáltatásban tárolt adatok összegyűjti az adatokat vizuálisan megjelenítő konzol.

## <a name="applications"></a>Alkalmazások
Amellett, hogy az alkalmazás lehet írni a vendég operációs rendszer telemetriát, részletes alkalmazásfigyelés történik a [Application Insights](https://docs.microsoft.com/azure/application-insights/). Az Application Insights kiszállítására különböző platformokon futó alkalmazásokat tud adatokat gyűjteni. Az alkalmazás az Azure, egy másik felhőalapú vagy helyszíni futtathat.

![Alkalmazás adatgyűjtésének](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>Alkalmazásadatok
Amikor engedélyezi az Application Insights egy alkalmazás számára egy kialakítási csomag telepítése, gyűjti, metrikák és naplók a teljesítménnyel és az alkalmazás működésének kapcsolatos. Ez magában foglalja a lapmegtekintések alkalmazásokra irányuló kérések és kivételek kapcsolatos részletes információk. Az Application Insights összegyűjti az adatokat az Azure-mérőszámok és a Log Analytics tárolja. Adatok elemzésére szolgáló kiterjedt eszközöket tartalmaz, de a más forrásokból Metrikaböngészőt és a naplókeresések adatait is elemezheti.

Is használhatja az Application Insights [hozzon létre egy egyéni metrika](../application-insights/app-insights-api-custom-events-metrics.md).  Ez lehetővé teszi, hogy definiálja saját logika összeillesztésétől a numerikus értékek, és ezután tárolja ezt az értéket, amely a Metrikaböngésző érhető el, és használt más metrikákkal [automatikus skálázási](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) és metrikákhoz kapcsolódó riasztások.

#### <a name="dependencies"></a>Függőségek
Annak érdekében, hogy egy alkalmazás különböző logikai műveletek monitorozására, kell [telemetriai adatok gyűjtése a több összetevőt](../application-insights/app-insights-transaction-diagnostics.md). Az Application Insights támogatja [telemetriai korreláció elosztott](../application-insights/application-insights-correlation.md) amely azonosítja a függőségeket, így elemezheti együtt összetevői között.

#### <a name="availability-tests"></a>Rendelkezésre állási tesztek
[Rendelkezésre állási tesztek](../application-insights/app-insights-monitor-web-app-availability.md) az Application Insights lehetővé teszi, hogy ellenőrizze a rendelkezésre állásának és válaszkészségének az alkalmazás különböző helyekről, a nyilvános interneten. Hajtsa végre egy egyszerű ping teszt annak ellenőrzéséhez, hogy az alkalmazás aktív, vagy hozzon létre egy webes tesztet, amely a felhasználói forgatókönyv szimulálja a Visual Studio használatával.  Rendelkezésre állási tesztek nem igénylik bármilyen, az alkalmazás kialakítási.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [figyelési adatok és az Azure-eszközök típusú](monitoring-data-collection.md) használt összegyűjtheti és elemezheti őket.
