---
title: Az Azure Resource naplózza a támogatott szolgáltatásokat és sémákat
description: Ismerje meg az Azure-erőforrás-naplók támogatott szolgáltatásait és esemény-sémáját.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 9b7b51417814e74cc7e3559029c9af8c35cbf6f2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016355"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Támogatott szolgáltatások, sémák és kategóriák az Azure-erőforrások naplóihoz

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek.

[Azure monitor az erőforrás-naplókat](../../azure-monitor/platform/platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) és a `category` séma egyedileg azonosítható kombinációja. Ez a cikk az erőforrás-naplók legfelső szintű sémáját, valamint az egyes szolgáltatások sémák mutató hivatkozásokat ismerteti.

## <a name="top-level-resource-logs-schema"></a>Legfelső szintű erőforrás-naplók sémája

| Name | Kötelező vagy nem kötelező | Leírás |
|---|---|---|
| time | Kötelező | Az esemény időbélyegzője (UTC). |
| resourceId | Kötelező | Az eseményt kibocsátó erőforrás erőforrás-azonosítója. A bérlői szolgáltatások esetében ez a/tenants/Tenant-ID/Providers/Provider-Name. formátumú. |
| tenantId | A bérlői naplókhoz szükséges | Annak a Active Directory bérlőnek a bérlői azonosítója, amelyhez ez az esemény hozzá van kötve. Ez a tulajdonság csak a bérlői szintű naplók esetében használatos, nem jelenik meg az erőforrás-szintű naplókban. |
| operationName | Kötelező | Az esemény által jelzett művelet neve. Ha az esemény egy RBAC műveletet jelöl, akkor ez a RBAC művelet neve (például Microsoft. Storage/storageAccounts/blobServices/Blobok/olvasás). Általában Resource Manager-művelet formájában modellezve, még akkor is, ha nem ténylegesen dokumentált erőforrás-kezelői műveletek ( `Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>` ) |
| operationVersion | Választható | A művelethez társított API-verzió, ha a operationName API-val (például:) lett elvégezve. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| category | Kötelező | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. A szokásos naplózási kategóriák "audit" "működési" "végrehajtás" és "kérelem". |
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

## <a name="service-specific-schemas-for-resource-logs"></a>Az erőforrás-naplók szolgáltatás-specifikus sémái
Az erőforrás-diagnosztikai naplók sémája az erőforrás és a napló kategóriától függően változik. Ez a lista az összes olyan szolgáltatást megjeleníti, amely elérhető erőforrás-naplókat készít, valamint a szolgáltatásra mutató hivatkozásokat, valamint a kategória-specifikus sémát.

| Szolgáltatás | Séma & docs |
| --- | --- |
| Azure Active Directory | [Áttekintés](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [naplózási napló sémája](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) és [bejelentkezési sémája](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Erőforrás-naplók API Management](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Application Gateway-átjárók |[Application Gateway naplózása](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Azure Automation log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch naplózás](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Diagnosztikai naplók Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Naplók Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Data Explorer | [Azure Adatkezelő-naplók](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Azure-Cognitive Services naplózása](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Azure Container Registry naplózása](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [A CDN-hez készült Azure-naplók](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB naplózás](../../cosmos-db/logging.md) |
| Data Factory | [Az adatüzemek figyelése Azure Monitor használatával](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Naplók elérése Azure Data Lake Analyticshoz](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Naplók elérése Azure Data Lake Storehoz](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Azure Event Hubs-naplók](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | A séma nem érhető el. |
| Azure Firewall | A séma nem érhető el. |
| IoT Hub | [IoT Hub műveletek](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure Key Vault naplózás](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes-naplózás](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Naplóelemzés az Azure Load Balancerhez](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Hálózati biztonsági csoportok) |[Naplóelemzés hálózati biztonsági csoportokhoz](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Védelem DDOS-támadások ellen | [Azure DDoS Protection standard kezelése](../../virtual-network/manage-ddos-protection.md) |
| Power BI – dedikált | [Power BI Embedded naplózása az Azure-ban](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure Backup adatmodellje](../../backup/backup-azure-reports-data-model.md)|
| Keresés |[A keresési Traffic Analytics engedélyezése és használata](../../search/search-traffic-analytics.md) |
| Service Bus |[Naplók Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL Database naplózás](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Stream Analytics |[Feladatnaplók](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager naplózási séma](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuális hálózatok | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplózási kategóriák/erőforrás típusok

Bizonyos kategóriák csak bizonyos típusú erőforrások esetén támogatottak. Ez az összes olyan lista, amely valamilyen formában elérhető.  Például a Microsoft. SQL/kiszolgálók/adatbázisok kategóriái nem érhetők el minden típusú adatbázishoz. További információ: [SQL Database diagnosztikai naplózással kapcsolatos információk](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

|Erőforrás típusa|Kategória|Kategória megjelenítendő neve|
|---|---|---|
|Microsoft. HRE/domainServices|SystemSecurity|SystemSecurity|
|Microsoft. HRE/domainServices|AccountManagement|AccountManagement|
|Microsoft. HRE/domainServices|LogonLogoff|LogonLogoff|
|Microsoft. HRE/domainServices|ObjectAccess|ObjectAccess|
|Microsoft. HRE/domainServices|PolicyChange|PolicyChange|
|Microsoft. HRE/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. HRE/domainServices|DetailTracking|DetailTracking|
|Microsoft. HRE/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. HRE/domainServices|AccountLogon|AccountLogon|
|Microsoft. aadiam/bérlők|Bejelentkezési|Bejelentkezési|
|Microsoft. AnalysisServices/kiszolgálók|Motor|Motor|
|Microsoft. AnalysisServices/kiszolgálók|Szolgáltatás|Szolgáltatás|
|Microsoft.ApiManagement/service|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|
|Microsoft. AppPlatform/Spring|ApplicationConsole|Application Console|
|Microsoft. Automation/automationAccounts|JobLogs|Feladatok naplói|
|Microsoft. Automation/automationAccounts|JobStreams|Feladatok adatfolyamai|
|Microsoft. Automation/automationAccounts|DscNodeStatus|DSC-csomópont állapota|
|Microsoft. batch/batchAccounts|ServiceLog|Szolgáltatási naplók|
|Microsoft. BatchAI/munkaterületek|BaiClusterEvent|BaiClusterEvent|
|Microsoft. BatchAI/munkaterületek|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. BatchAI/munkaterületek|BaiJobEvent|BaiJobEvent|
|Microsoft. Blockchain/blockchainMembers|BlockchainApplication|Blockchain-alkalmazás|
|Microsoft. Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft. CDN/profilok/végpontok|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő adatokat stb.|
|Microsoft. ClassicNetwork/networksecuritygroups|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|
|Microsoft. CognitiveServices/fiókok|Naplózás|Naplók|
|Microsoft. CognitiveServices/fiókok|RequestResponse|Kérelmek és válaszok naplói|
|Microsoft. ContainerRegistry/nyilvántartók|ContainerRegistryRepositoryEvents|RepositoryEvent-naplók (előzetes verzió)|
|Microsoft. ContainerRegistry/nyilvántartók|ContainerRegistryLoginEvents|Bejelentkezési események (előzetes verzió)|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – apiserver|Kubernetes API-kiszolgáló|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – vezérlő – kezelő|Kubernetes Controller Manager|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – ütemező|Kubernetes Scheduler|
|Microsoft. Tárolószolgáltatás/managedClusters|Kube – naplózás|Kubernetes naplózása|
|Microsoft. Tárolószolgáltatás/managedClusters|fürt – automéretező|Kubernetes-fürt autoskálázása|
|Microsoft. Databricks/munkaterületek|dbfs|Databricks fájlrendszer|
|Microsoft. Databricks/munkaterületek|fürtök|Databricks-fürtök|
|Microsoft. Databricks/munkaterületek|fiókok|Databricks-fiókok|
|Microsoft. Databricks/munkaterületek|feladatok|Databricks-feladatok|
|Microsoft. Databricks/munkaterületek|notebook|Databricks-jegyzetfüzet|
|Microsoft. Databricks/munkaterületek|SSH|Databricks SSH|
|Microsoft. Databricks/munkaterületek|munkaterület|Databricks-munkaterület|
|Microsoft. Databricks/munkaterületek|titkok|Databricks titkai|
|Microsoft. Databricks/munkaterületek|sqlPermissions|Databricks SQLPermissions|
|Microsoft. Databricks/munkaterületek|instancePools|Példányok készletei|
|Microsoft. DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft. DataFactory/gyárak|ActivityRuns|Folyamat-futtatási tevékenység naplója|
|Microsoft. DataFactory/gyárak|PipelineRuns|Folyamat-futtatási napló|
|Microsoft. DataFactory/gyárak|TriggerRuns|Trigger futtatási naplója|
|Microsoft. DataLakeAnalytics/fiókok|Naplózás|Naplók|
|Microsoft. DataLakeAnalytics/fiókok|Kérelmek|Kérelmek naplói|
|Microsoft. Data Lake Store/fiókok|Naplózás|Naplók|
|Microsoft. Data Lake Store/fiókok|Kérelmek|Kérelmek naplói|
|Microsoft. DataShare/fiókok|Megosztások|Megosztások|
|Microsoft. DataShare/fiókok|ShareSubscriptions|Előfizetések megosztása|
|Microsoft. DataShare/fiókok|SentShareSnapshots|Eljuttatott megosztási Pillanatképek|
|Microsoft. DataShare/fiókok|ReceivedShareSnapshots|Fogadott megosztási Pillanatképek|
|Microsoft. DBforMySQL/kiszolgálók|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|
|Microsoft. DBforMySQL/kiszolgálók|MySqlAuditLogs|MySQL-naplók|
|Microsoft. DBforPostgreSQL/kiszolgálók|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|
|Microsoft. DBforPostgreSQL/kiszolgálók|QueryStoreRuntimeStatistics|PostgreSQL Query Store futásidejű statisztikái|
|Microsoft. DBforPostgreSQL/kiszolgálók|QueryStoreWaitStatistics|PostgreSQL lekérdezési tár várakozási statisztikái|
|Microsoft. DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|PostgreSQL Query Store futásidejű statisztikái|
|Microsoft. DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|PostgreSQL lekérdezési tár várakozási statisztikái|
|Microsoft. DesktopVirtualization/munkaterületek|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/munkaterületek|Hiba|Hiba|
|Microsoft. DesktopVirtualization/munkaterületek|Kezelés|Kezelés|
|Microsoft. DesktopVirtualization/munkaterületek|Adatcsatorna|Adatcsatorna|
|Microsoft. DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/applicationGroups|Hiba|Hiba|
|Microsoft. DesktopVirtualization/applicationGroups|Kezelés|Kezelés|
|Microsoft. DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft. DesktopVirtualization/hostPools|Hiba|Hiba|
|Microsoft. DesktopVirtualization/hostPools|Kezelés|Kezelés|
|Microsoft. DesktopVirtualization/hostPools|Kapcsolat|Kapcsolat|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft. Devices/IotHubs|Kapcsolatok|Kapcsolatok|
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
|Microsoft. Devices/IotHubs|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|
|Microsoft. Devices/IotHubs|Konfigurációk|Konfigurációk|
|Microsoft. Devices/IotHubs|DeviceStreams|Eszköz streamek (előzetes verzió)|
|Microsoft. Devices/provisioningServices|DeviceOperations|Eszköz műveletei|
|Microsoft. Devices/provisioningServices|ServiceOperations|Szolgáltatási műveletek|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft. DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/szolgáltatások|AuditEvent|AuditEvent-napló|
|Microsoft. EnterpriseKnowledgeGraph/szolgáltatások|DataIssue|DataIssue-napló|
|Microsoft. EnterpriseKnowledgeGraph/szolgáltatások|Kérelmek|Konfigurációs napló|
|Microsoft. EventHub/névterek|ArchiveLogs|Archiválási naplók|
|Microsoft. EventHub/névterek|OperationalLogs|Operatív naplók|
|Microsoft. EventHub/névterek|AutoScaleLogs|Naplók automatikus méretezése|
|Microsoft. EventHub/névterek|KafkaCoordinatorLogs|Kafka-koordinátor naplói|
|Microsoft. EventHub/névterek|KafkaUserErrorLogs|Kafka felhasználói hibák naplói|
|Microsoft. EventHub/névterek|EventHubVNetConnectionEvent|VNet/IP-szűrési kapcsolatok naplófájljai|
|Microsoft. EventHub/névterek|CustomerManagedKeyUserLogs|Ügyfél által felügyelt kulcsok naplói|
|Microsoft. HealthcareApis/szolgáltatások|AuditLogs|Auditnaplók|
|Microsoft. bepillantások/AutoscaleSettings|AutoscaleEvaluations|Az autoscale-értékelések|
|Microsoft. bepillantások/AutoscaleSettings|AutoscaleScaleActions|Méretezési műveletek az autoskálázáshoz|
|Microsoft. IoTSpaces/Graph|Nyomkövetés|Nyomkövetés|
|Microsoft. IoTSpaces/Graph|Operatív|Operatív|
|Microsoft. IoTSpaces/Graph|Naplózás|Naplózás|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Bejövő forgalom|Bejövő forgalom|
|Microsoft. IoTSpaces/Graph|Kimenő forgalom|Kimenő forgalom|
|Microsoft. kulcstartó/tárolók|AuditEvent|Naplók|
|Microsoft. Kusto/fürtök|SucceededIngestion|Sikeres betöltési műveletek|
|Microsoft. Kusto/fürtök|FailedIngestion|Sikertelen betöltési műveletek|
|Microsoft. Logic/munkafolyamatok|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|
|Microsoft. Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|
|Microsoft. MachineLearningServices/munkaterületek|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft. MachineLearningServices/munkaterületek|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft. MachineLearningServices/munkaterületek|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/Mediaservices|KeyDeliveryRequests|Kulcsok kézbesítésére vonatkozó kérelmek|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|
|Microsoft. Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|
|Microsoft. Network/nyilvános IP|DDoSProtectionNotifications|DDoS Protection-értesítések|
|Microsoft. Network/nyilvános IP|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|
|Microsoft. Network/nyilvános IP|DDoSMitigationReports|A DDoS enyhítésének jelentései|
|Microsoft. Network/virtualNetworks|VMProtectionAlerts|VM-védelmi riasztások|
|Microsoft. Network/applicationGateways|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|
|Microsoft. Network/applicationGateways|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|
|Microsoft. Network/applicationGateways|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|
|Microsoft. Network/virtualNetworkGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|Microsoft. Network/virtualNetworkGateways|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|
|Microsoft. Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnosztikai naplók irányítása|
|Microsoft. Network/virtualNetworkGateways|IKEDiagnosticLog|IKE diagnosztikai naplók|
|Microsoft. Network/virtualNetworkGateways|P2SDiagnosticLog|P2S diagnosztikai naplók|
|Microsoft. Network/trafficManagerProfiles|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|
|Microsoft. Network/expressRouteCircuits|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Diagnosztikai naplók irányítása|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|IKE diagnosztikai naplók|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Frontdoor hozzáférési naplója|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|IKE diagnosztikai naplók|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|P2S diagnosztikai naplók|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Megerősített naplók|
|Microsoft. Network/loadBalancers|LoadBalancerAlertEvent|Riasztási események Load Balancer|
|Microsoft. Network/loadBalancers|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|
|Microsoft. PowerBIDedicated/kapacitások|Motor|Motor|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AzureBackupReport|Jelentéskészítési adatgyűjtés Azure Backup|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|CoreAzureBackup|Alapvető Azure Backup-adathalmazok|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AddonAzureBackupJobs|Addon Azure Backup feladatok adatai|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AddonAzureBackupAlerts|Addon Azure Backup riasztási adatkezelési|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AddonAzureBackupPolicy|Addon Azure Backup házirend-adatbázis|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AddonAzureBackupStorage|Addon Azure Backup Storage-beli adattárak|
|Microsoft. Recoveryservices szolgáltatónál/tárolók|AddonAzureBackupProtectedInstance|Addon Azure Backup védett példányok adatvédelme|
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
|Microsoft. SQL/kiszolgálók/adatbázisok|Naplózás|Naplók|
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
|Microsoft. Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft. StreamAnalytics/streamingjobs|Futtatási|Futtatási|
|Microsoft. StreamAnalytics/streamingjobs|Tartalomkészítés|Tartalomkészítés|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service Environment platform naplói|
|Microsoft. Web/Sites|FunctionAppLogs|Function Application-naplók|
|Microsoft. Web/Sites|AppServiceHTTPLogs|HTTP-naplók|
|Microsoft. Web/Sites|AppServiceConsoleLogs|App Service konzol naplófájljai|
|Microsoft. Web/Sites|AppServiceAppLogs|App Service alkalmazás naplófájljai|
|Microsoft. Web/Sites|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|
|Microsoft. Web/Sites|AppServiceAuditLogs|Hozzáférés-naplózási naplók|
|Microsoft. Web/Sites/Slots|FunctionAppLogs|Function Application-naplók|
|Microsoft. Web/Sites/Slots|AppServiceHTTPLogs|HTTP-naplók|
|Microsoft. Web/Sites/Slots|AppServiceConsoleLogs|Konzol naplófájljai|
|Microsoft. Web/Sites/Slots|AppServiceAppLogs|Alkalmazásnaplók|
|Microsoft. Web/Sites/Slots|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|
|Microsoft. Web/Sites/Slots|AppServiceAuditLogs|Hozzáférés-naplózási naplók|

## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](../../azure-monitor/platform/platform-logs-overview.md)
* [Stream erőforrás-erőforrás naplófájljai **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Az erőforrás-napló diagnosztikai beállításainak módosítása a Azure Monitor használatával REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
