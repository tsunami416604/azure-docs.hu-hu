---
title: Az Azure monitorban adatforrásokat |} A Microsoft Docs
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
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: b10236a1e0307c9464d58e50eb0c7b4e6a60b5e5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987775"
---
# <a name="sources-of-data-in-azure-monitor"></a>Az Azure monitorban adatok forrásai
Ez a cikk ismerteti az Azure Monitor állapotát és teljesítményét az erőforrások és a rajtuk futó alkalmazások figyelése által gyűjtött adatok forrásai. Ezeket az erőforrásokat az Azure-ban, egy másik felhőben vagy helyszíni lehet.  Lásd: [Azure Monitor által gyűjtött adatok](monitoring-data-collection.md) hogyan tárolja ezeket az adatokat, és hogyan tekintheti meg.

Rétegek, a legmagasabb szinten az alkalmazás és bármely operációs rendszerek és az alacsonyabb rétegek folyamatban van az Azure platform összetevői monitorozási adatok az Azure-ban a különböző forrásokból csoportosítható származnak. Ezt szemlélteti az alábbi ábrán az egyes szintek, a következő szakaszok részletesen ismertetjük.

![A monitorozási adatok szintje](media/monitoring-data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Az Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetriai adatokat gyűjt a bérlői szintű szolgáltatások, például az Azure Active Directory.

![Az Azure-bérlő gyűjtemény](media/monitoring-data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Az Azure Active Directory-naplók
[Jelentéskészítés az Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) bejelentkezési tevékenység és a naplózási beállításainak egy adott bérlőn belül végrehajtott módosítások előzményeit tartalmazza. Ezek a naplók az elemzésük más naplózott adatokat a Log Analytics szolgáltatásba lehet írni.


## <a name="azure-platform"></a>Azure-platform
Telemetriával kapcsolatos, az egészségügyi és működését az Azure maga a művelet és az Azure-előfizetés kezelésére vonatkozó adatokat tartalmazza. Ez magában foglalja a szolgáltatás egészségügyi adatok, tárolva az Azure-tevékenységnapló és a naplók az Azure Active Directoryból.

![Azure-előfizetés gyűjtemény](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Az Azure Service Health](../monitoring-and-diagnostics/monitoring-service-notifications.md) az Azure-szolgáltatások az előfizetésében, amelyet az alkalmazás és-erőforrások állapotával kapcsolatos információkat nyújt. A jelenlegi és várt kritikus problémákra, amelyek hatással lehetnek az alkalmazás értesítést riasztásokat is létrehozhat. Tárolja a Service Health-rekordokat a [Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), így az Activity Log Explorer ezeket is megtekintheti, és másolja őket a Log analyticsbe.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló
A [Azure-tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) együtt az Azure-erőforrások konfigurációs módosításai a rekordok service health rekordokat tartalmaz. A tevékenységnapló érhető el az összes Azure-erőforrások és jelöli a _külső_ megtekintése. A megadott típusú rekordok a tevékenységnaplóban ismertetett [Azure-tevékenységnapló eseménysémája](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

A tevékenységnapló egy bizonyos erőforrás esetén több erőforrást az Azure portal vagy a nézet naplóit a oldalán is megtekintheti a [Activity Log Explorer](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Ez különösen hasznos a naplóbejegyzéseket átmásolása a Log Analyticsben, hogy más figyelési adatokhoz való összevonásához. Is küldhet nekik segítségével más helyekre [az Event Hubs](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Azure-szolgáltatások
Metrikák és erőforrás szintű diagnosztikai naplók kapcsolatos adatok megadása a _belső_ az Azure-erőforrás a művelet. A legtöbb Azure-szolgáltatásokhoz érhető el, és felügyeleti megoldások további háttérismereteinek megismeréséhez adott szolgáltatásokhoz.

![Az Azure erőforrás-gyűjtemény](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mérőszámok
A legtöbb Azure-szolgáltatásokat hoz létre [platform metrikák](monitoring-data-collection.md#metrics) , amely tükrözi a teljesítmény és működés. Az adott [metrikák változnak az olyan erőforrástípusok](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Elérhető a metrikaböngészőben és népszerű és más elemzéshez másolhatók a Log Analytics szolgáltatásba.


### <a name="resource-diagnostic-logs"></a>Erőforrás-diagnosztikai naplók
Bár a tevékenységnapló egy Azure-erőforrások, erőforrásszintek simítása végrehajtott műveletek információt nyújt [diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) adja meg a műveletet az erőforrás magát betekintést.   A konfigurációs követelmények és ezek a naplók tartalmának [erőforrás típusa szerint változó](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Diagnosztikai naplók nem közvetlenül tekintheti meg az Azure Portalon, de Ön is [küldje el azokat az Azure storage alacsony költségű](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) és exportálhatja őket [Eseményközpont](../event-hubs/event-hubs-what-is-event-hubs.md) az átirányítás más szolgáltatások vagy [naplójába Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) elemzés céljából. Bizonyos erőforrások közvetlenül a Log Analytics írhat, míg mások írni egy storage-fiók mielőtt [importálja a Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Figyelési megoldások
 [Figyelési megoldások](../monitoring/monitoring-solutions.md) nyújt további betekintést a művelet egy adott szolgáltatás vagy alkalmazás az adatok gyűjtéséhez. Hol lehet a Log analyticsbe gyűjtenek adatokat elemzi a a [lekérdezési nyelvet](../log-analytics/log-analytics-log-search.md) vagy [nézetek](../log-analytics/log-analytics-view-designer.md) , amelyek általában bekerülnek a megoldásban.

## <a name="guest-operating-system"></a>Vendég operációs rendszer
Az Azure-ban, az egyéb felhőkben, és a helyszíni számítási erőforrások figyeléséhez vendég operációs rendszer rendelkezik. Egy vagy több ügynökök telepítésére a figyelési eszközök, az Azure-szolgáltatások magukat az gyűjthet telemetriai adatokat a vendégről származó.

![Azure-beli számítási erőforrás-gyűjtemény](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Diagnosztikai bővítmény
Az a [Azure Diagnostics bővítmény](../monitoring-and-diagnostics/azure-diagnostics.md), is gyűjthet naplókat, és a teljesítményadatokat az ügyfél operációs rendszerét az Azure számítási erőforrásokat. Metrikák és a naplók az ügyfelektől begyűjtött adatok alapján is az Azure storage-fiókhoz vannak tárolva [importálása a Log Analytics konfigurálása](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  A metrikaböngésző olvasása a tárfiókból ismeri, és az egyéb összegyűjtött metrikák ügyfél metrikákat tartalmazza.


### <a name="log-analytics-agent"></a>Log Analytics-ügynököket
A Log Analytics-ügynököket telepítheti bármelyik [Windows](../log-analytics/log-analytics-agent-windows.md) vagy [Linux]() virtuális gép vagy fizikai számítógép. A virtuális gép az Azure, egy másik felhőalapú vagy helyszíni futtathat.  Az ügynök csatlakozik a Log Analytics vagy közvetlenül vagy egy [csatlakoztatott System Center Operations Manager felügyeleti csoport](../log-analytics/log-analytics-om-agents.md) és adatokat gyűjthet [adatforrások](../log-analytics/log-analytics-data-sources.md) konfigurált vagy [felügyeleti megoldások](../monitoring/monitoring-solutions.md) , amely a virtuális gépen futó alkalmazások további betekintést nyújtson.

### <a name="service-map"></a>Szolgáltatástérkép
[A Service Map](../operations-management-suite/operations-management-suite-service-map.md) igényel a függőségi ügynököt Windows és Linux rendszerű virtuális gépeken. Ez a módszer a Log Analytics-ügynököt a virtuális gép és a függőségek a külső folyamatok futó folyamatok adatokat gyűjt. Ez tárolja ezeket az adatokat a Log Analytics és a egy kívül más, a Log Analytics szolgáltatásban tárolt adatok összegyűjti az adatokat vizuálisan megjelenítő konzol.

## <a name="applications"></a>Alkalmazások
Amellett, hogy az alkalmazás lehet írni a vendég operációs rendszer telemetriát, részletes alkalmazásfigyelés történik a [Application Insights](https://docs.microsoft.com/azure/application-insights/). Az Application Insights kiszállítására különböző platformokon futó alkalmazásokat tud adatokat gyűjteni. Az alkalmazás az Azure, egy másik felhőalapú vagy helyszíni futtathat.

![Alkalmazás adatgyűjtésének](media/monitoring-data-sources/application-collection.png)


### <a name="application-data"></a>Alkalmazásadatok
Amikor engedélyezi az Application Insights egy alkalmazás számára egy kialakítási csomag telepítése, gyűjti, metrikák és naplók a teljesítménnyel és az alkalmazás működésének kapcsolatos. Ez magában foglalja a lapmegtekintések alkalmazásokra irányuló kérések és kivételek kapcsolatos részletes információk. Az Application Insights összegyűjti az adatokat az Azure-mérőszámok és a Log Analytics tárolja. Adatok elemzésére szolgáló kiterjedt eszközöket tartalmaz, de a más forrásokból Metrikaböngészőt és a naplókeresések adatait is elemezheti.

Is használhatja az Application Insights [hozzon létre egy egyéni metrika](../application-insights/app-insights-api-custom-events-metrics.md).  Ez lehetővé teszi, hogy definiálja saját logika összeillesztésétől a numerikus értékek, és ezután tárolja ezt az értéket, amely a Metrikaböngésző érhető el, és használt más metrikákkal [automatikus skálázási](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) és metrikákhoz kapcsolódó riasztások.

### <a name="dependencies"></a>Függőségek
Annak érdekében, hogy egy alkalmazás különböző logikai műveletek monitorozására, kell [telemetriai adatok gyűjtése a több összetevőt](../application-insights/app-insights-transaction-diagnostics.md). Az Application Insights támogatja [telemetriai korreláció elosztott](../application-insights/application-insights-correlation.md) amely azonosítja a függőségeket, így elemezheti együtt összetevői között.

### <a name="availability-tests"></a>Rendelkezésre állási tesztek
[Rendelkezésre állási tesztek](../application-insights/app-insights-monitor-web-app-availability.md) az Application Insights lehetővé teszi, hogy ellenőrizze a rendelkezésre állásának és válaszkészségének az alkalmazás különböző helyekről, a nyilvános interneten. Hajtsa végre egy egyszerű ping teszt annak ellenőrzéséhez, hogy az alkalmazás aktív, vagy hozzon létre egy webes tesztet, amely a felhasználói forgatókönyv szimulálja a Visual Studio használatával.  Rendelkezésre állási tesztek nem igénylik bármilyen, az alkalmazás kialakítási.

## <a name="custom-sources"></a>Egyéni források
A standard csomagokhoz-alkalmazások, kívül szükség lehet egyéb telemetriai adatokból, hogy nem lehet összegyűjteni a más adatforrásokkal rendelkező erőforrások figyeléséhez. Ezekhez az erőforrásokhoz kell írnia az adatok egy Azure Monitor API-val.

![Egyéni adatok gyűjtése](media/monitoring-data-sources/custom-collection.png)

### <a name="data-collector-api"></a>Data Collector API
Az Azure Monitor naplózási adatokat gyűjt a bármely REST-ügyfél használatával a [adatgyűjtő API](../log-analytics/log-analytics-data-collector-api.md). Ez lehetővé teszi, hogy hozzon létre egyéni figyelési helyzeteket, és kiterjesztheti az erőforrásokhoz, amelyek nem teszik elérhetővé a telemetriai adatok segítségével más adatforrások figyelését.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [típusú monitorozási adatait az Azure Monitor által gyűjtött](monitoring-data-collection.md) , és hogyan tekintheti meg és elemezheti ezeket az adatokat.
