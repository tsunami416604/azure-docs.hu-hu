---
title: A Azure Monitor által figyelt
description: A Azure Monitor által figyelt összes szolgáltatás és egyéb erőforrás referenciája.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/15/2020
ms.openlocfilehash: 1f76966b0ed82ae3d4b32e54627a28e99b0dba2f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505720"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Mi figyeli a Azure Monitor?
Ez a cikk a Azure Monitor által figyelt különböző alkalmazásokat és szolgáltatásokat ismerteti. 

## <a name="insights-and-core-solutions"></a>Áttekintések és alapvető megoldások
Az alapvető megállapítások és megoldások a Azure Monitor részét képezik, és követik az Azure támogatási és szolgáltatási szintű szerződéseit. Ezek az összes Azure-régióban támogatottak, ahol Azure Monitor érhető el.

### <a name="insights"></a>Insights

Az egyes alkalmazásokhoz és szolgáltatásokhoz testreszabott figyelési funkciókkal szolgálnak. A naplók és a metrikák összegyűjtését és elemzését végzik.

| Elemzések | Leírás |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Bővíthető Application Performance Management (APM) szolgáltatás az élő webalkalmazások bármely platformon való figyeléséhez. |
| [Azure Monitor tárolók számára](insights/container-insights-overview.md) | Az Azure Kubernetes Service (ak) szolgáltatásban üzemeltetett Azure Container Instances vagy felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményét figyeli. |
| [Cosmos DB Azure Monitor](insights/cosmosdb-insights-overview.md) | Áttekintést nyújt az összes Azure Cosmos DB-erőforrás általános teljesítményéről, hibáiról, kapacitásáról és működési állapotáról egy egységes interaktív felhasználói élményben. |
| [Azure Monitor hálózatok számára (előzetes verzió)](insights/network-insights-overview.md) | Átfogó képet nyújt az összes hálózati erőforrás állapotáról és mérőszámáról. A speciális keresési funkció segítségével azonosíthatja az erőforrás-függőségeket, és engedélyezheti az olyan forgatókönyveket, mint például a webhelyet üzemeltető erőforrás azonosítása, egyszerűen csak a webhely nevét keresi. |
[Erőforráscsoportok Azure Monitor (előzetes verzió)](insights/resource-group-insights.md) |  Az egyes erőforrások által észlelt problémák osztályozása és diagnosztizálása, valamint az erőforráscsoport állapotának és teljesítményének megtalálása. |
| [Azure Monitor Storage-hoz](insights/storage-insights-overview.md) | Átfogóan figyeli az Azure Storage-fiókokat az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. |
| [Azure Monitor virtuális gépekhez](insights/vminsights-overview.md) | Az Azure-beli virtuális gépek (VM) és a virtuálisgép-méretezési csoportok skálán való figyelését figyeli. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. |
| [Key Vault Azure Monitor (előzetes verzió)](./insights/key-vault-insights-overview.md) | A a kulcstartók átfogó figyelését teszi lehetővé a Key Vault kérelmek, a teljesítmény, a hibák és a késések egységes áttekintésével. |
| [Azure Monitor az Azure cache for Redis (előzetes verzió)](insights/redis-cache-insights-overview.md) |  Egységes, interaktív áttekintést nyújt a teljes teljesítményről, a hibákról, a kapacitásról és az üzemeltetési állapotról. |


### <a name="core-solutions"></a>Alapvető megoldások

A megoldások egy adott alkalmazáshoz vagy szolgáltatáshoz testreszabott naplózási lekérdezéseken és nézeteken alapulnak. Csak a naplók gyűjtésére és elemzésére szolgálnak, és az elemzések során időben elavultak.

| Megoldás | Leírás |
|:---|:---|
| [Ügynök állapota](insights/solution-agenthealth.md) | Log Analytics ügynökök állapotának és konfigurációjának elemzése. |
| [Riasztások kezelése](platform/alert-management-solution.md) | System Center Operations Manager, Nagios vagy Zabbix által gyűjtött riasztások elemzése. |
| [Service Map](insights/service-map.md) | A automatikusan feltérképezi az alkalmazás-összetevőket Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. |



## <a name="azure-services"></a>Azure-szolgáltatások
A következő táblázat az Azure-szolgáltatásokat és a Azure Monitorbe gyűjtött adatokat sorolja fel. 

- Metrikák – a szolgáltatás automatikusan Azure Monitor mérőszámokra gyűjti a metrikákat. 
- Naplók – a szolgáltatás támogatja a diagnosztikai beállításokat, amelyek összegyűjthetik a naplók és a metrikák összegyűjtését Azure Monitor.
- Betekintés – a szolgáltatás számára elérhető egy betekintés, amely testreszabott figyelési élményt biztosít a szolgáltatás számára.

| Szolgáltatás | Mérőszámok | Naplók | Elemzések | Jegyzetek |
|:---|:---|:---|:---|:---|
|Active Directory | Nem | Igen | [Igen](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nem | Nem | Nem |  |
|Active Directory Domain Services | Nem | Igen | Nem |  |
|Tevékenységnapló | Nem | Igen | Nem | |
|Advanced Threat Protection | Nem | Nem | Nem |  |
|Advisor | Nem | Nem | Nem |  |
|AI Builder | Nem | Nem | Nem |  |
|Analysis Services | Igen | Igen | Nem |  |
|API for FHIR | Nem | Nem | Nem |  |
|API Management | Igen | Igen | Nem |  |
|App Service | Igen | Igen | Nem |  |
|AppConfig | Nem | Nem | Nem |  |
|Application Gateway | Igen | Igen | Nem |  |
|Igazolási szolgáltatás | Nem | Nem | Nem |  |
|Automation | Igen | Igen | Nem |  |
|Azure Service Manager (RDFE) | Nem | Nem | Nem |  |
|Backup | Nem | Igen | Nem |  |
|Bastion | Nem | Nem | Nem |  |
|Batch | Igen | Igen | Nem |  |
|Batch AI | Nem | Nem | Nem |  |
|Blockchain szolgáltatás | Nem | Igen | Nem |  |
|Blueprints | Nem | Nem | Nem |  |
|Robotszolgáltatás | Nem | Nem | Nem |  |
|Cloud Services | Igen | Igen | Nem | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök.  |
|Cloud Shell | Nem | Nem | Nem |  |
|Cognitive Services | Igen | Igen | Nem |  |
|Container Instances | Igen | Nem | Nem |  |
|Container Registry | Igen | Igen | Nem |  |
|Content Delivery Network (CDN) | Nem | Igen | Nem |  |
|Cosmos DB | Igen | Igen | [Igen](insights/cosmosdb-insights-overview.md) |  |
|Költségkezelés | Nem | Nem | Nem |  |
|Data Box | Nem | Nem | Nem |  |
|Data Catalog Gen2 | Nem | Nem | Nem |  |
|Data Explorer | Igen | Igen | Nem |  |
|Data Factory | Igen | Igen | Nem |  |
|Data Factory v2 | Nem | Igen | Nem |  |
|Data Share | Nem | Nem | Nem |  |
|Database for MariaDB | Igen | Igen | Nem |  |
|Database for MySQL | Igen | Igen | Nem |  |
|Database for PostgreSQL | Igen | Igen | Nem |  |
|Database Migration Service | Nem | Nem | Nem |  |
|Databricks | Nem | Igen | Nem |  |
|DDoS Protection | Igen | Igen | Nem |  |
|DevOps | Nem | Nem | Nem |  |
|DNS | Igen | Nem | Nem |  |
|Tartománynevek | Nem | Nem | Nem |  |
|DPS | Nem | Nem | Nem |  |
|Dynamics 365 Customer engagement | Nem | Nem | Nem |  |
|Dynamics 365 – Pénzügy és műveletek | Nem | Nem | Nem |  |
|Event Grid | Igen | Nem | Nem |  |
|Event Hubs | Igen | Igen | Nem |  |
|ExpressRoute | Igen | Igen | Nem |  |
|Tűzfal | Igen | Igen | Nem |  |
|Front Door | Igen | Igen | Nem |  |
|Függvények | Igen | Igen | Nem |  |
|HDInsight | Nem | Igen | Nem |  |
|HPC Cache | Nem | Nem | Nem |  |
|Information Protection | Nem | Igen | Nem |  |
|Intune | Nem | Igen | Nem |  |
|IoT Central | Nem | Nem | Nem |  |
|IoT Hub | Igen | Igen | Nem |  |
|Key Vault | Igen | Igen | [Igen](./insights/key-vault-insights-overview.md) |  |
|Kubernetes Service (AKS) | Nem | Nem | [Igen](insights/container-insights-overview.md)  |  |
|Load Balancer | Igen | Igen | Nem |  |
|Logic Apps | Igen | Igen | Nem |  |
|Machine Learning szolgáltatás | Nem | Nem | Nem |  |
|Felügyelt alkalmazások  | Nem | Nem | Nem |  |
|Maps  | Nem | Nem | Nem |  |
|Media Services | Igen | Igen | Nem |  |
|Microsoft Flow | Nem | Nem | Nem |  |
|Microsoft Felügyelt asztal | Nem | Nem | Nem |  |
|Microsoft PowerApps | Nem | Nem | Nem |  |
|Microsoft Social Engagement | Nem | Nem | Nem |  |
|Microsoft Stream | Igen | Igen | Nem |  |
|Migrate | Nem | Nem | Nem |  |
|Multi-Factor Authentication | Nem | Igen | Nem |  |
|Network Watcher | Igen | Igen | Nem |  |
|Notification Hubs | Igen | Nem | Nem |  |
|Open Datasets | Nem | Nem | Nem |  |
|Szabályzat | Nem | Nem | Nem |  |
|Power BI Embedded | Igen | Igen | Nem |  |
|Privát kapcsolat | Nem | Nem | Nem |  |
|Projekt-várólista kommunikációs platformja | Nem | Nem | Nem |  |
|Red Hat OpenShift | Nem | Nem | Nem |  |
|Redis Cache | Igen | Igen | [Igen](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Nem | Nem | Nem |  |
|Resource Manager | Nem | Nem | Nem |  |
|Kiskereskedelmi keresés – Bing alapján | Nem | Nem | Nem |  |
|Keresés | Igen | Igen | Nem |  |
|Service Bus | Igen | Igen | Nem |  |
|Service Fabric | Nem | Igen | Nem | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök.  |
|Regisztrációs portál | Nem | Nem | Nem |  |
|Site Recovery | Nem | Igen | Nem |  |
|Spring Cloud Service | Nem | Nem | Nem |  |
|SQL Data Warehouse | Igen | Igen | Nem |  |
|SQL Database | Igen | Igen | Nem |  |
|SQL Server Stretch Database | Igen | Igen | Nem |  |
|Verem | Nem | Nem | Nem |  |
|Storage | Igen | Nem | [Igen](insights/storage-insights-overview.md) |  |
|Tárolási gyorsítótár | Nem | Nem | Nem |  |
|Storage Sync Services | Nem | Nem | Nem |  |
|Stream Analytics | Igen | Igen | Nem |  |
|Time Series Insights | Igen | Igen | Nem |  |
|TINA | Nem | Nem | Nem |  |
|Traffic Manager | Igen | Igen | Nem |  |
|Univerzális nyomtatás | Nem | Nem | Nem |  |
|Virtual Machine Scale Sets | Nem | Igen | [Igen](insights/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök. |
|Virtual Machines | Igen | Igen | [Igen](insights/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök. |
|Virtual Network | Igen | Igen | [Igen](insights/network-insights-overview.md) |  |
|Virtual Network – NSG folyamatok naplói | Nem | Igen | Nem |  |
|VPN Gateway | Igen | Igen | Nem |  |
|Windows Virtual Desktop | Nem | Nem | Nem |  |


## <a name="product-integrations"></a>Termék-integrációk
Az alábbi táblázatban szereplő szolgáltatások és megoldások egy Log Analytics munkaterületen tárolják az adatokat, így a Azure Monitor által gyűjtött egyéb naplózási adatokkal is elemezhetők.

| Termék/szolgáltatás | Leírás |
|:---|:---|
| [Azure Automation](../automation/index.yml) | Az operációs rendszer frissítéseinek kezelése és a változások nyomon követése Windows és Linux rendszerű számítógépeken. Lásd: [change Tracking](../automation/change-tracking.md) és [Update Management](../automation/automation-update-management.md). |
| [Azure Information Protection](/azure/information-protection/) | A dokumentumok és e-mailek besorolása és opcionális védelemmel való ellátása. Lásd: [Azure Information Protection központi jelentéskészítése](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](../security-center/index.yml) | A biztonsági események összegyűjtése és elemzése, valamint a fenyegetések elemzésének elvégzése. Adatgyűjtés [Azure Security Center](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Különböző forrásokhoz, például az Office 365-hez és a Amazon Web Services Cloud Trailhez csatlakozik. Lásd: [az adatforrások összekötése](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Hozzon létre egy diagnosztikai beállítást, amely naplókat küld Azure Monitornak. Lásd: [naplózási információk küldése a Storage-ba, az Event hubokba vagy a log analyticsbe az Intune-ban (előzetes verzió)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Network (Hálózat)  | [Network Performance monitor](insights/network-performance-monitor.md) – a hálózati kapcsolat és a teljesítmény figyelése a szolgáltatás-és alkalmazás-végpontok számára.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) – naplók és mérőszámok elemzése az Azure Application Gatewayból.<br>[Traffic Analytics](../network-watcher/traffic-analytics.md) – elemzi Network Watcher hálózati biztonsági csoport (NSG) folyamatának naplóit, hogy betekintést nyújtson az Azure-felhőbe irányuló forgalomba. |
| [Office 365](insights/solution-office-365.md) | Az Office 365-környezet monitorozása. Frissített verzió, amely az Azure Sentinel használatával érhető el továbbfejlesztett bevezetéssel. |
| [SQL Analytics](insights/azure-sql.md) | Az Azure SQL Database-adatbázisok és a felügyelt SQL-példányok teljesítményének figyelése nagy méretekben és több előfizetésen keresztül. |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub eszközök állapotának és használatának nyomon követése. |
| [System Center Operations Manager](/system-center/scom) | Operations Manager ügynököktől származó adatok gyűjtése a felügyeleti csoport Azure Monitorhoz való csatlakoztatásával. Lásd: [a Operations Manager kapcsolódása Azure monitor](platform/om-agents.md)<br> [Operations Manager Assessment](insights/scom-assessment.md) megoldással értékelheti System Center Operations Manager felügyeleti csoportjának kockázatait és állapotát. |
| [Microsoft Teams-szobák](/microsoftteams/room-systems/azure-monitor-deploy) | A Microsoft Teams Rooms eszközeinek integrált, teljes körű felügyelete. |
| [Visual Studio App Center](/appcenter/) | Alkalmazások létrehozása, tesztelése és terjesztése, majd az állapotuk és a használat monitorozása. Lásd: [a Mobile App elemzése app Center és Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Megfelelőség Windows Update](/windows/deployment/update/update-compliance-get-started) – a Windows asztali frissítéseinek felmérése.<br>Az [asztali elemzések](/configmgr/desktop-analytics/overview) és a Configuration Manager integrálása lehetővé teszi a Windows-ügyfelek frissítési felkészültségével kapcsolatos tájékozott döntések meghozatalát. |



## <a name="other-solutions"></a>Egyéb megoldások
Más megoldások is elérhetők különböző alkalmazások és szolgáltatások figyelésére, de az aktív fejlesztés leállt, és előfordulhat, hogy az összes régióban nem érhető el. Ezeket az Azure Log Analytics adatfeldolgozási szolgáltatói szerződése szabályozza.

| Megoldás | Leírás |
|:---|:---|
| [Active Directory állapot-ellenőrzési](insights/ad-assessment.md) | A Active Directory környezetek kockázatának és állapotának felmérése. |
| [Active Directory replikáció állapota](insights/ad-replication-status.md) | Rendszeresen figyeli a Active Directory környezetet bármilyen replikációs hiba esetén. |
| [Activity log Analytics](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Tevékenységek naplójának bejegyzéseinek megtekintése. |
| [DNS Analytics (előzetes verzió)](insights/dns-analytics.md) | Gyűjti, elemzi és korrelálja a DNS-kiszolgálókkal kapcsolatos Windows DNS analitikai és naplózási naplókat és egyéb kapcsolódó adatokat. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Összegyűjtheti, megtekintheti és elemezheti Cloud Foundry rendszerállapot-és teljesítmény-mérőszámait több üzemelő példány között. |
| [Containers](insights/containers.md) | A Docker és a Windows-tároló gazdagépek megtekintése és kezelése. |
| [Igény szerinti értékelések](/services-hub/health/getting_started_with_on_demand_assessments) | A helyszíni, hibrid és Felhőbeli Microsoft technológiai környezetek rendelkezésre állásának, biztonságának és teljesítményének felmérése és optimalizálása. |
| [SQL Health-vizsgálat](insights/sql-assessment.md) | A SQL Server környezetek kockázatának és állapotának felmérése.  |
| [Átviteli adatok](insights/wire-data.md) | Összevont hálózati és teljesítményadatok a Windows rendszerhez csatlakoztatott és a Linux rendszerű számítógépeken a Log Analytics ügynökkel. |

## <a name="third-party-integration"></a>Harmadik féltől származó integráció

| Megoldás | Leírás |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Az IT Service Management-összekötő (ITSMC) lehetővé teszi, hogy az Azure-t egy támogatott IT Service Management- (ITSM-) termékhez/szolgáltatáshoz kapcsolja.  |


## <a name="resources-outside-of-azure"></a>Az Azure-on kívüli erőforrások
A Azure Monitor az Azure-on kívüli erőforrásokból is gyűjthet adatokat az alábbi táblázatban felsorolt módszerek használatával.

| Erőforrás | Metódus |
|:---|:---|
| Alkalmazások | Webalkalmazások monitorozása az Azure-on kívül Application Insights használatával. Lásd: [Mi az Application Insights?](./app/app-insights-overview.md) |
| Virtuális gépek | A Log Analytics ügynök használatával adatokat gyűjthet a virtuális gépek vendég operációs rendszeréről más felhőalapú környezetekben vagy helyszíni környezetben. Lásd: [a naplózási adatok összegyűjtése a log Analytics ügynökkel](platform/log-analytics-agent.md). |
| Ügyfél REST API | A különböző API-k képesek adatokat írni Azure Monitor naplókba és metrikába bármilyen REST API ügyfélről. Lásd: [naplófájlok küldésének Azure monitor a http](platform/data-collector-api.md) -adatgyűjtő API-val a naplókhoz, és [Egyéni metrikák küldése egy Azure-erőforrásnak a Azure monitor metrika-tárolóba](platform/metrics-store-custom-rest-api.md) a metrikák REST API használatával. |



## <a name="next-steps"></a>Következő lépések

- Tudjon meg többet a [Azure monitor adatplatformról, amely az elemzések és megoldások által gyűjtött naplókat és mérőszámokat tárolja](platform/data-platform.md).
- Az [Azure-erőforrások figyelésével kapcsolatos oktatóanyag](learn/tutorial-resource-logs.md)elvégzése.
- Végezzen el egy olyan [oktatóanyagot, amely Azure monitor naplókba tartozó adatelemzési napló lekérdezését írja](learn/tutorial-resource-logs.md)le.
- A [metrikák diagramjának létrehozásával Azure monitor mérőszámokban lévő adatok elemzéséhez](learn/tutorial-metrics-explorer.md)hajtson végre egy oktatóanyagot.

 
