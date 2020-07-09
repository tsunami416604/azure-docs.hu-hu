---
title: A Azure Monitor által figyelt
description: A Azure Monitor által figyelt összes szolgáltatás és egyéb erőforrás referenciája.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/15/2020
ms.openlocfilehash: 70c1b35759241c2fdf687e7b7042cf4a18232bf5
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085497"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Mi figyeli a Azure Monitor?
Ez a cikk a Azure Monitor által figyelt különböző alkalmazásokat és szolgáltatásokat ismerteti. 

## <a name="insights-and-core-solutions"></a>Áttekintések és alapvető megoldások
Az alapvető megállapítások és megoldások a Azure Monitor részét képezik, és követik az Azure támogatási és szolgáltatási szintű szerződéseit. Ezek az összes Azure-régióban támogatottak, ahol Azure Monitor érhető el.

### <a name="insights"></a>Insights

Az egyes alkalmazásokhoz és szolgáltatásokhoz testreszabott figyelési funkciókkal szolgálnak. A naplók és a metrikák összegyűjtését és elemzését végzik.

| Elemzések | Description |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Bővíthető Application Performance Management (APM) szolgáltatás az élő webalkalmazások bármely platformon való figyeléséhez. |
| [Azure Monitor tárolók számára](insights/container-insights-overview.md) | Az Azure Kubernetes Service (ak) szolgáltatásban üzemeltetett Azure Container Instances vagy felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményét figyeli. |
| [Cosmos DB Azure Monitor](insights/cosmosdb-insights-overview.md) | Áttekintést nyújt az összes Azure Cosmos DB-erőforrás általános teljesítményéről, hibáiról, kapacitásáról és működési állapotáról egy egységes interaktív felhasználói élményben. |
| [Azure Monitor hálózatok számára (előzetes verzió)](insights/network-insights-overview.md) | Átfogó képet nyújt az összes hálózati erőforrás állapotáról és mérőszámáról. A speciális keresési funkció segítségével azonosíthatja az erőforrás-függőségeket, és engedélyezheti az olyan forgatókönyveket, mint például a webhelyet üzemeltető erőforrás azonosítása, egyszerűen csak a webhely nevét keresi. |
[Erőforráscsoportok Azure Monitor (előzetes verzió)](insights/resource-group-insights.md) |  Az egyes erőforrások által észlelt problémák osztályozása és diagnosztizálása, valamint az erőforráscsoport állapotának és teljesítményének megtalálása. |
| [Azure Monitor Storage-hoz](insights/storage-insights-overview.md) | Átfogóan figyeli az Azure Storage-fiókokat az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. |
| [Azure Monitor virtuális gépekhez](insights/vminsights-overview.md) | Az Azure-beli virtuális gépek (VM) és a virtuálisgép-méretezési csoportok skálán való figyelését figyeli. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. |
| [Key Vault Azure Monitor (előzetes verzió)](insights/key-vaults-insights-overview.md) | A a kulcstartók átfogó figyelését teszi lehetővé a Key Vault kérelmek, a teljesítmény, a hibák és a késések egységes áttekintésével. |
| [Azure Monitor az Azure cache for Redis (előzetes verzió)](insights/redis-cache-insights-overview.md) |  Egységes, interaktív áttekintést nyújt a teljes teljesítményről, a hibákról, a kapacitásról és az üzemeltetési állapotról. |


### <a name="core-solutions"></a>Alapvető megoldások

A megoldások egy adott alkalmazáshoz vagy szolgáltatáshoz testreszabott naplózási lekérdezéseken és nézeteken alapulnak. Csak a naplók gyűjtésére és elemzésére szolgálnak, és az elemzések során időben elavultak.

| Megoldás | Description |
|:---|:---|
| [Ügynök állapota](insights/solution-agenthealth.md) | Log Analytics ügynökök állapotának és konfigurációjának elemzése. |
| [Riasztások kezelése](platform/alert-management-solution.md) | System Center Operations Manager, Nagios vagy Zabbix által gyűjtött riasztások elemzése. |
| [Szolgáltatástérkép](insights/service-map.md) | A automatikusan feltérképezi az alkalmazás-összetevőket Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. |



## <a name="azure-services"></a>Azure-szolgáltatások
A következő táblázat az Azure-szolgáltatásokat és a Azure Monitorbe gyűjtött adatokat sorolja fel. 

- Metrikák – a szolgáltatás automatikusan Azure Monitor mérőszámokra gyűjti a metrikákat. 
- Naplók – a szolgáltatás támogatja a diagnosztikai beállításokat, amelyek összegyűjthetik a naplók és a metrikák összegyűjtését Azure Monitor.
- Betekintés – a szolgáltatás számára elérhető egy betekintés, amely testreszabott figyelési élményt biztosít a szolgáltatás számára.

| Szolgáltatás | Mérőszámok | Naplók | Elemzések | Jegyzetek |
|:---|:---|:---|:---|:---|
|Active Directory | No | Yes | [Igen](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nem | Nem | Nem |  |
|Active Directory Domain Services | No | Yes | No |  |
|Tevékenységnapló | No | Yes | No | |
|Fejlett fenyegetésvédelem | Nem | Nem | Nem |  |
|Advisor | Nem | Nem | Nem |  |
|AI Builder | Nem | Nem | Nem |  |
|Analysis Services | Igen | Igen | No |  |
|API for FHIR | Nem | Nem | Nem |  |
|API Management | Igen | Igen | No |  |
|App Service | Igen | Igen | No |  |
|AppConfig | Nem | Nem | Nem |  |
|Application Gateway | Igen | Igen | No |  |
|Igazolási szolgáltatás | Nem | Nem | Nem |  |
|Automation | Igen | Igen | No |  |
|Azure Service Manager (RDFE) | Nem | Nem | Nem |  |
|Backup | No | Yes | No |  |
|Bastion | Nem | Nem | Nem |  |
|Batch | Igen | Igen | No |  |
|Batch AI | Nem | Nem | Nem |  |
|Blockchain szolgáltatás | No | Yes | No |  |
|Blueprints | Nem | Nem | Nem |  |
|Robotszolgáltatás | Nem | Nem | Nem |  |
|Cloud Services | Igen | Igen | No | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök.  |
|Cloud Shell | Nem | Nem | Nem |  |
|Cognitive Services | Igen | Igen | No |  |
|Tárolópéldányok | Yes | Nem | Nem |  |
|Container Registry | Igen | Igen | No |  |
|Content Delivery Network (CDN) | No | Yes | No |  |
|Cosmos DB | Igen | Igen | [Igen](insights/cosmosdb-insights-overview.md) |  |
|Költségkezelés | Nem | Nem | Nem |  |
|Data Box | Nem | Nem | Nem |  |
|Data Catalog Gen2 | Nem | Nem | Nem |  |
|Data Explorer | Igen | Igen | No |  |
|Data Factory | Igen | Igen | No |  |
|Data Factory v2 | No | Yes | No |  |
|Data Share | Nem | Nem | Nem |  |
|Database for MariaDB | Igen | Igen | No |  |
|Database for MySQL | Igen | Igen | No |  |
|Database for PostgreSQL | Igen | Igen | No |  |
|Database Migration Service | Nem | Nem | Nem |  |
|Databricks | No | Yes | No |  |
|DDoS Protection | Igen | Igen | No |  |
|DevOps | Nem | Nem | Nem |  |
|DNS | Yes | Nem | Nem |  |
|Tartománynevek | Nem | Nem | Nem |  |
|DPS | Nem | Nem | Nem |  |
|Dynamics 365 Customer engagement | Nem | Nem | Nem |  |
|Dynamics 365 – Pénzügy és műveletek | Nem | Nem | Nem |  |
|Event Grid | Yes | Nem | Nem |  |
|Event Hubs | Igen | Igen | No |  |
|ExpressRoute | Igen | Igen | No |  |
|Tűzfal | Igen | Igen | No |  |
|Front Door | Igen | Igen | No |  |
|Functions | Igen | Igen | No |  |
|HDInsight | No | Yes | No |  |
|HPC Cache | Nem | Nem | Nem |  |
|Information Protection | No | Yes | No |  |
|Intune | No | Yes | No |  |
|IoT-központ | Nem | Nem | Nem |  |
|IoT Hub | Igen | Igen | No |  |
|Key Vault | Igen | Igen | [Igen](insights/key-vaults-insights-overview.md) |  |
|Kubernetes Service (AKS) | No | Nem | [Igen](insights/container-insights-overview.md)  |  |
|Load Balancer | Igen | Igen | No |  |
|Logic Apps | Igen | Igen | No |  |
|Machine Learning szolgáltatás | No | Nem | Nem |  |
|Felügyelt alkalmazások  | No | Nem | Nem |  |
|Maps  | No | Nem | Nem |  |
|Media Services | Igen | Igen | No |  |
|Microsoft Flow | No | Nem | Nem |  |
|Microsoft Felügyelt asztal | No | Nem | Nem |  |
|Microsoft PowerApps | No | Nem | Nem |  |
|Microsoft Social Engagement | No | Nem | Nem |  |
|Microsoft Stream | Igen | Igen | No |  |
|Migrate (Áttelepítés) | No | Nem | Nem |  |
|Multi-Factor Authentication | No | Yes | No |  |
|Network Watcher | Igen | Igen | No |  |
|Notification Hubs | Yes | No | Nem |  |
|Open Datasets | No | Nem | Nem |  |
|Szabályzat | No | Nem | Nem |  |
|Power BI Embedded | Igen | Igen | No |  |
|Privát kapcsolat | No | Nem | Nem |  |
|Projekt-várólista kommunikációs platformja | No | Nem | Nem |  |
|Red Hat OpenShift | No | Nem | Nem |  |
|Redis Cache | Igen | Igen | [Igen](insights/redis-cache-insights-overview.md) | |
|Erőforrás-diagram | No | Nem | Nem |  |
|Resource Manager | No | Nem | Nem |  |
|Kiskereskedelmi keresés – Bing alapján | Nem | Nem | Nem |  |
|Keresés | Igen | Igen | No |  |
|Service Bus | Igen | Igen | No |  |
|Service Fabric | No | Yes | No | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök.  |
|Regisztrációs portál | Nem | Nem | Nem |  |
|Site Recovery | No | Yes | No |  |
|Spring Cloud Service | Nem | Nem | Nem |  |
|SQL Data Warehouse | Igen | Igen | No |  |
|SQL Database | Igen | Igen | No |  |
|SQL Server Stretch Database | Igen | Igen | No |  |
|Verem | Nem | Nem | Nem |  |
|Storage | Yes | No | [Igen](insights/storage-insights-overview.md) |  |
|Tárolási gyorsítótár | Nem | Nem | Nem |  |
|Storage Sync Services | Nem | Nem | Nem |  |
|Stream Analytics | Igen | Igen | No |  |
|Time Series Insights | Igen | Igen | No |  |
|TINA | Nem | Nem | Nem |  |
|Traffic Manager | Igen | Igen | No |  |
|Univerzális nyomtatás | Nem | Nem | Nem |  |
|Virtual Machine Scale Sets | No | Yes | [Igen](insights/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök. |
|Virtuális gépek | Igen | Igen | [Igen](insights/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök. |
|Virtual Network | Igen | Igen | [Igen](insights/network-insights-overview.md) |  |
|Virtual Network – NSG folyamatok naplói | No | Yes | No |  |
|VPN Gateway | Igen | Igen | No |  |
|Windows virtuális asztal | Nem | Nem | Nem |  |


## <a name="product-integrations"></a>Termék-integrációk
Az alábbi táblázatban szereplő szolgáltatások és megoldások egy Log Analytics munkaterületen tárolják az adatokat, így a Azure Monitor által gyűjtött egyéb naplózási adatokkal is elemezhetők.

| Termék/szolgáltatás | Description |
|:---|:---|
| [Azure Automation](/azure/automation/) | Az operációs rendszer frissítéseinek kezelése és a változások nyomon követése Windows és Linux rendszerű számítógépeken. Lásd: [change Tracking](../automation/change-tracking.md) és [Update Management](../automation/automation-update-management.md). |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) | A dokumentumok és e-mailek besorolása és opcionális védelemmel való ellátása. Lásd: [Azure Information Protection központi jelentéskészítése](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Azure Security Center](/azure/security-center/) | A biztonsági események összegyűjtése és elemzése, valamint a fenyegetések elemzésének elvégzése. Adatgyűjtés [Azure Security Center](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Különböző forrásokhoz, például az Office 365-hez és a Amazon Web Services Cloud Trailhez csatlakozik. Lásd: [az adatforrások összekötése](/azure/sentinel/connect-data-sources). |
| [Key Vault-elemzés](insights/azure-key-vault.md) | Azure Key Vault AuditEvent-naplók elemzése. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Hozzon létre egy diagnosztikai beállítást, amely naplókat küld Azure Monitornak. Lásd: [naplózási információk küldése a Storage-ba, az Event hubokba vagy a log analyticsbe az Intune-ban (előzetes verzió)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Network (Hálózat)  | [Network Performance monitor](insights/network-performance-monitor.md) – a hálózati kapcsolat és a teljesítmény figyelése a szolgáltatás-és alkalmazás-végpontok számára.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) – naplók és mérőszámok elemzése az Azure Application Gatewayból.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) – elemzi Network Watcher hálózati biztonsági csoport (NSG) folyamatának naplóit, hogy betekintést nyújtson az Azure-felhőbe irányuló forgalomba. |
| [Office 365](insights/solution-office-365.md) | Az Office 365-környezet monitorozása. Frissített verzió, amely az Azure Sentinel használatával érhető el továbbfejlesztett bevezetéssel. |
| [SQL Analytics](insights/azure-sql.md) | Az Azure SQL Database-adatbázisok és a felügyelt SQL-példányok teljesítményének figyelése nagy méretekben és több előfizetésen keresztül. |
| [Surface Hub](insights/surface-hubs.md) | Surface Hub eszközök állapotának és használatának nyomon követése. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Operations Manager ügynököktől származó adatok gyűjtése a felügyeleti csoport Azure Monitorhoz való csatlakoztatásával. Lásd: [a Operations Manager kapcsolódása Azure monitor](platform/om-agents.md)<br> [Operations Manager Assessment](insights/scom-assessment.md) megoldással értékelheti System Center Operations Manager felügyeleti csoportjának kockázatait és állapotát. |
| [Microsoft Teams-szobák](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | A Microsoft Teams Rooms eszközeinek integrált, teljes körű felügyelete. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Alkalmazások létrehozása, tesztelése és terjesztése, majd az állapotuk és a használat monitorozása. Lásd: [a Mobile App elemzése app Center és Application Insights](learn/mobile-center-quickstart.md). |
| Windows | [Megfelelőség Windows Update](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) – a Windows asztali frissítéseinek felmérése.<br>Az [asztali elemzések](https://docs.microsoft.com/configmgr/desktop-analytics/overview) és a Configuration Manager integrálása lehetővé teszi a Windows-ügyfelek frissítési felkészültségével kapcsolatos tájékozott döntések meghozatalát. |



## <a name="other-solutions"></a>Egyéb megoldások
Más megoldások is elérhetők különböző alkalmazások és szolgáltatások figyelésére, de az aktív fejlesztés leállt, és előfordulhat, hogy az összes régióban nem érhető el. Ezeket az Azure Log Analytics adatfeldolgozási szolgáltatói szerződése szabályozza.

| Megoldás | Description |
|:---|:---|
| [Active Directory állapot-ellenőrzési](insights/ad-assessment.md) | A Active Directory környezetek kockázatának és állapotának felmérése. |
| [Active Directory replikáció állapota](insights/ad-replication-status.md) | Rendszeresen figyeli a Active Directory környezetet bármilyen replikációs hiba esetén. |
| [Activity log Analytics](platform/activity-log.md#activity-log-analytics-monitoring-solution) | Tevékenységek naplójának bejegyzéseinek megtekintése. |
| [DNS Analytics (előzetes verzió)](insights/dns-analytics.md) | Gyűjti, elemzi és korrelálja a DNS-kiszolgálókkal kapcsolatos Windows DNS analitikai és naplózási naplókat és egyéb kapcsolódó adatokat. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Összegyűjtheti, megtekintheti és elemezheti Cloud Foundry rendszerállapot-és teljesítmény-mérőszámait több üzemelő példány között. |
| [Containers](insights/containers.md) | A Docker és a Windows-tároló gazdagépek megtekintése és kezelése. |
| [Igény szerinti értékelések](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | A helyszíni, hibrid és Felhőbeli Microsoft technológiai környezetek rendelkezésre állásának, biztonságának és teljesítményének felmérése és optimalizálása. |
| [SQL Health-vizsgálat](insights/sql-assessment.md) | A SQL Server környezetek kockázatának és állapotának felmérése.  |
| [Átviteli adatok](insights/wire-data.md) | Összevont hálózati és teljesítményadatok a Windows rendszerhez csatlakoztatott és a Linux rendszerű számítógépeken a Log Analytics ügynökkel. |

## <a name="third-party-integration"></a>Harmadik féltől származó integráció

| Megoldás | Description |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Az IT Service Management-összekötő (ITSMC) lehetővé teszi, hogy az Azure-t egy támogatott IT Service Management- (ITSM-) termékhez/szolgáltatáshoz kapcsolja.  |


## <a name="resources-outside-of-azure"></a>Az Azure-on kívüli erőforrások
A Azure Monitor az Azure-on kívüli erőforrásokból is gyűjthet adatokat az alábbi táblázatban felsorolt módszerek használatával.

| Erőforrás | Metódus |
|:---|:---|
| Alkalmazások | Webalkalmazások monitorozása az Azure-on kívül Application Insights használatával. Lásd: [Mi az Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) |
| Virtual machines (Virtuális gépek) | A Log Analytics ügynök használatával adatokat gyűjthet a virtuális gépek vendég operációs rendszeréről más felhőalapú környezetekben vagy helyszíni környezetben. Lásd: [a naplózási adatok összegyűjtése a log Analytics ügynökkel](platform/log-analytics-agent.md). |
| Ügyfél REST API | A különböző API-k képesek adatokat írni Azure Monitor naplókba és metrikába bármilyen REST API ügyfélről. Lásd: [naplófájlok küldésének Azure monitor a http](platform/data-collector-api.md) -adatgyűjtő API-val a naplókhoz, és [Egyéni metrikák küldése egy Azure-erőforrásnak a Azure monitor metrika-tárolóba](platform/metrics-store-custom-rest-api.md) a metrikák REST API használatával. |



## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [Azure monitor adatplatformról, amely az elemzések és megoldások által gyűjtött naplókat és mérőszámokat tárolja](platform/data-platform.md).
- Az [Azure-erőforrások figyelésével kapcsolatos oktatóanyag](learn/tutorial-resource-logs.md)elvégzése.
- Végezzen el egy olyan [oktatóanyagot, amely Azure monitor naplókba tartozó adatelemzési napló lekérdezését írja](learn/tutorial-resource-logs.md)le.
- A [metrikák diagramjának létrehozásával Azure monitor mérőszámokban lévő adatok elemzéséhez](learn/tutorial-metrics-explorer.md)hajtson végre egy oktatóanyagot.

 
