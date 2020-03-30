---
title: Adatforrások az Azure Monitorban | Microsoft dokumentumok
description: Ismerteti az Azure-erőforrások és az azokon futó alkalmazások állapotának és teljesítményének figyeléséhez rendelkezésre álló adatokat.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479841"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Az Azure Monitor figyelési adatainak forrásai
Az Azure Monitor egy [közös figyelési adatplatformon](data-platform.md) alapul, amely [naplókat](data-platform-logs.md) és [metrikákat](data-platform-metrics.md)tartalmaz. A platformra történő adatgyűjtés lehetővé teszi, hogy több erőforrásból származó adatokat együtt elemezhet az Azure Monitor közös eszközeivel. Figyelési adatok at is küldhető más helyekre, hogy támogassa bizonyos forgatókönyvek, és egyes erőforrások írhatnak más helyekre, mielőtt azok gyűjthetők a naplók vagy metrikák.

Ez a cikk ismerteti az Azure Monitor által gyűjtött adatok figyelésének különböző forrásait az Azure-erőforrások által létrehozott figyelési adatok mellett. Az adatok különböző helyekre történő gyűjtéséhez szükséges konfigurációra vonatkozó részletes információkra mutató hivatkozások találhatók.

## <a name="application-tiers"></a>Alkalmazásrétegek

Az Azure-alkalmazásokból származó figyelési adatok forrásai rétegekbe rendezhetők, a legmagasabb szintek pedig maga az alkalmazás, az alacsonyabb szintek pedig az Azure platform összetevői. Az egyes rétegekből származó adatok elérésének módja változó. Az alkalmazásszintek összegzése az alábbi táblázatban található, és az egyes rétegekfigyelési adatok forrásai a következő szakaszokban jelennek meg. Tekintse [meg az adatok figyelése az Azure-ban](data-locations.md) az egyes adatok helyének leírását, és hogyan férhet hozzá az adatokhoz.


![Figyelési szintek](../media/overview/overview.png)


### <a name="azure"></a>Azure
Az alábbi táblázat röviden ismerteti az Azure-ra jellemző alkalmazásszinteket. Miután a link további részleteket az egyes szakaszokaz alábbi szakaszokban.

| Szint | Leírás | Begyűjtési módszer |
|:---|:---|:---|
| [Azure-bérlő](#azure-tenant) | Bérlői szintű Azure-szolgáltatások, például az Azure Active Directory működésével kapcsolatos adatok. | Tekintse meg az AAD-adatokat a portálon, vagy konfigurálja a gyűjteményt az Azure Monitorra egy bérlői diagnosztikai beállítás használatával. |
| [Azure-előfizetés](#azure-subscription) | Az Azure-előfizetésben, például az Erőforrás-kezelőben és a Szolgáltatásállapotban található erőforrás-szolgáltatások állapotával és kezelésével kapcsolatos adatok. | Tekintse meg a portálon, vagy konfigurálja a gyűjtemény t az Azure Monitor egy naplóprofil használatával. |
| [Azure-erőforrások](#azure-resources) |  Az egyes Azure-erőforrások működésére és teljesítményére vonatkozó adatok. | Az automatikusan gyűjtött mérőszámok megtekintése a Metrikák intézőben.<br>Diagnosztikai beállítások konfigurálása a naplók gyűjtéséhez az Azure Monitorban.<br>Figyelési megoldások és insights elérhető az egyes erőforrástípusok részletesebb figyeléséhez. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, más felhős vagy helyszíni 
Az alábbi táblázat röviden ismerteti az azure-beli, egy másik felhőbeli vagy helyszíni alkalmazásszinteket. Miután a link további részleteket az egyes szakaszokaz alábbi szakaszokban.

| Szint | Leírás | Begyűjtési módszer |
|:---|:---|:---|
| [Operációs rendszer (vendég)](#operating-system-guest) | A számítási erőforrásokon alapuló operációs rendszer adatai. | Telepítse a Log Analytics-ügynököt, hogy ügyféladatforrásokat gyűjtsön az Azure Monitorés a függőségi ügynök begyűjtéséhez az Azure Monitor virtuális gépekhez támogató függőségek gyűjtéséhez.<br>Az Azure virtuális gépek, telepítse az Azure diagnosztikai bővítmény naplók és metrikák gyűjtése az Azure Monitorba. |
| [Alkalmazáskód](#application-code) | A tényleges alkalmazás és kód teljesítményére és működésére vonatkozó adatok, beleértve a teljesítmény-nyomkövetéseket, az alkalmazásnaplókat és a felhasználói telemetriai adatokat. | A kód segítségével adatokat gyűjthet az Application Insightsban. |
| [Egyéni források](#custom-sources) | Külső szolgáltatásokból vagy más összetevőkből vagy eszközökből származó adatok. | Napló- vagy metrikaadatok gyűjtése az Azure Monitorbármely REST-ügyfélből. |

## <a name="azure-tenant"></a>Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetriai adatokat a bérlői szintű szolgáltatásokból, például az Azure Active Directoryból gyűjti össze.

![Azure-beli bérlői gyűjtemény](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Az Azure Active Directory naplózási naplói
[Az Azure Active Directory-jelentéskészítés](../../active-directory/reports-monitoring/overview-reports.md) iményét tartalmazza a bejelentkezési tevékenység és a naplózási nyomvonal egy adott bérlőn belül végrehajtott módosítások. 

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Konfigurálja az Azure AD-naplók at kell gyűjteni az Azure Monitor más figyelési adatokkal elemezni őket. | [Azure AD-naplók integrálása az Azure Monitor-naplókkal (előzetes verzió)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exportálásra exportálhatja az Azure AD-naplókat az Azure Storage-ba archiváláshoz. | [Oktatóanyag: Azure AD-naplók archiválása egy Azure-tárfiókba (előzetes verzió)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Eseményközpont | Az Azure AD-naplók streamelése más helyekre az Event Hubs használatával. | [Oktatóanyag: Stream Eltakar Az Azure Active Directory-naplók egy Azure-eseményközpontba (előzetes verzió)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-előfizetés
Az Azure-előfizetés állapotával és működésével kapcsolatos telemetriai adatok.

![Azure-előfizetés](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-tevékenységnapló 
Az [Azure-tevékenységnapló](platform-logs-overview.md) szolgáltatásállapot-rekordokat, valamint az Azure-előfizetésben az erőforrásokon végrehajtott konfigurációs módosítások rekordjait tartalmazza. A tevékenységnapló minden Azure-erőforrás számára elérhető, és a _külső_ nézetüket jelöli.

| Cél | Leírás | Referencia |
|:---|:---|
| Tevékenységnapló | A tevékenységnapló a saját adattárba kerül, amelyet az Azure Monitor menüből tekinthet meg, vagy tevékenységnapló-riasztások létrehozásához használhat. | [A tevékenységnapló lekérdezése az Azure Portalon](activity-log-view.md#azure-portal) |
| Azure Monitor-naplók | Konfigurálja az Azure Monitor naplók a tevékenységnapló gyűjtésére, hogy más figyelési adatokkal elemezze azt. | [Azure-tevékenységnaplók gyűjtése és elemzése az Azure-figyelő Log Analytics-munkaterületén](activity-log-collect.md) |
| Azure Storage | Exportálja a tevékenységnaplót az Azure Storage-ba archiváláshoz. | [Archiválási tevékenységnapló](resource-logs-collect-storage.md)  |
| Event Hubs | A tevékenységnapló streamelése más helyekre az Event Hubs használatával | [Tevékenységnapló streamelése az Eseményközpontba](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Azure Service Health
[Az Azure Service Health](../../service-health/service-health-overview.md) az előfizetésben található Azure-szolgáltatások állapotáról nyújt tájékoztatást, amelyre az alkalmazás és az erőforrások támaszkodnak.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Tevékenységnapló<br>Azure Monitor-naplók | A Szolgáltatásállapot-rekordok az Azure-tevékenységnaplóban tárolódnak, így megtekintheti őket az Azure Portalon, vagy bármilyen más tevékenységet végrehajthat a tevékenységnaplóval. | [Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon](service-notifications.md) |


## <a name="azure-resources"></a>Azure-erőforrások
Metrikák és erőforrásnaplók az Azure-erőforrások _belső_ működésével kapcsolatos információkat tartalmaznak. Ezek a legtöbb Azure-szolgáltatáshoz elérhetők, és a figyelési megoldások és elemzések további adatokat gyűjtenek az egyes szolgáltatásokhoz.

![Azure-erőforrás-gyűjtemény](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Platform-mutatók 
A legtöbb Azure-szolgáltatás közvetlenül a metrikák adatbázisába küldi a teljesítményüket és működésüket tükröző [platformmutatókat.](data-platform-metrics.md) A konkrét [mutatók az egyes erőforrástípusoktól függően változnak.](metrics-supported.md) 

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Az Azure Monitor metrikák | A platformmetrikák konfiguráció nélkül írnak az Azure Monitor metrika-adatbázisába. A Mérőszámok kezelőből elérheti a platformmérő katajkait.  | [Bevezetés az Azure Metrikaböngésző használatába](metrics-getting-started.md)<br>[Támogatott metrikák az Azure Monitorsegítségével](metrics-supported.md) |
| Azure Monitor-naplók | Másolja a platform metrikáit a Naplókba a felkapott és egyéb elemzésekhez a Log Analytics használatával. | [Az Azure-diagnosztika közvetlenül a Log Analytics szolgáltatásba](resource-logs-collect-workspace.md) |
| Event Hubs | Az Event Hubs használatával metrikákat továbbítson más helyekre. |[Az Azure figyelési adatainak streamelése egy eseményközpontba külső eszköz általi felhasználás érdekében](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Erőforrásnaplók
[Az erőforrásnaplók](platform-logs-overview.md) betekintést nyújtanak egy Azure-erőforrás _belső_ működésébe.  Az erőforrásnaplók automatikusan létrejönnek, de létre kell hoznia egy diagnosztikai beállítást, hogy megadja az egyes erőforrásokhoz összegyűjtött célértéket.

A konfigurációs követelmények és az erőforrásnaplók tartalma erőforrástípustól függően változik, és még nem minden szolgáltatás hozza létre őket. Az egyes szolgáltatások részleteiről és a részletes konfigurációs eljárásokra mutató hivatkozásokról a [Támogatott szolgáltatások, sémák és az Azure-erőforrásnaplók kategóriái](diagnostic-logs-schema.md) című témakörben talál. Ha a szolgáltatás nem szerepel ebben a cikkben, majd a szolgáltatás jelenleg nem hoz létre erőforrásnaplókat.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Erőforrásnaplók küldése az Azure Monitor naplók elemzésre más összegyűjtött naplóadatokkal. | [Azure-erőforrásnaplók gyűjtése a Log Analytics-munkaterületen az Azure Monitorban](resource-logs-collect-storage.md) |
| Storage | Erőforrásnaplók küldése az Azure Storage archiváláshoz. | [Az Azure-erőforrásnaplók archiválása](resource-logs-collect-workspace.md) |
| Event Hubs | Erőforrásnaplók streamelése más helyekre az Event Hubs használatával. |[Az Azure-erőforrás-naplók streamelése egy eseményközpontba](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operációs rendszer (vendég)
Számítási erőforrások az Azure-ban, más felhőkben és a helyszíni vendég operációs rendszer figyelheti. Egy vagy több ügynök telepítésével összegyűjtheti a telemetriai adatokat a vendégtől az Azure Monitorba, hogy elemezze azt az azure-szolgáltatásokkal azonos figyelési eszközökkel.

![Azure számítási erőforrások gyűjtése](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure diagnosztikai bővítmény
Az Azure Diagnostics bővítmény engedélyezése az Azure virtuális gépekhez lehetővé teszi, hogy naplókat és metrikákat gyűjtsön az Azure számítási erőforrásainak vendég operációs rendszeréből, beleértve az Azure Cloud Service (klasszikus) webes és feldolgozói szerepköröket, virtuális gépeket, virtuális gépeket méretezési csoportok, és a Service Fabric.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Storage | Az Azure diagnosztikai bővítmény mindig egy Azure Storage-fiókba ír. | [A Windows Azure diagnosztikai bővítmény (WAD) telepítése és konfigurálása](diagnostics-extension-windows-install.md)<br>[Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával](../../virtual-machines/extensions/diagnostics-linux.md) |
| Az Azure Monitor metrikák | Ha konfigurálja a diagnosztikai bővítmény teljesítményszámlálók gyűjtésére, azok az Azure Monitor metrikák adatbázisba írásakor. | [Vendég operációsrendszer-mérőszámok küldése az Azure Monitor metrikatárolóba egy Windows virtuális gép Erőforrás-kezelő sablonja segítségével](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Konfigurálja a Diagnosztikai bővítményt úgy, hogy az adatokat más helyekre továbbítsa az Event Hubs használatával.  | [Az Azure-diagnosztika adatainak streamelése az Event Hubs használatával](diagnostics-extension-stream-event-hubs.md)<br>[Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával](../../virtual-machines/extensions/diagnostics-linux.md) |
| Application Insights-naplók | Gyűjtsön naplókat és teljesítményszámlálókat az alkalmazást más alkalmazásadatokkal elemezendő számítási erőforrásból. | [Felhőszolgáltatás, virtuális gép vagy a Service Fabric diagnosztikai adatainak küldése az Application Insightsba](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-ügynök 
Telepítse a Log Analytics-ügynököt a Windows vagy Linux rendszerű virtuális gépek átfogó figyeléséhez és kezeléséhez. A virtuális gép futtatható az Azure-ban, egy másik felhőben vagy a helyszínen.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | A Log Analytics-ügynök közvetlenül vagy a System Center Operations Manageren keresztül csatlakozik az Azure Monitorhoz, és lehetővé teszi, hogy adatokat gyűjtsön az Ön által konfigurált adatforrásokból vagy az alkalmazásokba további betekintést nyújtó figyelési megoldásokból. fut a virtuális gépen. | [Ügynökadatforrások az Azure Monitorban](agent-data-sources.md)<br>[Az Operations Manager csatlakoztatása az Azure Monitorhoz](om-agents.md) |
| Virtuálisgép-tárolás | A virtuális gépekhez készült Azure Monitor a Log Analytics-ügynök segítségével tárolja az egészségügyi állapot adatait egy egyéni helyen. További információt a következő szakaszban talál.  |


### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez 
[Az Azure Monitor virtuális gépekhez](../insights/vminsights-overview.md) személyre szabott figyelési élményt nyújt az Azure Monitor alapvető funkcióin túli funkciókat biztosító virtuális gépek számára. Windows- és Linux-beli virtuális gépeken egy függőségi ügynökre van szükség, amely integrálható a Log Analytics-ügynökkel a virtuális gépen futó folyamatokra és a külső folyamatfüggőségekre vonatkozó felderített adatok gyűjtéséhez.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Az ügynök folyamataira és függőségeire vonatkozó adatokat tárolja. | [Az Azure Monitor virtuális gépekhez (előzetes verzió) leképezésének használata az alkalmazás-összetevők megismeréséhez](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Alkalmazáskód
Az Azure Monitor részletes alkalmazásfigyelése az [Application Insights](https://docs.microsoft.com/azure/application-insights/) szolgáltatással történik, amely adatokat gyűjt a különböző platformokon futó alkalmazásokból. Az alkalmazás futtatható az Azure-ban, egy másik felhőben vagy a helyszínen.

![Alkalmazásadatgyűjtés](media/data-sources/applications.png)


### <a name="application-data"></a>Alkalmazásadatok
Ha egy instrumentation csomag telepítésével engedélyezi az Application Insights alkalmazást egy alkalmazáshoz, az az alkalmazás teljesítményével és működésével kapcsolatos metrikákat és naplókat gyűjt. Az Application Insights tárolja az összegyűjtött adatokat ugyanabban az Azure Monitor adatplatform más adatforrások által használt. Ez magában foglalja a kiterjedt eszközöket az adatok elemzéséhez, de más forrásokból származó adatokkal is elemezheti azokat olyan eszközök kel, mint a Metrics Explorer és a Log Analytics.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Az alkalmazás működési adatai, beleértve az oldalnézeteket, az alkalmazáskérelmeket, a kivételeket és a nyomkövetéseket. | [Naplóadatok elemzése az Azure Monitorban](../log-query/log-query-overview.md) |
|                    | Az alkalmazásleképezés és a telemetriai-korreláció támogatásához az alkalmazás-összetevők közötti függőségi információk. | [Telemetriai korreláció az Application Insightsban](../app/correlation.md) <br> [Alkalmazástérkép](../app/app-map.md) |
|            | A rendelkezésre állási tesztek eredményei, amelyek tesztelik az alkalmazás rendelkezésre állását és válaszképességét a nyilvános internet különböző helyeiről. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
| Az Azure Monitor metrikák | Az Application Insights az alkalmazásban az Azure Monitor metrika-adatbázisába definiált egyéni metrikákon kívül az alkalmazás teljesítményét és működését leíró metrikákat gyűjt. | [Naplóalapú és előre összesített metrikák az Application Insightsban](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights API egyéni eseményekhez és metrikákhoz](../app/api-custom-events-metrics.md) |
| Azure Storage | Alkalmazásadatok küldése az Azure Storage-ba archiváláshoz. | [Telemetria exportálása az Application Insightsból](../app/export-telemetry.md) |
|            | A rendelkezésre állási tesztek részleteit az Azure Storage tárolja. Használja az Application Insights az Azure Portalon letölthető a helyi elemzéshez. A rendelkezésre állási tesztek eredményeit az Azure Monitor naplók tárolják. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
|            | A Profiler-nyomkövetési adatok az Azure Storage-ban tárolódnak. Használja az Application Insights az Azure Portalon letölthető a helyi elemzéshez.  | [Az Azure-beli éles alkalmazások profilkészítési alkalmazásai az Application Insights segítségével](../app/profiler-overview.md) 
|            | A kivételek egy részhalmazához rögzített pillanatkép-adatok hibakeresési pillanatkép-adatokat az Azure Storage tárolja. Használja az Application Insights az Azure Portalon letölthető a helyi elemzéshez.  | [A pillanatképek működésének megmunkálása](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Felügyeleti megoldások és elemzések
[Figyelési megoldások](../insights/solutions.md) és [insights](../insights/insights-overview.md) adatokat gyűjt, hogy további betekintést egy adott szolgáltatás vagy alkalmazás működésébe. Különböző alkalmazásszinteken, sőt több rétegben lévő erőforrásokat is kezelhetnek.

### <a name="monitoring-solutions"></a>Monitorozási megoldások

| Cél | Leírás | Referencia
|:---|:---|:---|
| Azure Monitor-naplók | Figyelési megoldások adatokat gyűjt az Azure Monitor naplók, ahol lehet elemezni a lekérdezési nyelv vagy [a megoldásban](view-designer.md) jellemzőnézetek használatával. | [Adatgyűjtési részletek az Azure-beli figyelési megoldásokhoz](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
[Az Azure Monitor tárolók](../insights/container-insights-overview.md) személyre szabott figyelési élményt nyújt az [Azure Kubernetes-szolgáltatás (AKS)](/azure/aks/)számára. További adatokat gyűjt ezekről az erőforrásokról az alábbi táblázatban ismertetett.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Az AKS figyelési adatait tárolja, beleértve a készletet, a naplókat és az eseményeket. Metrika adatok is tárolja naplók annak érdekében, hogy kihasználja az elemzési funkció a portálon. | [AKS-fürt teljesítményvizsgálata tárolókhoz készült Azure Monitor segítségével](../insights/container-insights-analyze.md) |
| Az Azure Monitor metrikák | Metrika adatok tárolása a metrika adatbázisban a vizualizáció és a riasztások. | [Tárolómetrikák megtekintése a metrikák kezelőjében](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Közvetlen hozzáférést biztosít az Azure Kubernetes-szolgáltatás (AKS) tárolónaplókhoz (stdout/stderror), eseményekhez és pod metrikáihoz a portálon. | [A Kubernetes-naplók, események és pod-metrikák megtekintése valós időben](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
[Az Azure Monitor virtuális gépekhez](../insights/vminsights-overview.md) személyre szabott élményt biztosít a virtuális gépek figyeléséhez. Az Azure Monitor virtuális gépekhez gyűjtött adatok leírása a fenti [operációs rendszer (vendég)](#operating-system-guest) szakaszban található.

## <a name="custom-sources"></a>Egyéni források
Az alkalmazás szabványos szintjein kívül előfordulhat, hogy figyelnie kell más erőforrásokat, amelyek telemetriai adatokkal rendelkeznek, amelyek nem gyűjthetők a többi adatforrással. Ezekhez az erőforrásokhoz írja ezeket az adatokat metrikák vagy naplók egy Azure Monitor API használatával.

![Egyéni gyűjtemény](media/data-sources/custom.png)

| Cél | Módszer | Leírás | Referencia |
|:---|:---|:---|:---|
| Azure Monitor-naplók | Data Collector API | Naplóadatok gyűjtése bármely REST-ügyféltől, és tárolja a Log Analytics-munkaterületen. | [Naplóadatok küldése az Azure Monitornak a HTTP-adatgyűjtő API-val (nyilvános előzetes verzió)](data-collector-api.md) |
| Az Azure Monitor metrikák | Egyéni metrikák API | Metrikaadatok gyűjtése bármely REST-ügyfélből, és az Azure Monitor metrika-adatbázisában tárolható. | [Egyéni metrikák küldése egy Azure-erőforráshoz az Azure Monitor metrikatárolóba rest API használatával](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Egyéb szolgáltatások
Az Azure más szolgáltatásai adatokat írnak az Azure Monitor adatplatformjára. Ez lehetővé teszi, hogy elemezze az ilyen szolgáltatások által gyűjtött adatokat az Azure Monitor által gyűjtött adatokkal, és ugyanazokat az elemzési és vizualizációs eszközöket használja ki.

| Szolgáltatás | Cél | Leírás | Referencia |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor-naplók | Az Azure Security Center tárolja a naplóanalytics-munkaterületen gyűjtött biztonsági adatokat, amely lehetővé teszi azok elemzését az Azure Monitor által gyűjtött egyéb naplóadatokkal.  | [Adatgyűjtés az Azure Security Centerben](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor-naplók | Az Azure Sentinel a különböző adatforrásokból gyűjtött adatokat egy Log Analytics-munkaterületen tárolja, amely lehetővé teszi azok elemzését az Azure Monitor által gyűjtött egyéb naplóadatokkal.  | [Adatforrások csatlakoztatása](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitor által gyűjtött figyelési adatok típusáról,](data-platform.md) valamint az adatok megtekintéséről és elemzéséről.
- Sorolja fel a [különböző helyeket, ahol az Azure-erőforrások adatokat tárolnak,](data-locations.md) és hogyan érheti el azokat. 
