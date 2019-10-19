---
title: Azure diagnosztikai naplók – támogatott szolgáltatások és sémák
description: Ismerje meg az Azure diagnosztikai naplók támogatott szolgáltatásait és esemény-sémáját.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/11/2018
ms.openlocfilehash: f2d71972fa8acc930800a70193f688246ee7415c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555564"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplóihoz

[Azure monitor a diagnosztikai naplókat](../../azure-monitor/platform/resource-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. Az Azure Monitoron keresztül elérhető diagnosztikai naplók közös felső szintű sémával rendelkeznek, és minden szolgáltatás számára rugalmasságot biztosítanak, hogy egyedi tulajdonságokat nyújtsanak a saját eseményeiknek.

Az erőforrástípus kombinációja (a `resourceId` tulajdonságban érhető el) és a `category` egyedileg azonosít egy sémát. Ez a cikk a diagnosztikai naplók legfelső szintű sémáját és az egyes szolgáltatások sémák mutató hivatkozásokat ismerteti.

## <a name="top-level-diagnostic-logs-schema"></a>Legfelső szintű diagnosztikai naplók sémája

| Név | Kötelező/nem kötelező | Leírás |
|---|---|---|
| time | Szükséges | Az esemény időbélyegzője (UTC). |
| resourceId | Szükséges | Az eseményt kibocsátó erőforrás erőforrás-azonosítója. A bérlői szolgáltatások esetében ez a/tenants/Tenant-ID/Providers/Provider-Name. formátumú. |
| TenantId | A bérlői naplókhoz szükséges | Annak a Active Directory bérlőnek a bérlői azonosítója, amelyhez ez az esemény hozzá van kötve. Ez a tulajdonság csak a bérlői szintű naplók esetében használatos, nem jelenik meg az erőforrás-szintű naplókban. |
| operationName | Szükséges | Az esemény által jelzett művelet neve. Ha az esemény egy RBAC műveletet jelöl, akkor ez a RBAC művelet neve (például Microsoft. Storage/storageAccounts/blobServices/Blobok/olvasás). Általában Resource Manager-művelet formájában modellezve, még akkor is, ha nem ténylegesen dokumentált erőforrás-kezelői műveletek (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Választható | A művelethez társított API-verzió, ha a operationName API-val (például:) lett elvégezve. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| category | Szükséges | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. A szokásos naplózási kategóriák "audit" "működési" "végrehajtás" és "kérelem". |
| resultType | Választható | Az esemény állapota. A tipikus értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív és megoldott. |
| resultSignature | Választható | Az esemény alállapota. Ha ez a művelet egy REST API hívásnak felel meg, akkor ez a megfelelő REST-hívás HTTP-állapotkód. |
| resultDescription | Választható | A művelet statikus szöveges leírása, például:. "Tárolási fájl beolvasása" |
| durationMs | Választható | A művelet időtartama ezredmásodpercben. |
| callerIpAddress | Választható | A hívó IP-címe, ha a művelet olyan API-hívásnak felel meg, amely nyilvánosan elérhető IP-címmel rendelkező entitásból származik. |
| correlationId | Választható | A kapcsolódó események halmazának csoportosítására szolgáló GUID. Általában, ha két esemény ugyanazzal a operationName, de két különböző állapottal rendelkezik (például "Elindítva" és "sikeres") ugyanaz a korrelációs azonosító. Ez az események közötti egyéb kapcsolatokat is jelenthet. |
| identitáskezelés | Választható | Egy JSON-blob, amely leírja a műveletet végrehajtó felhasználó vagy alkalmazás identitását. Ez általában magában foglalja az Active Directoryból származó engedélyezési és jogcímek/JWT tokent is. |
| Szint | Választható | Az esemény súlyossági szintje. Az egyik tájékoztatási, figyelmeztetési, hiba vagy kritikus értéknek kell lennie. |
| location | Választható | Az eseményt kibocsátó erőforrás régiója, például:. "USA keleti régiója" vagy "Dél-Franciaország" |
| properties | Választható | Az adott kategóriába tartozó eseményekhez kapcsolódó további tulajdonságok. Minden egyéni/egyedi tulajdonságot a séma "B része" részévé kell tenni. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Az erőforrás-diagnosztikai naplók szolgáltatás-specifikus sémái
Az erőforrás-diagnosztikai naplók sémája az erőforrás és a napló kategóriától függően változik. Ez a lista az összes olyan szolgáltatást megjeleníti, amely elérhetővé teszi a diagnosztikai naplókat, és a szolgáltatásra mutató hivatkozásokat, valamint a kategória-specifikus sémát.

| Szolgáltatás | Séma & docs |
| --- | --- |
| Azure Active Directory | [Áttekintés](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [naplózási napló sémája](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) és [bejelentkezési sémája](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Diagnosztikai naplók API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway átjárók |[Diagnosztikai naplózás a Application Gatewayhoz](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Diagnosztikai naplózás Azure Batch](../../batch/batch-diagnostics.md) |
| A MySQL-hez készült Azure Database | [Diagnosztikai naplók Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| A PostgreSQL-hez készült Azure Database | [Diagnosztikai naplók Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Adatkezelő | [Azure Adatkezelő diagnosztikai naplók](../../data-explorer/using-diagnostic-logs.md) |
| Cognitive Services | [Az Azure Cognitive Services diagnosztikai naplózása](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [A CDN-hez készült Azure diagnosztikai naplók](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB naplózás](../../cosmos-db/logging.md) |
| Data Factory | [Az adatüzemek figyelése Azure Monitor használatával](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Diagnosztikai naplók elérése Azure Data Lake Storehoz](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Event Hubs-eseményközpontok |[Azure Event Hubs diagnosztikai naplók](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | A séma nem érhető el. |
| Azure Firewall | A séma nem érhető el. |
| IoT Hub | [IoT Hub műveletek](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Az Azure Key Vault naplózása](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes-naplózás](../../aks/view-master-logs.md#log-event-schema) |
| Terheléselosztó |[Naplóelemzés az Azure Load Balancerhez](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Hálózati biztonsági csoportok |[Naplóelemzés hálózati biztonsági csoportokhoz](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection standard kezelése](../../virtual-network/manage-ddos-protection.md) |
| Power BI – dedikált | [Az Azure-beli Power BI Embedded diagnosztikai naplózása](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services (Helyreállítási szolgáltatások) | [Azure Backup adatmodellje](../../backup/backup-azure-reports-data-model.md)|
| Search |[A keresési Traffic Analytics engedélyezése és használata](../../search/search-traffic-analytics.md) |
| Szolgáltatásbusz |[Diagnosztikai naplók Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL-adatbázis | [Diagnosztikai naplózás Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Feladatok diagnosztikai naplói](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Forgalomkezelő | [Traffic Manager naplózási séma](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtual Network | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplózási kategóriák/erőforrás típusok
|Erőforrás típusa|Kategória|Kategória megjelenítendő neve|
|---|---|---|
|Microsoft. AnalysisServices/kiszolgálók|Motor|Motor|
|Microsoft. AnalysisServices/kiszolgálók|Szolgáltatás|Szolgáltatás|
|Microsoft.ApiManagement/service|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|
|Microsoft. Automation/automationAccounts|JobLogs|Feladatok naplói|
|Microsoft. Automation/automationAccounts|JobStreams|Feladatok adatfolyamai|
|Microsoft. Automation/automationAccounts|DscNodeStatus|DSC-csomópont állapota|
|Microsoft. batch/batchAccounts|ServiceLog|Szolgáltatási naplók|
|Microsoft. CDN/profilok/végpontok|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő adatokat stb.|
|Microsoft. ClassicNetwork/networksecuritygroups|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|
|Microsoft. CognitiveServices/fiókok|Naplózás|Naplófájlok|
|Microsoft. CognitiveServices/fiókok|RequestResponse|Kérelmek és válaszok naplói|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – apiserver|Kubernetes API-kiszolgáló|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – vezérlő – kezelő|Kubernetes Controller Manager|
|Microsoft. Tárolószolgáltatás/managedClusters|fürt – automéretező|Kubernetes-fürt autoskálázása|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – ütemező|Kubernetes Scheduler|
|Microsoft. Tárolószolgáltatás/managedClusters|őr|Hitelesítési webhook|
|Microsoft. CustomerInsights/hubok|Listázásával|Listázásával|
|Microsoft. DataFactory/gyárak|ActivityRuns|Folyamat-futtatási tevékenység naplója|
|Microsoft. DataFactory/gyárak|PipelineRuns|Folyamat-futtatási napló|
|Microsoft. DataFactory/gyárak|TriggerRuns|Trigger futtatási naplója|
|Microsoft. DataLakeAnalytics/fiókok|Naplózás|Naplófájlok|
|Microsoft. DataLakeAnalytics/fiókok|Kérelmek|Kérelmek naplói|
|Microsoft. Data Lake Store/fiókok|Naplózás|Naplófájlok|
|Microsoft. Data Lake Store/fiókok|Kérelmek|Kérelmek naplói|
|Microsoft. DBforMySQL/kiszolgálók|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|
|Microsoft. DBforPostgreSQL/kiszolgálók|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|
|Microsoft. Devices/IotHubs|Connections (Kapcsolatok)|Connections (Kapcsolatok)|
|Microsoft. Devices/IotHubs|DeviceTelemetry|Eszköztelemetria|
|Microsoft. Devices/IotHubs|C2DCommands|C2D parancsok|
|Microsoft. Devices/IotHubs|DeviceIdentityOperations|Eszköz-identitási műveletek|
|Microsoft. Devices/IotHubs|FileUploadOperations|Fájlfeltöltés-műveletek|
|Microsoft. Devices/IotHubs|Útvonalak|Útvonalak|
|Microsoft. Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft. Devices/IotHubs|C2DTwinOperations|C2D – két művelet|
|Microsoft. Devices/IotHubs|TwinQueries|Dupla lekérdezés|
|Microsoft. Devices/IotHubs|JobsOperations|Feladatok műveletei|
|Microsoft. Devices/IotHubs|DirectMethods|Közvetlen metódusok|
|Microsoft. Devices/IotHubs|E2EDiagnostics|E2E-diagnosztika (előzetes verzió)|
|Microsoft. Devices/IotHubs|Konfigurációk|Konfigurációk|
|Microsoft. Devices/provisioningServices|DeviceOperations|Eszköz műveletei|
|Microsoft. Devices/provisioningServices|ServiceOperations|Szolgáltatási műveletek|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. EventHub/névterek|ArchiveLogs|Archiválási naplók|
|Microsoft. EventHub/névterek|OperationalLogs|Operatív naplók|
|Microsoft. EventHub/névterek|AutoScaleLogs|Naplók automatikus méretezése|
|Microsoft. bepillantások/AutoscaleSettings|AutoscaleEvaluations|Az autoscale-értékelések|
|Microsoft. bepillantások/AutoscaleSettings|AutoscaleScaleActions|Méretezési műveletek az autoskálázáshoz|
|Microsoft. IoTSpaces/Graph|Nyomkövetés|Nyomkövetés|
|Microsoft. IoTSpaces/Graph|Operatív|Operatív|
|Microsoft. IoTSpaces/Graph|Naplózás|Naplózás|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Belépő|Belépő|
|Microsoft. IoTSpaces/Graph|Kimenő forgalom|Kimenő forgalom|
|Microsoft. kulcstartó/tárolók|AuditEvent|Naplófájlok|
|Microsoft. Logic/munkafolyamatok|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Riasztási események Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS Protection-értesítések|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|A DDoS enyhítésének jelentései|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM-védelmi riasztások|
|Microsoft. Network/applicationGateways|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|
|Microsoft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|
|Microsoft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|
|Microsoft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|Microsoft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|
|Microsoft. Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnosztikai naplók irányítása|
|Microsoft. Network/virtualNetworkGateways|IKEDiagnosticLog|IKE diagnosztikai naplók|
|Microsoft. Network/virtualNetworkGateways|P2SDiagnosticLog|P2S diagnosztikai naplók|
|Microsoft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|
|Microsoft. Network/expressRouteCircuits|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Frontdoor hozzáférési naplója|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|
|Microsoft. PowerBIDedicated/kapacitások|Motor|Motor|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureBackupReport|Jelentéskészítési adatgyűjtés Azure Backup|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryJobs|Azure Site Recovery feladatok|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryEvents|Események Azure Site Recovery|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryReplicatedItems|Replikált elemek Azure Site Recovery|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryReplicationStats|Azure Site Recovery replikációs statisztika|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery helyreállítási pontok|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery replikációs adatok feltöltési sebessége|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery védett lemez adatváltozása|
|Microsoft. Search/searchServices|OperationLogs|Műveleti naplók|
|Microsoft. ServiceBus/névterek|OperationalLogs|Operatív naplók|
|Microsoft. SQL/kiszolgálók/adatbázisok|SQLInsights|SQL-ismeretek|
|Microsoft. SQL/kiszolgálók/adatbázisok|AutomaticTuning|Automatikus hangolás|
|Microsoft. SQL/kiszolgálók/adatbázisok|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|Microsoft. SQL/kiszolgálók/adatbázisok|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|Microsoft. SQL/kiszolgálók/adatbázisok|Hibák|Hibák|
|Microsoft. SQL/kiszolgálók/adatbázisok|DatabaseWaitStatistics|Adatbázis várakozási statisztikája|
|Microsoft. SQL/kiszolgálók/adatbázisok|Időtúllépések|Időtúllépések|
|Microsoft. SQL/kiszolgálók/adatbázisok|Blokkok|Blokkok|
|Microsoft. SQL/kiszolgálók/adatbázisok|Holtpontok|Holtpontok|
|Microsoft. SQL/kiszolgálók/adatbázisok|Naplózás|Naplófájlok|
|Microsoft. SQL/kiszolgálók/adatbázisok|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Microsoft. SQL/kiszolgálók/adatbázisok|DmsWorkers|DMS-feldolgozók|
|Microsoft. SQL/kiszolgálók/adatbázisok|ExecRequests|Exec-kérelmek|
|Microsoft. SQL/kiszolgálók/adatbázisok|RequestSteps|Kérelem lépései|
|Microsoft. SQL/kiszolgálók/adatbázisok|SqlRequests|SQL-kérelmek|
|Microsoft. SQL/kiszolgálók/adatbázisok|Megvárja|Megvárja|
|Microsoft. SQL/managedInstances|ResourceUsageStats|Erőforrás-használati statisztika|
|Microsoft. SQL/managedInstances|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Microsoft. SQL/managedInstances/adatbázisok|SQLInsights|SQL-ismeretek|
|Microsoft. SQL/managedInstances/adatbázisok|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|Microsoft. SQL/managedInstances/adatbázisok|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|Microsoft. SQL/managedInstances/adatbázisok|Hibák|Hibák|
|Microsoft. StreamAnalytics/streamingjobs|Végrehajtás|Végrehajtás|
|Microsoft. StreamAnalytics/streamingjobs|Tartalomkészítés|Tartalomkészítés|
|Microsoft. Web/Sites|FunctionExecutionLogs|Függvények végrehajtási naplói|
|Microsoft. Web/Sites/Slots|FunctionExecutionLogs|Függvények végrehajtási naplói|

## <a name="next-steps"></a>Következő lépések

* [További információ a diagnosztikai naplókról](../../azure-monitor/platform/resource-logs-overview.md)
* [Stream erőforrás-diagnosztikai naplók **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Erőforrás-diagnosztikai beállítások módosítása a Azure Monitor használatával REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
