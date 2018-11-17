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
ms.date: 11/13/2018
ms.author: bwren
ms.openlocfilehash: fca8ed66ecee443243dbba49b9ec63c782c5a84e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51828559"
---
# <a name="sources-of-data-in-azure-monitor"></a>Az Azure monitorban adatok forrásai
Ez a cikk ismerteti az Azure Monitor állapotát és teljesítményét az erőforrások és a rajtuk futó alkalmazások figyelése által gyűjtött adatok forrásai. Ezeket az erőforrásokat az Azure-ban, egy másik felhőben vagy helyszíni lehet.  Lásd: [Azure Monitor által gyűjtött adatok](data-collection.md) hogyan tárolja ezeket az adatokat, és hogyan tekintheti meg.

Rétegek, a legmagasabb szinten az alkalmazás és bármely operációs rendszerek és az alacsonyabb rétegek folyamatban van az Azure platform összetevői monitorozási adatok az Azure-ban a különböző forrásokból csoportosítható származnak. Ezt szemlélteti az alábbi ábrán az egyes szintek, a következő szakaszok részletesen ismertetjük.

![A monitorozási adatok szintje](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Az Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetriai adatokat gyűjt a bérlői szintű szolgáltatások, például az Azure Active Directory.

![Az Azure-bérlő gyűjtemény](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Az Azure Active Directory-naplók
[Jelentéskészítés az Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) bejelentkezési tevékenység és a naplózási beállításainak egy adott bérlőn belül végrehajtott módosítások előzményeit tartalmazza. Ezek a naplók az elemzésük más naplózott adatokat a Log Analytics szolgáltatásba lehet írni.


## <a name="azure-platform"></a>Azure-platform
Telemetriával kapcsolatos, az egészségügyi és működését az Azure maga a művelet és az Azure-előfizetés kezelésére vonatkozó adatokat tartalmazza. Ez magában foglalja a szolgáltatás egészségügyi adatok, tárolva az Azure-tevékenységnapló és a naplók az Azure Active Directoryból.

![Azure-előfizetés gyűjtemény](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Az Azure Service Health](../../monitoring-and-diagnostics/monitoring-service-notifications.md) az Azure-szolgáltatások az előfizetésében, amelyet az alkalmazás és-erőforrások állapotával kapcsolatos információkat nyújt. A jelenlegi és várt kritikus problémákra, amelyek hatással lehetnek az alkalmazás értesítést riasztásokat is létrehozhat. Tárolja a Service Health-rekordokat a [Azure tevékenységnapló](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), így az Activity Log Explorer ezeket is megtekintheti, és másolja őket a Log analyticsbe.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló
A [Azure-tevékenységnapló](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) együtt az Azure-erőforrások konfigurációs módosításai a rekordok service health rekordokat tartalmaz. A tevékenységnapló érhető el az összes Azure-erőforrások és jelöli a _külső_ megtekintése. A megadott típusú rekordok a tevékenységnaplóban ismertetett [Azure-tevékenységnapló eseménysémája](../../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

A tevékenységnapló egy bizonyos erőforrás esetén több erőforrást az Azure portal vagy a nézet naplóit a oldalán is megtekintheti a [Activity Log Explorer](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Ez különösen hasznos a naplóbejegyzéseket átmásolása a Log Analyticsben, hogy más figyelési adatokhoz való összevonásához. Is küldhet nekik segítségével más helyekre [az Event Hubs](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Azure-szolgáltatások
Metrikák és erőforrás szintű diagnosztikai naplók kapcsolatos adatok megadása a _belső_ az Azure-erőforrás a művelet. A legtöbb Azure-szolgáltatásokhoz érhető el, és felügyeleti megoldások további háttérismereteinek megismeréséhez adott szolgáltatásokhoz.

![Az Azure erőforrás-gyűjtemény](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mérőszámok
A legtöbb Azure-szolgáltatásokat hoz létre [platform metrikák](data-collection.md#metrics) , amely tükrözi a teljesítmény és működés. Az adott [metrikák változnak az olyan erőforrástípusok](../../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Elérhető a metrikaböngészőben és népszerű és más elemzéshez másolhatók a Log Analytics szolgáltatásba.


### <a name="resource-diagnostic-logs"></a>Erőforrás-diagnosztikai naplók
Bár a tevékenységnapló egy Azure-erőforrások, erőforrásszintek simítása végrehajtott műveletek információt nyújt [diagnosztikai naplók](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) adja meg a műveletet az erőforrás magát betekintést.   A konfigurációs követelmények és ezek a naplók tartalmának [erőforrás típusa szerint változó](../../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Diagnosztikai naplók nem közvetlenül tekintheti meg az Azure Portalon, de Ön is [küldje el azokat az Azure storage alacsony költségű](../../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) és exportálhatja őket [Eseményközpont](../../event-hubs/event-hubs-about.md) az átirányítás más szolgáltatások vagy [naplójába Analytics](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) elemzés céljából. Bizonyos erőforrások közvetlenül a Log Analytics írhat, míg mások írni egy storage-fiók mielőtt [importálja a Log Analytics](../../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Figyelési megoldások
 [Figyelési megoldások](../../azure-monitor/insights/solutions.md) nyújt további betekintést a művelet egy adott szolgáltatás vagy alkalmazás az adatok gyűjtéséhez. Hol lehet a Log analyticsbe gyűjtenek adatokat elemzi a a [lekérdezési nyelvet](../../log-analytics/log-analytics-queries.md) vagy [nézetek](../../log-analytics/log-analytics-view-designer.md) , amelyek általában bekerülnek a megoldásban.

## <a name="guest-operating-system"></a>Vendég operációs rendszer
Az Azure-ban, az egyéb felhőkben, és a helyszíni számítási erőforrások figyeléséhez vendég operációs rendszer rendelkezik. Egy vagy több ügynökök telepítésére a figyelési eszközök, az Azure-szolgáltatások magukat az gyűjthet telemetriai adatokat a vendégről származó.

![Azure-beli számítási erőforrás-gyűjtemény](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Az Azure diagnosztikai bővítmény
Az Azure Diagnostics bővítmény a naplók gyűjtésére a figyelés alapvető szintjének biztosít, és a teljesítményadatokat az ügyfél operációs rendszerét az Azure számítási erőforrásokat.   

### <a name="log-analytics-agent"></a>Log Analytics-ügynököket
Teljes körű figyelését és kezelését a Windows vagy Linux rendszerű virtuális gépek vagy fizikai számítógép kézbesíti a rendszer a Log Analytics-ügynökkel. Az ügynök csatlakozik-e a Log Analytics és a virtuális gépet futtathat az Azure, egy másik felhőalapú vagy helyszíni közvetlenül vagy a System Center Operations Manager keresztül, és lehetővé teszi, hogy az adatok gyűjtéséhez [adatforrások](../../log-analytics/log-analytics-data-sources.md) , amikor konfigurálja vagy [figyelési megoldások](../../azure-monitor/insights/solutions.md) , amely a virtuális gépen futó alkalmazások további betekintést nyújtson.

### <a name="dependency-agent"></a>Függőségi ügynök
[A Service Map](../insights/service-map.md) és [-beli virtuális gépek az Azure Monitor](../../azure-monitor/insights/vminsights-overview.md) igényel a függőségi ügynököt Windows és Linux rendszerű virtuális gépeken. Ez integrálható a Log Analytics-ügynök gyűjti a virtuális gép és a külső folyamat függőségek futó folyamatok felderített adatait. Azt tárolja ezeket az adatokat a Log Analytics elérhetővé, és a felderített összekapcsolt összetevőket.  

Az ügynökök és a felügyeleti követelményektől függően használandó közötti különbségekről további ismertetése: [figyelés ügynökök áttekintése](agents-overview.md).

## <a name="applications"></a>Alkalmazások
Amellett, hogy az alkalmazás lehet írni a vendég operációs rendszer telemetriát, részletes alkalmazásfigyelés történik a [Application Insights](https://docs.microsoft.com/azure/application-insights/). Az Application Insights kiszállítására különböző platformokon futó alkalmazásokat tud adatokat gyűjteni. Az alkalmazás az Azure, egy másik felhőalapú vagy helyszíni futtathat.

![Alkalmazás adatgyűjtésének](media/data-sources/application-collection.png)


### <a name="application-data"></a>Alkalmazásadatok
Amikor engedélyezi az Application Insights egy alkalmazás számára egy kialakítási csomag telepítése, gyűjti, metrikák és naplók a teljesítménnyel és az alkalmazás működésének kapcsolatos. Ez magában foglalja a lapmegtekintések alkalmazásokra irányuló kérések és kivételek kapcsolatos részletes információk. Az Application Insights összegyűjti az adatokat az Azure-mérőszámok és a Log Analytics tárolja. Adatok elemzésére szolgáló kiterjedt eszközöket tartalmaz, de a más forrásokból Metrikaböngészőt és a naplókeresések adatait is elemezheti.

Is használhatja az Application Insights [hozzon létre egy egyéni metrika](../../application-insights/app-insights-api-custom-events-metrics.md).  Ez lehetővé teszi, hogy definiálja saját logika összeillesztésétől a numerikus értékek, és ezután tárolja ezt az értéket, amely a Metrikaböngésző érhető el, és használt más metrikákkal [automatikus skálázási](../../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) és metrikákhoz kapcsolódó riasztások.

### <a name="dependencies"></a>Függőségek
Annak érdekében, hogy egy alkalmazás különböző logikai műveletek monitorozására, kell [telemetriai adatok gyűjtése a több összetevőt](../../application-insights/app-insights-transaction-diagnostics.md). Az Application Insights támogatja [telemetriai korreláció elosztott](../../application-insights/application-insights-correlation.md) amely azonosítja a függőségeket, így elemezheti együtt összetevői között.

### <a name="availability-tests"></a>Rendelkezésre állási tesztek
[Rendelkezésre állási tesztek](../../application-insights/app-insights-monitor-web-app-availability.md) az Application Insights lehetővé teszi, hogy ellenőrizze a rendelkezésre állásának és válaszkészségének az alkalmazás különböző helyekről, a nyilvános interneten. Hajtsa végre egy egyszerű ping teszt annak ellenőrzéséhez, hogy az alkalmazás aktív, vagy hozzon létre egy webes tesztet, amely a felhasználói forgatókönyv szimulálja a Visual Studio használatával.  Rendelkezésre állási tesztek nem igénylik bármilyen, az alkalmazás kialakítási.

## <a name="custom-sources"></a>Egyéni források
A standard csomagokhoz-alkalmazások, kívül szükség lehet egyéb telemetriai adatokból, hogy nem lehet összegyűjteni a más adatforrásokkal rendelkező erőforrások figyeléséhez. Ezekhez az erőforrásokhoz kell írnia az adatok egy Azure Monitor API-val.

![Egyéni adatok gyűjtése](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>Data Collector API
Az Azure Monitor naplózási adatokat gyűjt a bármely REST-ügyfél használatával a [adatgyűjtő API](../../log-analytics/log-analytics-data-collector-api.md). Ez lehetővé teszi, hogy hozzon létre egyéni figyelési helyzeteket, és kiterjesztheti az erőforrásokhoz, amelyek nem teszik elérhetővé a telemetriai adatok segítségével más adatforrások figyelését.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [típusú monitorozási adatait az Azure Monitor által gyűjtött](data-collection.md) , és hogyan tekintheti meg és elemezheti ezeket az adatokat.
