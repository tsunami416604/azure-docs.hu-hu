---
title: Adatforrások a Azure Monitorban | Microsoft Docs
description: Ismerteti az Azure-erőforrások és a rajtuk futó alkalmazások állapotának és teljesítményének figyeléséhez rendelkezésre álló információkat.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: 164d1db1a80ef577792740f67a27a856a4ba4c3b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972931"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>A Azure Monitor figyelési adatforrásai
Azure Monitor egy [közös figyelési adatplatformon](data-platform.md) alapul, amely [naplókat](data-platform-logs.md) és [mérőszámokat](data-platform-metrics.md)tartalmaz. Az adatok ebbe a platformba való gyűjtése lehetővé teszi, hogy a több erőforrásból származó adatok együttesen legyenek elemezve az Azure Monitor-ban lévő eszközök közös eszközkészletének használatával. A figyelési adatokat más helyszínekre is elküldheti bizonyos forgatókönyvek támogatása céljából, és egyes erőforrások a naplókba vagy metrikába való összegyűjtésük előtt más helyszínekre is írhatnak.

Ez a cikk az Azure-erőforrások által létrehozott figyelési adatok mellett Azure Monitor által gyűjtött figyelési adatok különböző forrásait ismerteti. A hivatkozások részletes információkkal szolgálnak az adatok különböző helyekre való összegyűjtéséhez szükséges konfigurációról.

## <a name="application-tiers"></a>Alkalmazásrétegek

Az Azure-alkalmazásokból származó monitorozási adatok forrásai rétegekbe rendezhetők, a legmagasabb szinten pedig az alkalmazás, és az alacsonyabb szintek az Azure platform összetevői. Az egyes rétegek adatainak elérési módja változó. Az alkalmazási rétegeket az alábbi táblázat foglalja össze, és az egyes rétegekben található megfigyelési adatforrások az alábbi részekben láthatók. Az egyes adatterületek leírását és az azokhoz való hozzáférés módját lásd: az [Azure-beli adathelyek figyelése](data-locations.md) .


![Megfigyelési szintek](../media/overview/overview.png)


### <a name="azure"></a>Azure
Az alábbi táblázat röviden leírja az Azure-ra jellemző alkalmazási szinteket. Az alábbi szakaszban található további részletekért lásd a hivatkozást.

| Szint | Leírás | Gyűjtési módszer |
|:---|:---|:---|
| [Azure-bérlő](#azure-tenant) | A bérlői szintű Azure-szolgáltatások (például Azure Active Directory) működésével kapcsolatos információk. | Megtekintheti a HRE a portálon, vagy megadhatja a gyűjteményt Azure Monitor a bérlői diagnosztikai beállítások használatával. |
| [Azure-előfizetés](#azure-subscription) | Az Azure-előfizetésben, például a Resource Managerben és a Service Healthban található erőforrások közötti szolgáltatások állapotával és kezelésével kapcsolatos adat. | Tekintse meg a portálon, vagy konfigurálja a gyűjteményt Azure Monitor egy log-profillal. |
| [Azure-erőforrások](#azure-resources) |  Az egyes Azure-erőforrások működésével és teljesítményével kapcsolatos információk. | Automatikusan összegyűjtött metrikák, megtekintés Metrikaböngészőban.<br>A diagnosztikai beállítások konfigurálása a naplók Azure Monitor való összegyűjtéséhez.<br>Az egyes erőforrástípusok részletesebb figyeléséhez rendelkezésre álló figyelési megoldások és információk. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, más felhő vagy helyszíni 
Az alábbi táblázat röviden ismerteti az Azure-ban, egy másik felhőben vagy a helyszínen található alkalmazások rétegeit. Az alábbi szakaszban található további részletekért lásd a hivatkozást.

| Szint | Leírás | Gyűjtési módszer |
|:---|:---|:---|
| [Operációs rendszer (vendég)](#operating-system-guest) | A számítási erőforrások operációs rendszerére vonatkozó adatok. | Telepítse Log Analytics ügynököt az ügyfél-adatforrások Azure Monitor és függőségi ügynökbe való gyűjtéséhez az Azure Monitor for VMst támogató függőségek összegyűjtéséhez.<br>Az Azure Virtual Machines esetében telepítse az Azure diagnosztikai bővítményt a naplók és a metrikák Azure Monitorba való gyűjtéséhez. |
| [Alkalmazás kódja](#application-code) | A tényleges alkalmazás és kód teljesítményével és működésével kapcsolatos adatok, beleértve a teljesítmény-nyomkövetést, az alkalmazás naplóit és a felhasználói telemetria. | A kód kiszámításával adatokat gyűjthet a Application Insightsba. |
| [Egyéni források](#custom-sources) | Külső szolgáltatásokból vagy más összetevőkből vagy eszközökről származó adatok. | Napló-vagy metrikák adatainak összegyűjtése bármely REST-ügyfélről Azure Monitorba. |

## <a name="azure-tenant"></a>Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetria a bérlői szintű szolgáltatásokból, például a Azure Active Directoryból gyűjtik össze.

![Azure-bérlők gyűjteménye](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Naplók Azure Active Directory
[Azure Active Directory jelentéskészítés](../../active-directory/reports-monitoring/overview-reports.md) a bejelentkezési tevékenység előzményeit és az adott bérlőn belül végrehajtott módosítások naplózási nyomvonalát tartalmazza. 

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor-naplók | Konfigurálja az Azure AD-naplókat a Azure Monitorba való gyűjtéshez, hogy elemezze azokat más figyelési adatokkal. | [Azure AD-naplók integrálása Azure Monitor naplókkal (előzetes verzió)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Azure AD-naplók exportálása az Azure Storage-ba archiválás céljából. | [Oktatóanyag: Azure AD-naplók archiválása Azure Storage-fiókba (előzetes verzió) @no__t – 0 |
| Eseményközpont | Azure AD-naplók továbbítása más helyszínekre Event Hubs használatával. | [Oktatóanyag: Stream Azure Active Directory naplók egy Azure Event hub-ba (előzetes verzió) ](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-előfizetés
Telemetria az Azure-előfizetés állapotával és működésével kapcsolatos.

![Azure-előfizetés](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-tevékenységnapló 
Az [Azure-tevékenység naplója](activity-logs-overview.md) tartalmazza a szolgáltatás állapotával kapcsolatos rekordokat, valamint az Azure-előfizetésében található erőforrásokon végrehajtott konfigurációs módosításokat tartalmazó rekordokat. A tevékenység naplója az összes Azure-erőforrás számára elérhető, és a _külső_ nézetet jelöli.

| Destination | Leírás | Hivatkozás |
|:---|:---|
| Tevékenységnapló | A rendszer begyűjti a tevékenység naplóját a saját adattárolójába, amelyet a Azure Monitor menüből tekinthet meg, vagy a használatával tevékenységi naplók létrehozására vonatkozó riasztásokat hozhat létre. | [A tevékenység naplójának lekérdezése a Azure Portal](activity-log-view.md#azure-portal) |
| Azure Monitor-naplók | Konfigurálja Azure Monitor naplókat a tevékenység naplójának összegyűjtéséhez, hogy elemezze más megfigyelési adatokkal. | [Azure-beli tevékenység-naplók gyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exportálja a tevékenység naplóját az Azure Storage-ba archiválás céljából. | [Archiválási tevékenység naplója](activity-log-export.md#archive-activity-log)  |
| Event Hubs | A tevékenység naplójának továbbítása más helyszínekre Event Hubs használatával | [Adatfolyam-tevékenység naplója az Event hub-](activity-log-export.md#stream-activity-log-to-event-hub)ba. |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) információt nyújt az előfizetésében lévő Azure-szolgáltatások állapotáról, amelyet az alkalmazás és az erőforrások támaszkodnak.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Tevékenységnapló<br>Azure Monitor-naplók | Service Health rekordokat az Azure-tevékenység naplójában tárolja a rendszer, így megtekintheti őket a Azure Portalban, vagy elvégezheti a tevékenység naplójában végrehajtható egyéb tevékenységeket. | [Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon](service-notifications.md) |


## <a name="azure-resources"></a>Azure-erőforrások
A mérőszámok és az erőforrás-naplók információt nyújtanak az Azure-erőforrások _belső_ műveleteiről. Ezek a legtöbb Azure-szolgáltatáshoz elérhetők, és a figyelési megoldások és elemzések további adatokat gyűjtenek az egyes szolgáltatásokhoz.

![Azure-erőforrások gyűjteménye](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Platform metrikái 
A legtöbb Azure-szolgáltatás olyan [platform-metrikákat](data-platform-metrics.md) küld, amelyek a teljesítményt és a műveletet közvetlenül a metrikák adatbázisára mutatnak. Az [egyes típusú erőforrások esetében az adott mérőszámok eltérőek lesznek](metrics-supported.md). 

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor metrikák | A platform metrikái a konfiguráció nélkül fognak írni a Azure Monitor metrikai adatbázisba. A platform metrikáinak elérése Metrikaböngészőból.  | [Bevezetés az Azure Metrikaböngésző használatába](metrics-getting-started.md)<br>[Támogatott metrikák Azure Monitor](metrics-supported.md) |
| Azure Monitor-naplók | A platform metrikáinak másolása a trendek és egyéb elemzések naplókba Log Analytics használatával. | [Az Azure Diagnostics közvetlen Log Analytics](resource-logs-collect-workspace.md) |
| Event Hubs | Stream metrikák más helyszínekre Event Hubs használatával. |[Az Azure monitoring-adattovábbítása egy Event hub-ba külső eszköz általi felhasználás céljából](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Erőforrásnaplók
Az [erőforrás-naplók](resource-logs-overview.md) betekintést nyújtanak az Azure-erőforrások _belső_ műveleteibe.  Az erőforrás-naplók automatikusan létrejönnek, de létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy az egyes erőforrásokhoz hogyan kell gyűjteni a célhelyet.

A konfigurációs követelmények és az erőforrás-naplók tartalma az erőforrástípus szerint változik, és nem minden szolgáltatás hozza létre őket. Az egyes szolgáltatásokról és a részletes konfigurációs eljárásokra mutató hivatkozásokról a [támogatott szolgáltatások, sémák és kategóriák találhatók az Azure-erőforrás naplóiban](diagnostic-logs-schema.md) . Ha a szolgáltatás nem szerepel ebben a cikkben, akkor a szolgáltatás jelenleg nem hoz létre erőforrás-naplókat.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor-naplók | Az erőforrás-naplók elküldése Azure Monitor naplókba más összegyűjtött naplózási adatok elemzéséhez. | [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen Azure Monitor](resource-logs-collect-storage.md) |
| Storage | Erőforrás-naplók küldése az Azure Storage-ba archiválás céljából. | [Azure-beli erőforrás-naplók archiválása](resource-logs-collect-workspace.md) |
| Event Hubs | Az erőforrás-naplók továbbítása más helyszínekre Event Hubs használatával. |[Azure-beli erőforrás-naplók továbbítása egy Event hubhoz](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Operációs rendszer (vendég)
A számítási erőforrások az Azure-ban, más felhőkben, a helyszínen pedig vendég operációs rendszert kell figyelni. Egy vagy több ügynök telepítésével telemetria gyűjthet a vendégtől a Azure Monitorba, hogy elemezze azokat ugyanazzal a figyelési eszközökkel, mint maguk az Azure-szolgáltatások.

![Azure számítási erőforrások gyűjtése](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure diagnosztikai bővítmény
Az Azure Diagnostics bővítmény Azure-beli virtuális gépekhez való engedélyezése lehetővé teszi naplók és metrikák gyűjtését az Azure számítási erőforrások vendég operációs rendszeréről, beleértve az Azure Cloud Service (klasszikus) webes és feldolgozói szerepköröket, Virtual Machinesokat, virtuális gépeket méretezési csoportok és Service Fabric.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Storage | Ha engedélyezi a diagnosztikai bővítményt, a rendszer alapértelmezés szerint egy Storage-fiókba ír. | [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](diagnostics-extension-to-storage.md) |
| Azure Monitor metrikák | Ha a diagnosztikai bővítményt a teljesítményszámlálók gyűjtésére konfigurálja, a rendszer a Azure Monitor metrikák adatbázisába írja. | [Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Resource Manager-sablon használatával a Windows rendszerű virtuális gépekhez](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Naplók Application Insights | Naplók és teljesítményszámlálók gyűjtése a számítási erőforrásból, amely támogatja az alkalmazás más alkalmazásadatok alapján történő elemzését. | [A felhőalapú szolgáltatás, a virtuális gép vagy a Service Fabric diagnosztikai adatbázis küldése Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Konfigurálja a diagnosztikai bővítményt úgy, hogy az Event Hubs használatával továbbítsa az adatait más helyszínekre.  | [Azure Diagnostics adatok továbbítása a gyors elérési úton a Event Hubs használatával](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics ügynök 
Telepítse a Log Analytics-ügynököt a Windows-vagy Linux-alapú virtuális gépek átfogó monitorozásához és felügyeletéhez. A virtuális gép az Azure-ban, egy másik felhőben vagy a helyszínen is futtatható.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor-naplók | A Log Analytics ügynök közvetlenül vagy System Center Operations Manageron keresztül csatlakozik Azure Monitorhoz, és lehetővé teszi az adatok gyűjtését olyan adatforrásokból, amelyeket Ön konfigurál, vagy olyan figyelési megoldásokból, amelyek további elemzéseket nyújtanak az alkalmazásokhoz fut a virtuális gépen. | [Ügynök-adatforrások a Azure Monitorban](agent-data-sources.md)<br>[Operations Manager összekötése a Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez 
[Azure monitor for VMS](../insights/vminsights-overview.md) testre szabott figyelési élményt biztosít a virtuális gépek számára, amelyek az alapvető Azure monitor funkciókon felül szolgáltatásokat biztosítanak, beleértve a szolgáltatás állapotát és a virtuális gép állapotát. A Windows-és Linux-alapú virtuális gépeken olyan Dependency Agent igényel, amely integrálható a Log Analytics-ügynökkel a virtuális gépen és a külső folyamat függőségein futó folyamatok felderített adatainak összegyűjtéséhez.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor-naplók | Az ügynök folyamataira és függőségeire vonatkozó információkat tárolja. | [Az alkalmazás-összetevők megismerése Azure Monitor for VMs (előzetes verzió) leképezés használatával](../insights/vminsights-maps.md) |
| VM-tároló | Azure Monitor for VMs az egészségügyi állapot adatait egy egyéni helyen tárolja. Ez csak az [Azure Resource health Rest APIon](/rest/api/resourcehealth/)kívül a Azure Portal Azure monitor for VMS számára érhető el. | [Az Azure-beli virtuális gépek állapotának megismerése](../insights/vminsights-health.md)<br>[Azure Resource Health REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Alkalmazás kódja
A Azure Monitor részletes alkalmazás-figyelése [Application Insights](https://docs.microsoft.com/azure/application-insights/) , amely különböző platformokon futó alkalmazások adatait gyűjti. Az alkalmazás az Azure-ban, egy másik felhőben vagy a helyszínen is futtatható.

![Alkalmazásadatok gyűjtése](media/data-sources/applications.png)


### <a name="application-data"></a>Alkalmazásadatok
Ha a rendszerállapot-csomagok telepítésével engedélyezi a Application Insights alkalmazást, az az alkalmazás teljesítményével és működésével kapcsolatos metrikákat és naplókat gyűjt. Application Insights az általa gyűjtött adatokat a más adatforrások által használt Azure Monitor adatplatformon tárolja. Kiterjedt eszközöket tartalmaz az adatok elemzéséhez, de más forrásokból származó adatokkal is elemezheti azokat olyan eszközökkel, mint például a Metrikaböngésző és a Log Analytics.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor-naplók | Az alkalmazás üzemeltetési adatai, beleértve az oldalletöltések, az alkalmazásra vonatkozó kérelmek, a kivételek és a Nyomkövetések. | [Naplófájlok elemzése Azure Monitor](../log-query/log-query-overview.md) |
|                    | Függőségi információk az alkalmazás-összetevők között az alkalmazások leképezésének és a telemetria korrelációjának támogatásához. | [Telemetria korreláció a Application Insightsban](../app/correlation.md) <br> [Alkalmazástérkép](../app/app-map.md) |
|            | A rendelkezésre állási tesztek eredményei, amelyek tesztelik az alkalmazás rendelkezésre állását és reagálását a nyilvános interneten lévő különböző helyekről. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
| Azure Monitor metrikák | A Application Insights az alkalmazás teljesítményének és működésének leírását, valamint az alkalmazásban az Azure Monitor metrikai adatbázisba definiált egyéni metrikákat is összegyűjti. | [Napló alapú és előre összevont metrikák a Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights API egyéni eseményekhez és mérőszámokhoz](../app/api-custom-events-metrics.md) |
| Azure Storage | Alkalmazásadatok küldése az Azure Storage-ba archiválás céljából. | [Telemetria exportálása az Application Insightsból](../app/export-telemetry.md) |
|            | A rendelkezésre állási tesztek részleteit az Azure Storage tárolja. A helyi elemzéshez a Azure Portal Application Insights használatával töltheti le. A rendelkezésre állási tesztek eredményei Azure Monitor naplókban tárolódnak. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
|            | A Profiler nyomkövetési adatait az Azure Storage tárolja. A helyi elemzéshez a Azure Portal Application Insights használatával töltheti le.  | [Profil éles alkalmazások az Azure-ban Application Insights](../app/profiler-overview.md) 
|            | A kivételek részhalmaza számára rögzített pillanatkép-adatok hibakeresése az Azure Storage-ban történik. A helyi elemzéshez a Azure Portal Application Insights használatával töltheti le.  | [A pillanatképek működése](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>A megoldások és az adatvizsgálatok figyelése
A [monitorozási megoldások](../insights/solutions.md) és elemzések [adatokat gyűjtenek, hogy](../insights/insights-overview.md) további betekintést nyújtsanak egy adott szolgáltatás vagy alkalmazás működéséhez. A különböző alkalmazási rétegekben és akár több rétegben is megoszthatják az erőforrásokat.

### <a name="monitoring-solutions"></a>Monitorozási megoldások

| Destination | Leírás | Hivatkozás
|:---|:---|:---|
| Azure Monitor-naplók | A figyelési megoldások adatokat gyűjtenek Azure Monitor naplókba, ahol elemezni lehet a megoldásban általában szereplő lekérdezési nyelv vagy [nézetek](view-designer.md) használatával. | [Az Azure-beli monitorozási megoldások adatgyűjtési adatai](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolók számára
A [tárolók Azure monitor](../insights/container-insights-overview.md) testreszabott figyelési élményt biztosítanak az [Azure Kubernetes szolgáltatáshoz (ak)](/azure/aks/). A következő táblázatban ismertetett erőforrásokra vonatkozó további adatokat gyűjt.

| Destination | Leírás | Hivatkozás |
|:---|:---|:---|
| Azure Monitor-naplók | Tárolja a figyelési adattáblákat, beleértve a leltárt, a naplókat és az eseményeket. A metrikák adatait a rendszer a naplókban is tárolja, hogy az elemzési funkcióit kihasználja a portálon. | [AKS-fürt teljesítményvizsgálata tárolókhoz készült Azure Monitor segítségével](../insights/container-insights-analyze.md) |
| Azure Monitor metrikák | A metrikai adatok a metrikus adatbázisban tárolódnak a vizualizációk és a riasztások megjelenítéséhez. | [Tároló metrikáinak megtekintése a metrikák Explorerben](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | A közel valós idejű élmény érdekében Azure Monitor a tárolók közvetlenül az Azure Kubernetes szolgáltatásból származó adatokkal Azure Portal. | [Tárolónaplók valós idejű megtekintése a tárolókhoz készült Azure Monitorral (előzetes verzió)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
A [Azure monitor for VMS](../insights/vminsights-overview.md) testreszabott felhasználói élményt biztosít a virtuális gépek figyeléséhez. A Azure Monitor for VMs által gyűjtött adatok leírását a fenti [operációs rendszer (vendég)](#operating-system-guest) szakasza tartalmazza.

## <a name="custom-sources"></a>Egyéni források
Az alkalmazások standard szintjein kívül előfordulhat, hogy figyelnie kell más erőforrásokat is, amelyek nem gyűjthetők össze más adatforrásokkal a telemetria. Ezekhez az erőforrásokhoz a Azure Monitor API használatával írhatja be ezeket az adatokat mérőszámok vagy naplók számára.

![Egyéni gyűjtemény](media/data-sources/custom.png)

| Destination | Módszer | Leírás | Hivatkozás |
|:---|:---|:---|:---|
| Azure Monitor-naplók | Data Collector API | Gyűjtsön adatokat a REST-ügyfélről, és tárolja Log Analytics munkaterületen. | [Naplóbejegyzések küldése a Azure Monitornak a HTTP-adatgyűjtő API-val (nyilvános előzetes verzió)](data-collector-api.md) |
| Azure Monitor metrikák | Egyéni metrikák API | Metrikus adatokat gyűjthet a REST-ügyfélről, és Azure Monitor metrikák adatbázisában tárolja őket. | [Egyéni metrikák küldése az Azure-erőforrásokhoz az Azure Monitor metrikai tárolóba egy REST API használatával](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Egyéb szolgáltatások
Az Azure egyéb szolgáltatásai a Azure Monitor adatplatformba írnak be adatfájlokat. Ez lehetővé teszi a szolgáltatások által összegyűjtött adatok elemzését Azure Monitor által összegyűjtött adatokkal, és ugyanazokat az elemzési és vizualizációs eszközöket használják.

| Szolgáltatás | Destination | Leírás | Hivatkozás |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor-naplók | Azure Security Center tárolja az általa gyűjtött biztonsági adatokat egy Log Analytics munkaterületen, amely lehetővé teszi, hogy az a Azure Monitor által gyűjtött egyéb naplózási adatokkal elemezze.  | [Adatgyűjtés az Azure Security Centerben](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor-naplók | Az Azure Sentinel a különböző adatforrásokból gyűjtött adatokat egy Log Analytics munkaterületen tárolja, amely lehetővé teszi, hogy a rendszer a Azure Monitor által gyűjtött egyéb naplózási adatokkal elemezze.  | [Adatforrások összekötése](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>További lépések

- További információ a [Azure monitor által gyűjtött megfigyelési adatok típusairól](data-platform.md) , valamint az adatok megtekintésének és elemzésének módjáról.
- Sorolja fel azokat a [különböző helyeket, amelyekben az Azure-erőforrások tárolják az adattárolási](data-locations.md) és elérési módját. 
