---
title: Az Azure Monitor által figyelt
description: Az Azure Monitor által figyelt összes szolgáltatás és egyéb erőforrás hivatkozása.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: e0e98b87cf3612bf01f90f806ea64ef06d08c60a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255328"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Mit figyel az Azure Monitor?
Ez a cikk ismerteti a különböző alkalmazások és szolgáltatások, amelyek az Azure Monitor által figyelt. 

## <a name="insights-and-core-solutions"></a>Betekintés ek és alapvető megoldások
Az alapvető elemzési adatok és megoldások az Azure Monitor részét képezik, és követik az Azure támogatási és szolgáltatásiszint-szerződéseit. Ezek minden Olyan Azure-régióban támogatottak, ahol az Azure Monitor elérhető.

### <a name="insights"></a>Insights

Az insights személyre szabott figyelési élményt nyújt az egyes alkalmazásokés szolgáltatások számára. A naplókat és a mutatókat egyaránt gyűjtik és elemzik.

| Elemzések | Leírás |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Bővíthető alkalmazásteljesítmény-kezelés (APM) szolgáltatás az élő webalkalmazás figyeléséhez bármely platformon. |
| [Az Azure-figyelő tárolókhoz](insights/container-insights-overview.md) | Figyeli az Azure Container-példányok vagy az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett felügyelt Kubernetes-fürtöküzembe helyezett tárolószámítási feladatok teljesítményét. |
| [Azure Monitor a Cosmos DB-hez (előzetes verzió)](insights/cosmosdb-insights-overview.md) | Az Azure Cosmos DB összes erőforrásának általános teljesítményét, hibáit, kapacitását és működési állapotát egységes interaktív környezetben jeleníti meg. |
| [Azure-figyelő hálózatokhoz (előzetes verzió)](insights/network-insights-overview.md) | Átfogó képet nyújt az összes hálózati erőforrás állapotáról és mutatóiról. A speciális keresési funkció segítségével azonosíthatja az erőforrás-függőségeket, lehetővé téve az olyan forgatókönyveket, mint például a webhelyet üzemeltető erőforrások azonosítása, egyszerűen a webhely nevének keresésével. |
[Azure-figyelő erőforráscsoportokhoz (előzetes verzió)](insights/resource-group-insights.md) |  Osztályzhatja és diagnosztizálhatja az egyes erőforrások által tapasztalt problémákat, miközben az erőforráscsoport egészének állapotát és teljesítményét illetően kontextust kínál. |
| [Azure-figyelő tárhelyhez (előzetes verzió)](insights/storage-insights-overview.md) | Az Azure Storage-fiókok átfogó figyelését biztosítja az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes nézetével. |
| [Virtuális gépek Azure-figyelője](insights/container-insights-overview.md) | Figyeli az Azure virtuális gépek (VM) és a virtuális gép méretezési csoportok nagy méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket. |

### <a name="core-solutions"></a>Alapvető megoldások

A megoldások naplólekérdezéseken és egy adott alkalmazáshoz vagy szolgáltatáshoz testre szabott nézeteken alapulnak. Csak naplókat gyűjtenek és elemeznek, és idővel elavultak az elemzések javára.

| Megoldás | Leírás |
|:---|:---|
| [Ügynök állapota](insights/solution-agenthealth.md) | Elemezze a Log Analytics-ügynökök állapotát és konfigurációját. |
| [Riasztás kezelése](platform/alert-management-solution.md) | Elemezze a System Center Operations Manager, Nagios vagy Zabbix által gyűjtött riasztásokat. |
| [Szolgáltatástérkép](insights/service-map.md) | Automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és leképezi a szolgáltatások közötti kommunikációt. |



## <a name="azure-services"></a>Azure-szolgáltatások
Az alábbi táblázat felsorolja az Azure-szolgáltatásokat és az Azure Monitorba gyűjtött adatokat. 

- Metrikák – A szolgáltatás automatikusan összegyűjti a metrikákat az Azure Monitor metrikák. 
- Naplók – A szolgáltatás támogatja a diagnosztikai beállításokat, amelyek platformnaplók és metrikák azure monitor naplók gyűjthető.
- Insight - Van egy betekintést a szolgáltatás, amely személyre szabott figyelési élményt biztosít a szolgáltatás.

| Szolgáltatás | Mérőszámok | Naplók | Elemzések | Megjegyzések |
|:---|:---|:---|:---|:---|
|Active Directory | Nem | Igen | [Igen](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Nem | Nem | Nem |  |
|Active Directory tartományi szolgáltatások | Nem | Igen | Nem |  |
|Tevékenységnapló | Nem | Igen | Nem | |
|Fejlett fenyegetésvédelem | Nem | Nem | Nem |  |
|Advisor | Nem | Nem | Nem |  |
|AI-szerkesztő | Nem | Nem | Nem |  |
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
|Blockchain Service | Nem | Igen | Nem |  |
|Tervek | Nem | Nem | Nem |  |
|Bot Service | Nem | Nem | Nem |  |
|Cloud Services | Igen | Igen | Nem | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök.  |
|Cloud Shell | Nem | Nem | Nem |  |
|Cognitive Services | Igen | Igen | Nem |  |
|Tárolópéldányok | Igen | Nem | Nem |  |
|Container Registry | Igen | Igen | Nem |  |
|Tartalomkézbesítési hálózat (CDN) | Nem | Igen | Nem |  |
|Cosmos DB | Igen | Igen | [Igen](insights/cosmosdb-insights-overview.md) |  |
|Cost Management | Nem | Nem | Nem |  |
|Data Box | Nem | Nem | Nem |  |
|Adatkatalógus Gen2 | Nem | Nem | Nem |  |
|Data Explorer | Igen | Igen | Nem |  |
|Data Factory | Igen | Igen | Nem |  |
|Data Factory v2 | Nem | Igen | Nem |  |
|Data Share | Nem | Nem | Nem |  |
|Database for MariaDB | Igen | Igen | Nem |  |
|Database for MySQL | Igen | Igen | Nem |  |
|Database for PostgreSQL | Igen | Igen | Nem |  |
|Adatbázis-áttelepítési szolgáltatás | Nem | Nem | Nem |  |
|Databricks | Nem | Igen | Nem |  |
|DDoS Protection | Igen | Igen | Nem |  |
|DevOps | Nem | Nem | Nem |  |
|DNS | Igen | Nem | Nem |  |
|Tartománynevek | Nem | Nem | Nem |  |
|Dps | Nem | Nem | Nem |  |
|Dynamics 365 ügyfélelköteleződés | Nem | Nem | Nem |  |
|Dynamics 365 pénzügyi és műveleti műveletek | Nem | Nem | Nem |  |
|Event Grid | Igen | Nem | Nem |  |
|Event Hubs | Igen | Igen | Nem |  |
|ExpressRoute | Igen | Igen | Nem |  |
|Tűzfal | Igen | Igen | Nem |  |
|Front Door | Igen | Igen | Nem |  |
|Functions | Igen | Igen | Nem |  |
|HDInsight | Nem | Igen | Nem |  |
|HPC Cache | Nem | Nem | Nem |  |
|Adatvédelem | Nem | Igen | Nem |  |
|Intune | Nem | Igen | Nem |  |
|IoT-központ | Nem | Nem | Nem |  |
|IoT Hub | Igen | Igen | Nem |  |
|Key Vault | Igen | Igen | Nem |  |
|Kubernetes Service (AKS) | Nem | Nem | [Igen](insights/container-insights-overview.md)  |  |
|Load Balancer | Igen | Igen | Nem |  |
|Logic Apps | Igen | Igen | Nem |  |
|Machine Learning-szolgáltatás | Nem | Nem | Nem |  |
|Felügyelt alkalmazások  | Nem | Nem | Nem |  |
|Maps  | Nem | Nem | Nem |  |
|Media Services | Igen | Igen | Nem |  |
|Microsoft Flow | Nem | Nem | Nem |  |
|Microsoft Felügyelt asztal | Nem | Nem | Nem |  |
|Microsoft PowerApps | Nem | Nem | Nem |  |
|Microsoft Social Engagement | Nem | Nem | Nem |  |
|Microsoft Stream | Igen | Igen | Nem |  |
|Migrate (Áttelepítés) | Nem | Nem | Nem |  |
|Multi-Factor Authentication | Nem | Igen | Nem |  |
|Network Watcher | Igen | Igen | Nem |  |
|Notification Hubs | Igen | Nem | Nem |  |
|Nyílt adathalmazok | Nem | Nem | Nem |  |
|Szabályzat | Nem | Nem | Nem |  |
|Power BI | Igen | Igen | Nem |  |
|Power BI Embedded | Nem | Nem | Nem |  |
|Privát kapcsolat | Nem | Nem | Nem |  |
|Projekt spool kommunikációs platform | Nem | Nem | Nem |  |
|Red Hat OpenShift | Nem | Nem | Nem |  |
|Redis Cache | Igen | Igen | Nem |  |
|Erőforrás-diagram | Nem | Nem | Nem |  |
|Resource Manager | Nem | Nem | Nem |  |
|Kiskereskedelmi keresés – a Bing | Nem | Nem | Nem |  |
|Keresés | Igen | Igen | Nem |  |
|Service Bus | Igen | Igen | Nem |  |
|Service Fabric | Nem | Igen | Nem | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök.  |
|Regisztrációs portál | Nem | Nem | Nem |  |
|Site Recovery | Nem | Igen | Nem |  |
|Tavaszi felhőszolgáltatás | Nem | Nem | Nem |  |
|SQL Data Warehouse | Igen | Igen | Nem |  |
|SQL Database | Igen | Igen | Nem |  |
|SQL Server Stretch Database | Igen | Igen | Nem |  |
|Verem | Nem | Nem | Nem |  |
|Storage | Igen | Nem | [Igen](insights/storage-insights-overview.md) |  |
|Tároló gyorsítótára | Nem | Nem | Nem |  |
|Tárolási szinkronizálási szolgáltatások | Nem | Nem | Nem |  |
|Stream Analytics | Igen | Igen | Nem |  |
|Time Series Insights | Igen | Igen | Nem |  |
|Tina | Nem | Nem | Nem |  |
|Traffic Manager | Igen | Igen | Nem |  |
|Univerzális nyomtatás | Nem | Nem | Nem |  |
|Virtual Machine Scale Sets | Nem | Igen | [Igen](insights/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök. |
|Virtuális gépek | Igen | Igen | [Igen](insights/vminsights-overview.md) | A vendég operációs rendszer és a munkafolyamatok figyeléséhez szükséges ügynök. |
|Virtual Network | Igen | Igen | [Igen](insights/network-insights-overview.md) |  |
|Virtuális hálózat – NSG-folyamatnaplók | Nem | Igen | Nem |  |
|VPN Gateway | Igen | Igen | Nem |  |
|Windows Virtual Desktop | Nem | Nem | Nem |  |


## <a name="product-integrations"></a>Termékintegrációk
Az alábbi táblázatban található szolgáltatások és megoldások az adatokat egy Log Analytics-munkaterületen tárolják, hogy elemezhetők legyenek az Azure Monitor által gyűjtött egyéb naplóadatokkal.

| Termék/szolgáltatás | Leírás |
|:---|:---|
| [Azure Automation](/azure/automation/) | Kezelje az operációs rendszer frissítéseit, és kövesse nyomon a változásokat Windows és Linux rendszerű számítógépeken. Lásd: [Változáskövetés](../automation/change-tracking.md) és [frissítéskezelés](../automation/automation-update-management.md). |
| [Azure-információvédelem](https://docs.microsoft.com/azure/information-protection/) | A dokumentumok és e-mailek besorolása és opcionális védelme. Lásd: [Központi jelentés készítés az Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports) |
| [Azure Security Center](/azure/security-center/) | Gyűjtse össze és elemezze a biztonsági eseményeket, és végezzen fenyegetéselemzést. [Lásd: Adatgyűjtés az Azure Security Centerben](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | Különböző forrásokhoz csatlakozik, például az Office 365-höz és az Amazon Web Services Cloud Trailhez. Lásd: [Adatforrások csatlakoztatása](/azure/sentinel/connect-data-sources). |
| [Key Vault-elemzés](insights/azure-key-vault.md) | Elemezze az Azure Key Vault auditesemény-naplóit. |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | Hozzon létre egy diagnosztikai beállítást a naplók küldéséhez az Azure Monitornak. Lásd: [Naplóadatok küldése a tárolóba, az eseményközpontokba vagy a naplóelemzésbe az Intune-ban (előzetes verzió)](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor).  |
| Network (Hálózat)  | [Hálózati teljesítményfigyelő](insights/network-performance-monitor.md) – A hálózati kapcsolat és a szolgáltatás- és alkalmazásvégpontok teljesítményének figyelése.<br>[Azure Application Gateway](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) – Az Azure Application Gateway naplóinak és metrikáinak elemzése.<br>[Traffic Analytics](/azure/network-watcher/traffic-analytics) – Elemzi a Network Watcher hálózati biztonsági csoport (NSG) folyamatnaplók at, hogy betekintést nyújt a forgalom áramlását az Azure-felhőben. |
| [Office 365](insights/solution-office-365.md) | Az Office 365-környezet figyelése. Frissített verzió az Azure Sentinelen keresztül elérhető továbbfejlesztett bevezetéssel. |
| [SQL Analytics](insights/azure-sql.md) | Az Azure SQL-adatbázisok, a rugalmas készletek és a felügyelt példányok teljesítményét nagy méretekben és több előfizetésben figyelheti. |
| [Surface Hub](insights/surface-hubs.md) | Nyomon követheti a Surface Hub-eszközök állapotát és használatát. |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | Adatokat gyűjthet az Operations Manager-ügynököktől, ha csatlakoztatja a felügyeleti csoportot az Azure Monitorhoz. Lásd: [Operations Manager csatlakoztatása az Azure Monitorhoz](platform/om-agents.md)<br> Mérje fel a System Center Operations Manager felügyeleti csoport kockázatát és állapotát az [Operations Manager Assessment](insights/scom-assessment.md) megoldással. |
| [Microsoft Teams-szobák](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | A Microsoft Teams Rooms eszközök integrált, végponttól végpontig integrálódása. |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | Alkalmazásokat hozhat létre, tesztelhet és terjeszthet, majd figyelheti azok állapotát és használatát. Lásd: [Mobilalkalmazás elemzésének megkezdése az App Center rel és az Application Insights alkalmazással.](learn/mobile-center-quickstart.md) |
| Windows | [Windows Update megfelelőség –](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started) A Windows asztali frissítésének felmérése.<br>[Desktop Analytics](https://docs.microsoft.com/configmgr/desktop-analytics/overview) – Integrálható a Configuration Managerrel, hogy betekintést és intelligenciát nyújtson a Windows-ügyfelek frissítésre való felkészültségével kapcsolatos megalapozottabb döntések meghozatalához. |



## <a name="other-solutions"></a>Egyéb megoldások
Más megoldások is rendelkezésre állnak a különböző alkalmazások és szolgáltatások figyelésére, de az aktív fejlesztés leállt, és előfordulhat, hogy nem érhetők el minden régióban. Ezek az Azure Log Analytics-adatok betöltési szolgáltatásiszint-szerződés hatálya alá tartoznak.

| Megoldás | Leírás |
|:---|:---|
| [Az Active Directory állapotának ellenőrzése](insights/ad-assessment.md) | Mérje fel az Active Directory-környezetek kockázatát és állapotát. |
| [Active Directory replikációs állapota](insights/ad-replication-status.md) | Rendszeresen figyeli az Active Directory-környezetben a replikációs hibákat. |
| [Tevékenységnapló-elemzés](platform/activity-log-view.md#azure-portal) | Tevékenységnapló-bejegyzések megtekintése. |
| [DNS-elemzés (előzetes verzió)](insights/dns-analytics.md) | Gyűjti, elemzi és korrelálja a Windows DNS-elemzési és naplózási naplókat és egyéb kapcsolódó adatokat a DNS-kiszolgálókról. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Gyűjtse össze, tekintse meg és elemezze a Cloud Foundry rendszer állapotát és teljesítménymutatóit több telepítéssorán. |
| [Containers](insights/containers.md) | Tekintse meg és kezelje a Docker- és Windows-tárolóállomásokat. |
| [Igény szerinti értékelések](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | Mérje fel és optimalizálja a helyszíni, hibrid és felhőalapú Microsoft-technológiai környezetek rendelkezésre állását, biztonságát és teljesítményét. |
| [SQL állapot-ellenőrzés](insights/sql-assessment.md) | Mérje fel az SQL Server-környezetek kockázatát és állapotát.  |
| [Átviteli adatok](insights/wire-data.md) | A Log Analytics-ügynökkel windowsos és Linuxhoz csatlakoztatott számítógépekről gyűjtött összevont hálózati és teljesítményadatok. |


## <a name="third-party-integration"></a>Harmadik fél integrációja

| Megoldás | Leírás |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | Az IT Service Management Connector (ITSMC) lehetővé teszi az Azure és a támogatott IT Service Management (ITSM) termék/szolgáltatás csatlakoztatását.  |


## <a name="resources-outside-of-azure"></a>Az Azure-on kívüli erőforrások
Az Azure Monitor az alábbi táblázatban felsorolt módszerekkel adatokat gyűjthet az Azure-on kívüli erőforrásokból.

| Erőforrás | Módszer |
|:---|:---|
| Alkalmazások | Az Application Insights használatával az Azure-on kívüli webalkalmazások figyelése. Lásd: [Mi az Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). |
| Virtual machines (Virtuális gépek) | A Log Analytics-ügynök segítségével adatokat gyűjthet a más felhőalapú környezetekben vagy a helyszínen lévő virtuális gépek vendég operációs rendszeréből. Lásd: [Naplóadatok gyűjtése a Log Analytics-ügynökkel.](platform/log-analytics-agent.md) |
| REST API-ügyfél | Külön API-k állnak rendelkezésre az Azure Monitor naplók és metrikák bármely REST API-ügyfél adatok írásához. Lásd: [Naplóadatok küldése az Azure Monitornak a HTTP Data Collector API-val](platform/data-collector-api.md) a naplókhoz és [egyéni metrikák küldése egy Azure-erőforráshoz az Azure Monitor metrikatárolóba egy REST API](platform/metrics-store-custom-rest-api.md) metrikákhoz használatával. |



## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitor adatplatformról, amely az elemzések és megoldások által gyűjtött naplókat és mutatókat tárolja.](platform/data-platform.md)
- Az [Azure-erőforrások figyelésével kapcsolatos oktatóanyag](learn/tutorial-resource-logs.md)befejezése.
- Töltse ki [a naplólekérdezés írásával kapcsolatos oktatóanyagot az Azure Monitor-naplók ban lévő adatok elemzéséhez.](learn/tutorial-resource-logs.md)
- Töltse ki [a metrikadiagram létrehozásáról szóló oktatóanyagot az Azure Monitor metrikákban lévő adatok elemzéséhez.](learn/tutorial-metrics-explorer.md)

 
