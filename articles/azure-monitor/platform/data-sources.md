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
ms.openlocfilehash: 1612c2d47f88b6d065e5307be5ff3c99d8f6d405
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433440"
---
# <a name="sources-of-data-in-azure-monitor"></a>Az Azure monitorban adatok forrásai
Ez a cikk ismerteti az Azure Monitor állapotát és teljesítményét az erőforrások és a rajtuk futó alkalmazások figyelése által gyűjtött adatok forrásai. Ezeket az erőforrásokat az Azure-ban, egy másik felhőben vagy helyszíni lehet.  Lásd: [Azure Monitor által gyűjtött adatok](data-collection.md) hogyan tárolja ezeket az adatokat, és hogyan tekintheti meg.

Rétegek, a legmagasabb szinten az alkalmazás és bármely operációs rendszerek és az alacsonyabb rétegek folyamatban van az Azure platform összetevői monitorozási adatok az Azure-ban a különböző forrásokból csoportosítható származnak. Ezt szemlélteti az alábbi ábrán az egyes szintek, a következő szakaszok részletesen ismertetjük.

![A monitorozási adatok szintje](media/data-sources/monitoring-tiers.png)

## <a name="azure-tenant"></a>Az Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetriai adatokat gyűjt a bérlői szintű szolgáltatások, például az Azure Active Directory.

![Az Azure-bérlő gyűjtemény](media/data-sources/tenant-collection.png)

### <a name="azure-active-directory-audit-logs"></a>Az Azure Active Directory-naplók
[Jelentéskészítés az Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) bejelentkezési tevékenység és a naplózási beállításainak egy adott bérlőn belül végrehajtott módosítások előzményeit tartalmazza. Ezek a naplók csak írható Azure Monitor naplóira más log adatokkal elemezheti őket.


## <a name="azure-platform"></a>Azure-platform
Telemetriával kapcsolatos, az egészségügyi és működését az Azure maga a művelet és az Azure-előfizetés kezelésére vonatkozó adatokat tartalmazza. Ez magában foglalja a szolgáltatás egészségügyi adatok, tárolva az Azure-tevékenységnapló és a naplók az Azure Active Directoryból.

![Azure-előfizetés gyűjtemény](media/data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Azure Service Health
[Az Azure Service Health](../../monitoring-and-diagnostics/monitoring-service-notifications.md) az Azure-szolgáltatások az előfizetésében, amelyet az alkalmazás és-erőforrások állapotával kapcsolatos információkat nyújt. A jelenlegi és várt kritikus problémákra, amelyek hatással lehetnek az alkalmazás értesítést riasztásokat is létrehozhat. Tárolja a Service Health-rekordokat a [Azure tevékenységnapló](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), így az Activity Log Explorer ezeket is megtekintheti, és másolja őket az Azure Monitor naplóira.

### <a name="azure-activity-log"></a>Azure-tevékenységnapló
A [Azure-tevékenységnapló](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) együtt az Azure-erőforrások konfigurációs módosításai a rekordok service health rekordokat tartalmaz. A tevékenységnapló érhető el az összes Azure-erőforrások és jelöli a _külső_ megtekintése. A megadott típusú rekordok a tevékenységnaplóban ismertetett [Azure-tevékenységnapló eseménysémája](../../azure-monitor/platform/activity-log-schema.md).

A tevékenységnapló egy bizonyos erőforrás esetén több erőforrást az Azure portal vagy a nézet naplóit a oldalán is megtekintheti a [Activity Log Explorer](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Ez különösen hasznos a naplóbejegyzéseket másolása az Azure Monitor egyesítheti a többi monitorozási adattal együtt. Is küldhet nekik segítségével más helyekre [az Event Hubs](../../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).



## <a name="azure-services"></a>Azure-szolgáltatások
Metrikák és erőforrás szintű diagnosztikai naplók kapcsolatos adatok megadása a _belső_ az Azure-erőforrás a művelet. A legtöbb Azure-szolgáltatásokhoz érhető el, és felügyeleti megoldások további háttérismereteinek megismeréséhez adott szolgáltatásokhoz.

![Az Azure erőforrás-gyűjtemény](media/data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Mérőszámok
A legtöbb Azure-szolgáltatásokat hoz létre [platform metrikák](data-collection.md#metrics) , amely tükrözi a teljesítmény és működés. Az adott [metrikák változnak az olyan erőforrástípusok](../../azure-monitor/platform/metrics-supported.md).  Elérhető a metrikaböngészőben és népszerű és más elemzéshez másolhatók a Log Analytics szolgáltatásba.


### <a name="resource-diagnostic-logs"></a>Erőforrás-diagnosztikai naplók
Bár a tevékenységnapló egy Azure-erőforrások, erőforrásszintek simítása végrehajtott műveletek információt nyújt [diagnosztikai naplók](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) adja meg a műveletet az erőforrás magát betekintést.   A konfigurációs követelmények és ezek a naplók tartalmának [erőforrás típusa szerint változó](../../azure-monitor/platform/tutorial-dashboards.md).

Diagnosztikai naplók nem közvetlenül tekintheti meg az Azure Portalon, de Ön is [küldje el azokat az Azure storage alacsony költségű](../../azure-monitor/platform/archive-diagnostic-logs.md) és exportálhatja őket [Eseményközpont](../../event-hubs/event-hubs-about.md) az átirányítás más szolgáltatások vagy [naplójába Analytics](../../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) elemzés céljából. Bizonyos erőforrások közvetlenül a Log Analytics írhat, míg mások írni egy storage-fiók mielőtt [importálja a Log Analytics](../../azure-monitor/platform/azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="monitoring-solutions"></a>Figyelési megoldások
 [Figyelési megoldások](../insights/solutions.md) nyújt további betekintést a művelet egy adott szolgáltatás vagy alkalmazás az adatok gyűjtéséhez. Azok összegyűjtötte az adatokat az Azure Monitor naplóira hol lehet elemezni az [lekérdezési nyelvet](../log-query/log-query-overview.md) vagy [nézetek](view-designer.md) , amelyek általában bekerülnek a megoldás.

## <a name="guest-operating-system"></a>Vendég operációs rendszer
Az Azure-ban, az egyéb felhőkben, és a helyszíni számítási erőforrások figyeléséhez vendég operációs rendszer rendelkezik. Egy vagy több ügynökök telepítésére a figyelési eszközök, az Azure-szolgáltatások magukat az gyűjthet telemetriai adatokat a vendégről származó.

![Azure-beli számítási erőforrás-gyűjtemény](media/data-sources/compute-resource-collection.png)

### <a name="azure-diagnostic-extension"></a>Az Azure diagnosztikai bővítmény
Az Azure Diagnostics bővítmény a naplók gyűjtésére a figyelés alapvető szintjének biztosít, és a teljesítményadatokat az ügyfél operációs rendszerét az Azure számítási erőforrásokat.   

### <a name="log-analytics-agent"></a>Log Analytics-ügynököket
Teljes körű figyelését és kezelését a Windows vagy Linux rendszerű virtuális gépek vagy fizikai számítógép kézbesíti a rendszer a Log Analytics-ügynökkel. A virtuális gépet futtathat az Azure, egy másik felhőalapú vagy helyszíni és az ügynök csatlakozik az Azure-ba közvetlenül vagy a System Center Operations Manager figyelése, és lehetővé teszi az adatgyűjtést [adatforrások](agent-data-sources.md) során konfigurálja vagy [figyelési megoldások](../insights/solutions.md) , amely a virtuális gépen futó alkalmazások további betekintést nyújtson.

### <a name="dependency-agent"></a>Függőségi ügynök
[A Service Map](../insights/service-map.md) és [-beli virtuális gépek az Azure Monitor](../insights/vminsights-overview.md) igényel a függőségi ügynököt Windows és Linux rendszerű virtuális gépeken. Ez integrálható a Log Analytics-ügynök gyűjti a virtuális gép és a külső folyamat függőségek futó folyamatok felderített adatait. Azt tárolja ezeket az adatokat az Azure monitorban elérhetővé, és a felderített összekapcsolt összetevőket.  

Az ügynökök és a felügyeleti követelményektől függően használandó közötti különbségekről további ismertetése: [figyelés ügynökök áttekintése](agents-overview.md).

## <a name="applications"></a>Alkalmazások
Amellett, hogy az alkalmazás lehet írni a vendég operációs rendszer telemetriát, részletes alkalmazásfigyelés történik a [Application Insights](https://docs.microsoft.com/azure/application-insights/). Az Application Insights kiszállítására különböző platformokon futó alkalmazásokat tud adatokat gyűjteni. Az alkalmazás az Azure, egy másik felhőalapú vagy helyszíni futtathat.

![Alkalmazás adatgyűjtésének](media/data-sources/application-collection.png)


### <a name="application-data"></a>Alkalmazásadatok
Amikor engedélyezi az Application Insights egy alkalmazás számára egy kialakítási csomag telepítése, gyűjti, metrikák és naplók a teljesítménnyel és az alkalmazás működésének kapcsolatos. Ez magában foglalja a lapmegtekintések alkalmazásokra irányuló kérések és kivételek kapcsolatos részletes információk. Az Application Insights összegyűjti az adatokat az Azure monitorban tárolja. Adatok elemzésére szolgáló kiterjedt eszközöket tartalmaz, de az eszközök, például a metrikaalapú analytics és a log analytics használatával más forrásokból származó adatokat is elemezheti.

Is használhatja az Application Insights [hozzon létre egy egyéni metrika](../../application-insights/app-insights-api-custom-events-metrics.md).  Ez lehetővé teszi, hogy definiálja saját logika összeillesztésétől a numerikus értékek, és ezután tárolja ezt az értéket, amely a Metrikaböngésző érhető el, és használt más metrikákkal [automatikus skálázási](../../azure-monitor/platform/autoscale-custom-metric.md) és metrikákhoz kapcsolódó riasztások.

### <a name="dependencies"></a>Függőségek
Annak érdekében, hogy egy alkalmazás különböző logikai műveletek monitorozására, kell [telemetriai adatok gyűjtése a több összetevőt](../../application-insights/app-insights-transaction-diagnostics.md). Az Application Insights támogatja [telemetriai korreláció elosztott](../../application-insights/application-insights-correlation.md) amely azonosítja a függőségeket, így elemezheti együtt összetevői között.

### <a name="availability-tests"></a>Rendelkezésre állási tesztek
[Rendelkezésre állási tesztek](../../application-insights/app-insights-monitor-web-app-availability.md) az Application Insights lehetővé teszi, hogy ellenőrizze a rendelkezésre állásának és válaszkészségének az alkalmazás különböző helyekről, a nyilvános interneten. Hajtsa végre egy egyszerű ping teszt annak ellenőrzéséhez, hogy az alkalmazás aktív, vagy hozzon létre egy webes tesztet, amely a felhasználói forgatókönyv szimulálja a Visual Studio használatával.  Rendelkezésre állási tesztek nem igénylik bármilyen, az alkalmazás kialakítási.

## <a name="custom-sources"></a>Egyéni források
A standard csomagokhoz-alkalmazások, kívül szükség lehet egyéb telemetriai adatokból, hogy nem lehet összegyűjteni a más adatforrásokkal rendelkező erőforrások figyeléséhez. Ezekhez az erőforrásokhoz kell írnia az adatok egy Azure Monitor API-val.

![Egyéni adatok gyűjtése](media/data-sources/custom-collection.png)

### <a name="data-collector-api"></a>Data Collector API
Az Azure Monitor naplózási adatokat gyűjt a bármely REST-ügyfél használatával a [adatgyűjtő API](data-collector-api.md). Ez lehetővé teszi, hogy hozzon létre egyéni figyelési helyzeteket, és kiterjesztheti az erőforrásokhoz, amelyek nem teszik elérhetővé a telemetriai adatok segítségével más adatforrások figyelését.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [típusú monitorozási adatait az Azure Monitor által gyűjtött](data-collection.md) , és hogyan tekintheti meg és elemezheti ezeket az adatokat.
