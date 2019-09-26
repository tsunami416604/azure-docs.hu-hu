---
title: Azure diagnosztikai naplók – támogatott szolgáltatások és sémák
description: Ismerje meg az Azure diagnosztikai naplók támogatott szolgáltatásait és esemény-sémáját.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: fdcfcbaf99d48a345d2be4da297be1c9139da15c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308113"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplóihoz

[Azure monitor a diagnosztikai naplókat](../../azure-monitor/platform/resource-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. Az Azure Monitoron keresztül elérhető diagnosztikai naplók közös felső szintű sémával rendelkeznek, és minden szolgáltatás számára rugalmasságot biztosítanak, hogy egyedi tulajdonságokat nyújtsanak a saját eseményeiknek.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) és a `category` séma egyedileg azonosítható kombinációja. Ez a cikk a diagnosztikai naplók legfelső szintű sémáját és az egyes szolgáltatások sémák mutató hivatkozásokat ismerteti.

## <a name="top-level-diagnostic-logs-schema"></a>Legfelső szintű diagnosztikai naplók sémája

| Name (Név) | Kötelező/nem kötelező | Leírás |
|---|---|---|
| time | Szükséges | Az esemény időbélyegzője (UTC). |
| resourceId | Szükséges | Az eseményt kibocsátó erőforrás erőforrás-azonosítója. A bérlői szolgáltatások esetében ez a/tenants/Tenant-ID/Providers/Provider-Name. formátumú. |
| tenantId | A bérlői naplókhoz szükséges | Annak a Active Directory bérlőnek a bérlői azonosítója, amelyhez ez az esemény hozzá van kötve. Ez a tulajdonság csak a bérlői szintű naplók esetében használatos, nem jelenik meg az erőforrás-szintű naplókban. |
| operationName | Szükséges | Az esemény által jelzett művelet neve. Ha az esemény egy RBAC műveletet jelöl, akkor ez a RBAC művelet neve (például Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Általában Resource Manager-művelet formájában modellezve, még akkor is, ha nem ténylegesen dokumentált erőforrás-kezelői műveletek`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`() |
| operationVersion | Választható | A művelethez társított API-verzió, ha a operationName API-val (például:) lett elvégezve. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| category | Szükséges | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. A szokásos naplózási kategóriák "audit" "működési" "végrehajtás" és "kérelem". |
| resultType | Választható | Az esemény állapota. A tipikus értékek a következők: elindítva, folyamatban, sikeres, sikertelen, aktív és megoldott. |
| resultSignature | Választható | Az esemény alállapota. Ha ez a művelet egy REST API hívásnak felel meg, akkor ez a megfelelő REST-hívás HTTP-állapotkód. |
| resultDescription | Választható | A művelet statikus szöveges leírása, például:. "Tárolási fájl beolvasása" |
| durationMs | Választható | A művelet időtartama ezredmásodpercben. |
| callerIpAddress | Választható | A hívó IP-címe, ha a művelet olyan API-hívásnak felel meg, amely nyilvánosan elérhető IP-címmel rendelkező entitásból származik. |
| correlationId | Választható | A kapcsolódó események halmazának csoportosítására szolgáló GUID. Általában, ha két esemény ugyanazzal a operationName, de két különböző állapottal rendelkezik (például "Elindítva" és "sikeres") ugyanaz a korrelációs azonosító. Ez az események közötti egyéb kapcsolatokat is jelenthet. |
| identity | Választható | Egy JSON-blob, amely leírja a műveletet végrehajtó felhasználó vagy alkalmazás identitását. Ez általában magában foglalja az Active Directoryból származó engedélyezési és jogcímek/JWT tokent is. |
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
| Application Gateway-átjárók |[Diagnosztikai naplózás a Application Gatewayhoz](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Diagnosztikai naplózás Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Diagnosztikai naplók Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Diagnosztikai naplók Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Az Azure Cognitive Services diagnosztikai naplózása](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [A CDN-hez készült Azure diagnosztikai naplók](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB naplózás](../../cosmos-db/logging.md) |
| Data Factory | [Az adatüzemek figyelése Azure Monitor használatával](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Diagnosztikai naplók elérése Azure Data Lake Storehoz](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs diagnosztikai naplók](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | A séma nem érhető el. |
| Azure Firewall | A séma nem érhető el. |
| IoT Hub | [IoT Hub műveletek](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Az Azure Key Vault naplózása](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Azure Kubernetes-naplózás](../../aks/view-master-logs.md#log-event-schema) |
| Terheléselosztó |[Naplóelemzés az Azure Load Balancerhez](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Hálózati biztonsági csoportok) |[Naplóelemzés hálózati biztonsági csoportokhoz](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDoS Protection | [Azure DDoS Protection standard kezelése](../../virtual-network/manage-ddos-protection.md) |
| Power BI – dedikált | [Az Azure-beli Power BI Embedded diagnosztikai naplózása](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup adatmodellje](../../backup/backup-azure-reports-data-model.md)|
| Keresés |[A keresési Traffic Analytics engedélyezése és használata](../../search/search-traffic-analytics.md) |
| Service Bus |[Diagnosztikai naplók Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Diagnosztikai naplózás Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Feladatok diagnosztikai naplói](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager naplózási séma](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuális hálózatok | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplózási kategóriák/erőforrás típusok
|Erőforrástípus|Category|Kategória megjelenítendő neve|
|---|---|---|
|Microsoft.AnalysisServices/servers|Adatbázismotor|Adatbázismotor|
|Microsoft.AnalysisServices/servers|Szolgáltatás|Szolgáltatás|
|Microsoft.ApiManagement/service|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|
|Microsoft.Automation/automationAccounts|JobLogs|Feladatok naplói|
|Microsoft.Automation/automationAccounts|JobStreams|Feladatok adatfolyamai|
|Microsoft.Automation/automationAccounts|DscNodeStatus|DSC-csomópont állapota|
|Microsoft.Batch/batchAccounts|ServiceLog|Szolgáltatási naplók|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő adatokat stb.|
|Microsoft. ClassicNetwork/networksecuritygroups|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|
|Microsoft.CognitiveServices/accounts|Naplózás|Auditnaplók|
|Microsoft.CognitiveServices/accounts|RequestResponse|Kérelmek és válaszok naplói|
|Microsoft.ContainerService/managedClusters|Kube – apiserver|Kubernetes API Server|
|Microsoft.ContainerService/managedClusters|Kube – vezérlő – kezelő|Kubernetes Controller Manager|
|Microsoft.ContainerService/managedClusters|fürt – automéretező|Kubernetes Cluster Autoscaler|
|Microsoft.ContainerService/managedClusters|Kube – ütemező|Kubernetes Scheduler|
|Microsoft.ContainerService/managedClusters|őr|Authentication Webhook|
|Microsoft.CustomerInsights/hubs|Listázásával|Listázásával|
|Microsoft.DataFactory/factories|ActivityRuns|Folyamat-futtatási tevékenység naplója|
|Microsoft.DataFactory/factories|PipelineRuns|Folyamat-futtatási napló|
|Microsoft.DataFactory/factories|TriggerRuns|Trigger futtatási naplója|
|Microsoft.DataLakeAnalytics/accounts|Naplózás|Auditnaplók|
|Microsoft.DataLakeAnalytics/accounts|Kérelmek|Kérelmek naplói|
|Microsoft.DataLakeStore/accounts|Naplózás|Auditnaplók|
|Microsoft.DataLakeStore/accounts|Kérelmek|Kérelmek naplói|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|
|Microsoft.Devices/IotHubs|Kapcsolatok|Kapcsolatok|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Eszköztelemetria|
|Microsoft.Devices/IotHubs|C2DCommands|C2D parancsok|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Eszköz-identitási műveletek|
|Microsoft.Devices/IotHubs|FileUploadOperations|Fájlfeltöltés-műveletek|
|Microsoft.Devices/IotHubs|Útvonalak|Útvonalak|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D – két művelet|
|Microsoft.Devices/IotHubs|twinQueries|Dupla lekérdezés|
|Microsoft.Devices/IotHubs|JobsOperations|Feladatok műveletei|
|Microsoft.Devices/IotHubs|DirectMethods|Közvetlen metódusok|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E-diagnosztika (előzetes verzió)|
|Microsoft.Devices/IotHubs|Konfigurációk|Konfigurációk|
|Microsoft.Devices/provisioningServices|DeviceOperations|Eszközműveletek|
|Microsoft.Devices/provisioningServices|ServiceOperations|Szolgáltatási műveletek|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archiválási naplók|
|Microsoft.EventHub/namespaces|OperationalLogs|Operatív naplók|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Naplók automatikus méretezése|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Automatikus skálázás kiértékelései|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Automatikus skálázás skálázási műveletei|
|Microsoft. IoTSpaces/Graph|Híváslánc|Híváslánc|
|Microsoft. IoTSpaces/Graph|Működik|Működik|
|Microsoft. IoTSpaces/Graph|Naplózás|Naplózás|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Bejövő forgalom|Bejövő forgalom|
|Microsoft. IoTSpaces/Graph|Kimenő forgalom|Kimenő forgalom|
|Microsoft.KeyVault/vaults|AuditEvent|Auditnaplók|
|Microsoft.Logic/workflows|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Riasztási események Load Balancer|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS Protection-értesítések|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|A DDoS enyhítésének jelentései|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM-védelmi riasztások|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnosztikai naplók irányítása|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE diagnosztikai naplók|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S diagnosztikai naplók|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Frontdoor hozzáférési naplója|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|
|Microsoft.PowerBIDedicated/capacities|Adatbázismotor|Adatbázismotor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure Backup-jelentésadatok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery-feladatok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery-események|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery által replikált elemek|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Az Azure Site Recovery replikációs statisztikái|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery-beli helyreállítási pontok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery replikációs adatok feltöltési sebessége|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery védett lemez adatváltozása|
|Microsoft.Search/searchServices|OperationLogs|Műveleti naplók|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Operatív naplók|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatikus finomhangolás|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|Microsoft.Sql/servers/databases|Hibák|Hibák|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Adatbázis várakozási statisztikája|
|Microsoft.Sql/servers/databases|Időtúllépések|Időtúllépések|
|Microsoft.Sql/servers/databases|Blokkok|Blokkok|
|Microsoft.Sql/servers/databases|Holtpontok|Holtpontok|
|Microsoft.Sql/servers/databases|Naplózás|Auditnaplók|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS-feldolgozók|
|Microsoft.Sql/servers/databases|ExecRequests|Exec-kérelmek|
|Microsoft.Sql/servers/databases|RequestSteps|Kérelem lépései|
|Microsoft.Sql/servers/databases|SqlRequests|SQL-kérelmek|
|Microsoft.Sql/servers/databases|Megvárja|Megvárja|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Erőforrás-használati statisztika|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|Microsoft.Sql/managedInstances/databases|Hibák|Hibák|
|Microsoft.StreamAnalytics/streamingjobs|Végrehajtás|Végrehajtás|
|Microsoft.StreamAnalytics/streamingjobs|Szerzői műveletek|Szerzői műveletek|
|microsoft.web/sites|FunctionExecutionLogs|Függvények végrehajtási naplói|
|Microsoft. Web/Sites/Slots|FunctionExecutionLogs|Függvények végrehajtási naplói|

## <a name="next-steps"></a>További lépések

* [További információ a diagnosztikai naplókról](../../azure-monitor/platform/resource-logs-overview.md)
* [Az erőforrás-diagnosztikai naplók Stream **az Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Módosítsa az erőforrás diagnosztikai beállításait az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [A Log Analytics használatával az Azure storage-naplók elemzése](../../azure-monitor/platform/collect-azure-metrics-logs.md)
