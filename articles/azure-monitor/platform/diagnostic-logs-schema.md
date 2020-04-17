---
title: Az Azure Resource Naplók támogatott szolgáltatások és sémák
description: Ismerje meg az Azure-erőforrásnaplók támogatott szolgáltatásait és eseménysémáját.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 352310a6e489a96c38e85b16e9504d8eb9be38b1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457246"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Támogatott szolgáltatások, sémák és kategóriák az Azure-erőforrásnaplókhoz

> [!NOTE]
> Az erőforrásnaplókat korábban diagnosztikai naplóknak nevezték.

[Az Azure Monitor erőforrás-naplók](../../azure-monitor/platform/platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják az ilyen szolgáltatások vagy erőforrások működését. Az Azure Monitoron keresztül elérhető összes erőforrásnapló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasan bocsáthat ki egyedi tulajdonságokat a saját eseményeihez.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) `category` és a séma egyedi azonosítása kombinációja. Ez a cikk az erőforrásnaplók legfelső szintű sémáját és az egyes szolgáltatások sémájára mutató hivatkozásokat ismerteti.

## <a name="top-level-resource-logs-schema"></a>Legfelső szintű erőforrásnaplók sémája

| Name (Név) | Kötelező vagy nem kötelező | Leírás |
|---|---|---|
| time | Kötelező | Az esemény időbélyege (UTC). |
| resourceId | Kötelező | Az eseményt kibocsátó erőforrás erőforrásazonosítója. Bérlői szolgáltatások esetén ez a /tenants/tenant-id/providers/provider-name formában van. |
| tenantId | Bérlői naplókhoz szükséges | Annak az Active Directory-bérlőnek a bérlői azonosítója, amelyhez ez az esemény kötődik. Ez a tulajdonság csak bérlői szintű naplókhoz használatos, nem jelenik meg az erőforrásszintű naplókban. |
| operationName | Kötelező | Az esemény által képviselt művelet neve. Ha az esemény RBAC műveletet jelöl, akkor ez az RBAC művelet neve (pl. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Általában erőforrás-kezelő művelet formájában modellezve, még akkor is, ha azok`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`nem ténylegesen dokumentált Erőforrás-kezelő műveletek ( ) |
| operationVersion | Optional | A művelethez társított api-verzió, ha a operationName api használatával történt (pl. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Ha nincs olyan API, amely megfelel ennek a műveletnek, a verzió a művelet verzióját jelöli, ha a művelethez társított tulajdonságok a jövőben megváltoznak. |
| category | Kötelező | Az esemény naplókategóriája. Kategória Az a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Egy esemény tulajdonságblobjában megjelenő tulajdonságok megegyeznek egy adott naplókategórián és erőforrástípuson belül. A tipikus naplókategóriák a "Naplózás" "Operatív" "Végrehajtás" és "Kérés". |
| resultType | Optional | Az esemény állapota. A tipikus értékek a következők: Elindítva, Folyamatban, Sikeres, Sikertelen, Aktív és Megoldva. |
| resultSignature | Optional | Az esemény alállapota. Ha ez a művelet egy REST API-hívásnak felel meg, ez a megfelelő REST-hívás HTTP-állapotkódja. |
| resultDescription | Optional | A statikus szöveges leírása ennek a műveletnek, pl. "Tárolófájl betárolása." |
| durationMs | Optional | A művelet időtartama ezredmásodpercben. |
| callerIpAddress | Optional | A hívó IP-címe, ha a művelet megfelel egy API-hívás, amely egy nyilvánosan elérhető IP-címmel rendelkező entitástól származik. |
| correlationId | Optional | A kapcsolódó események csoportjának csoportosítására használt GUID azonosító. Általában, ha két esemény ugyanazt a műveletetName de két különböző állapotok (pl. "Elindítva" és "Sikeres"), azonos korrelációs azonosítóval rendelkeznek. Ez az események közötti egyéb kapcsolatokat is képviselhet. |
| identity | Optional | JSON blob, amely leírja a műveletet végző felhasználó vagy alkalmazás identitását. Ez általában tartalmazza az active directoryból származó engedélyezést és jogcímeket / JWT-jogkivonatot. |
| Szint | Optional | Az esemény súlyossági szintje. Tájékoztató, figyelmeztetési, hiba vagy kritikus típusúnak kell lennie. |
| location | Optional | Az eseményt kibocsátó erőforrás régiója, pl. "USA keleti része" vagy "Dél-Franciaország" |
| properties | Optional | Az adott eseménykategóriához kapcsolódó kiterjesztett tulajdonságok. Minden egyéni/egyedi tulajdonságot a séma "B. részébe" kell helyezni. |

## <a name="service-specific-schemas-for-resource-logs"></a>Szolgáltatásspecifikus sémák erőforrásnaplókhoz
Az erőforrásdiagnosztikai naplók sémája az erőforrás- és naplókategóriától függ. Ez a lista az összes olyan szolgáltatást tartalmazza, amely elérhetővé teszi az erőforrásnaplókat, és a szolgáltatásra és a kategóriaspecifikus sémára mutató hivatkozásokat, ahol elérhető.

| Szolgáltatás | Séma & dokumentumok |
| --- | --- |
| Azure Active Directory | [Áttekintés](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Naplóséma](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) és [bejelentkezési séma naplózása](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [API-felügyeleti erőforrásnaplók](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Application Gateway-átjárók |[Alkalmazásátjáró naplózása](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log analytics az Azure Automationhez](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch naplózás](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL diagnosztikai naplók](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure-adatbázis a PostgreSQL-naplókhoz](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Azure Data Explorer | [Az Azure Data Explorer naplói](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Naplózás az Azure Cognitive Services szolgáltatáshoz](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Az Azure Container Registry naplózása](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Azure-naplók a CDN-hez](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB naplózása](../../cosmos-db/logging.md) |
| Data Factory | [Adatgyárak figyelése az Azure Monitor használatával](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Az Azure Data Lake Analytics naplóinak elérése](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Az Azure Data Lake Store naplóinak elérése](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Az Azure Event Hubs naplói](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | A séma nem érhető el. |
| Azure Firewall | A séma nem érhető el. |
| IoT Hub | [IoT Hub-műveletek](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure key vault naplózása](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes naplózás](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Naplóelemzés az Azure Load Balancerhez](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Hálózati biztonsági csoportok) |[Naplóelemzés hálózati biztonsági csoportokhoz](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Védelem DDOS-támadások ellen | [Az Azure DDoS protection standard kezelése](../../virtual-network/manage-ddos-protection.md) |
| Power BI – dedikált | [Az Azure-ba ágyazott Power BI naplózása](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Adatmodell az Azure Backup hoz](../../backup/backup-azure-reports-data-model.md)|
| Keresés |[A Keresési forgalom elemzése engedélyezése és használata](../../search/search-traffic-analytics.md) |
| Service Bus |[Az Azure Service Bus naplói](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL-adatbázis naplózása](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Feladatnaplók](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Traffic Manager naplóséma](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuális hálózatok | A séma nem érhető el. |
| Virtuális hálózati átjárók | A séma nem érhető el. |

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplókategóriák erőforrástípusonként

Egyes kategóriák csak bizonyos típusú erőforrások esetében támogatottak. Ez a lista az összes, hogy rendelkezésre állnak valamilyen formában.  A Microsoft.Sql/servers/databases kategóriák például nem érhetők el minden típusú adatbázishoz. További információt [az SQL Database diagnosztikai naplózásáról talál.](../../sql-database/sql-database-metrics-diag-logging.md) 

|Erőforrás típusa|Kategória|Kategória megjelenítendő neve|
|---|---|---|
|Microsoft.AAD/domainServices|SystemSecurity rendszerbiztonság|SystemSecurity rendszerbiztonság|
|Microsoft.AAD/domainServices|Fiókkezelés|Fiókkezelés|
|Microsoft.AAD/domainServices|Bejelentkezés|Bejelentkezés|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|Házirendmódosítása|Házirendmódosítása|
|Microsoft.AAD/domainServices|PrivilegeUse (Jogosultsághasználata)|PrivilegeUse (Jogosultsághasználata)|
|Microsoft.AAD/domainServices|Részletek követése|Részletek követése|
|Microsoft.AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft.AAD/domainServices|Fiókbejelentkezés|Fiókbejelentkezés|
|microsoft.aadiam/bérlők|Bejelentkezés|Bejelentkezés|
|Microsoft.AnalysisServices/kiszolgálók|Motor|Motor|
|Microsoft.AnalysisServices/kiszolgálók|Szolgáltatás|Szolgáltatás|
|Microsoft.ApiManagement/service|GatewayLogs|Az ApiManagement átjáróhoz kapcsolódó naplók|
|Microsoft.AppPlatform/Tavasz|Alkalmazáskonzol|Alkalmazáskonzol|
|Microsoft.Automation/automationAccounts|JobLogs|Feladatnaplók|
|Microsoft.Automation/automationAccounts|JobStreams|Feladat-adatfolyamok|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc csomópont állapota|
|Microsoft.Batch/batchAccounts|Szerviznapló|Szolgáltatásnaplók|
|Microsoft.BatchAI/munkaterületek|BaiClusterEvent|BaiClusterEvent|
|Microsoft.BatchAI/munkaterületek|BaiClusterNodeEsemény|BaiClusterNodeEsemény|
|Microsoft.BatchAI/munkaterületek|BaiJobEsemény|BaiJobEsemény|
|Microsoft.Blockchain/blockchainMembers|BlockchainApplication alkalmazás|Blockchain alkalmazás|
|Microsoft.Blockchain/blockchainMembers|Proxy|Proxy|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Lefoglalja a végpont metrikáit, például sávszélességet, kimenő forgalom, stb.|
|Microsoft.ClassicNetwork/hálózati biztonsági csoportok|Hálózati biztonsági csoport szabályának folyamatának eseménye|Hálózati biztonsági csoport szabályának folyamatának eseménye|
|Microsoft.CognitiveServices/fiókok|Naplózás|Naplók|
|Microsoft.CognitiveServices/fiókok|Kérésválasz|Kérési és válasznaplók|
|Microsoft.ContainerRegistry/regisztika|ContainerRegistryRepositoryEvents|RepositoryEvent naplók (előzetes verzió)|
|Microsoft.ContainerRegistry/regisztika|ContainerRegistryInEvents|Bejelentkezési események (előzetes verzió)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API-kiszolgáló|
|Microsoft.ContainerService/managedClusters|kube-vezérlő-menedzser|Kubernetes-kezelő|
|Microsoft.ContainerService/managedClusters|kube-ütemező|Kubernetes ütemező|
|Microsoft.ContainerService/managedClusters|kube-audit|Kubernetes Audit|
|Microsoft.ContainerService/managedClusters|fürt-automatikus méretező|Kubernetes-fürt automatikus skálázója|
|Microsoft.Databricks/munkaterületek|dbfs|Databricks fájlrendszer|
|Microsoft.Databricks/munkaterületek|Klaszterek|Databricks klaszterek|
|Microsoft.Databricks/munkaterületek|fiókok|Databricks-fiókok|
|Microsoft.Databricks/munkaterületek|Munkahelyek|Databricks állás|
|Microsoft.Databricks/munkaterületek|Notebook|Databricks-jegyzetfüzet|
|Microsoft.Databricks/munkaterületek|Ssh|Databricks SSH|
|Microsoft.Databricks/munkaterületek|munkaterület|Databricks munkaterület|
|Microsoft.Databricks/munkaterületek|Titkok|Databricks titkok|
|Microsoft.Databricks/munkaterületek|sqlPermissions|Databricks SQLPermissions|
|Microsoft.Databricks/munkaterületek|példánykészlet|Példánykészletek|
|Microsoft.DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus (ScanStatus)|
|Microsoft.DataFactory/gyárak|Tevékenységfuttatások|A folyamattevékenység naplót futtat|
|Microsoft.DataFactory/gyárak|Csővezetékfuttatások|A folyamat naplót futtat|
|Microsoft.DataFactory/gyárak|Eseményindítók futtatása|Az eseményindító naplót futtat|
|Microsoft.DataLakeAnalytics/fiókok|Naplózás|Naplók|
|Microsoft.DataLakeAnalytics/fiókok|Kérelmek|Naplók kérése|
|Microsoft.DataLakeStore/fiókok|Naplózás|Naplók|
|Microsoft.DataLakeStore/fiókok|Kérelmek|Naplók kérése|
|Microsoft.DataShare/fiókok|Megosztások|Megosztások|
|Microsoft.DataShare/fiókok|Share-előfizetések|Előfizetések megosztása|
|Microsoft.DataShare/fiókok|SentShareSnapshots|Elküldött megosztási pillanatképek|
|Microsoft.DataShare/fiókok|ReceivedShareSnapshots|Fogadott megosztási pillanatképek|
|Microsoft.DBforMySQL/kiszolgálók|MySqlSlowLogs|MySQL server naplók|
|Microsoft.DBforMySQL/kiszolgálók|MySqlAuditLogs|MySQL naplói|
|Microsoft.DBforPostgreSQL/szerverek|PostgreSQLLogs|PostgreSQL szerver naplók|
|Microsoft.DBforPostgreSQL/szerverek|QueryStoreRuntimeStatisztika|PostgreSQL Query Store futásidejű statisztika|
|Microsoft.DBforPostgreSQL/szerverek|QueryStoreWaitStatisztikák|PostgreSQL Query Store várakozási statisztika|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|PostgreSQL szerver naplók|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatisztika|PostgreSQL Query Store futásidejű statisztika|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatisztikák|PostgreSQL Query Store várakozási statisztika|
|Microsoft.DesktopVirtualization/munkaterületek|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/munkaterületek|Hiba|Hiba|
|Microsoft.DesktopVirtualization/munkaterületek|Kezelés|Kezelés|
|Microsoft.DesktopVirtualization/munkaterületek|Adatcsatorna|Adatcsatorna|
|Microsoft.DesktopVirtualization/applicationGroups|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/applicationGroups|Hiba|Hiba|
|Microsoft.DesktopVirtualization/applicationGroups|Kezelés|Kezelés|
|Microsoft.DesktopVirtualization/hostPools|Checkpoint|Checkpoint|
|Microsoft.DesktopVirtualization/hostPools|Hiba|Hiba|
|Microsoft.DesktopVirtualization/hostPools|Kezelés|Kezelés|
|Microsoft.DesktopVirtualization/hostPools|Kapcsolat|Kapcsolat|
|Microsoft.DesktopVirtualization/hostPools|Állomásregisztráció|Állomásregisztráció|
|Microsoft.Devices/IotHubs|Kapcsolatok|Kapcsolatok|
|Microsoft.Devices/IotHubs|DeviceTelemetria|Eszköztelemetria|
|Microsoft.Devices/IotHubs|C2DParancsok|C2D parancsok|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations (DeviceIdentityOperations)|Eszközidentitás-műveletek|
|Microsoft.Devices/IotHubs|FileUploadOperations|Fájlfeltöltési műveletek|
|Microsoft.Devices/IotHubs|Útvonalak|Útvonalak|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D ikerműveletek|
|Microsoft.Devices/IotHubs|TwinQueries|Ikerlekérdezések|
|Microsoft.Devices/IotHubs|JobsOperations (Feladatok üzemeltetése)|Feladatműveletek|
|Microsoft.Devices/IotHubs|DirectMethods (Közvetlen módszerek)|Közvetlen módszerek|
|Microsoft.Devices/IotHubs|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|
|Microsoft.Devices/IotHubs|Konfigurációk|Konfigurációk|
|Microsoft.Devices/IotHubs|DeviceStreams|Eszközadatfolyamok (előzetes verzió)|
|Microsoft.Devices/provisioningServices|DeviceOperations (Eszközüzemeltetés)|Eszköz műveletek|
|Microsoft.Devices/provisioningServices|Szolgáltatási műveletek|Szolgáltatási műveletek|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoKérések|MongoKérések|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatisztika|QueryRuntimeStatisztika|
|Microsoft.DocumentDB/databaseAccounts|PartitionKeyStatistics (Partíciókulcsstatisztikája)|PartitionKeyStatistics (Partíciókulcsstatisztikája)|
|Microsoft.DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft.EnterpriseKnowledgeGraph/szolgáltatások|AuditEvent|Naplózási eseménynaplója|
|Microsoft.EnterpriseKnowledgeGraph/szolgáltatások|Adatprobléma|DataIssue napló|
|Microsoft.EnterpriseKnowledgeGraph/szolgáltatások|Kérelmek|Konfigurációs napló|
|Microsoft.EventHub/névterek|Archiválási naplók|Archiválási naplók|
|Microsoft.EventHub/névterek|Operatív naplók|Operatív naplók|
|Microsoft.EventHub/névterek|Automatikus skálánaplók|Automatikus méretezési naplók|
|Microsoft.EventHub/névterek|KafkaCoordinatorLogs|Kafka koordinátor naplók|
|Microsoft.EventHub/névterek|KafkaUserErrorLogs|Kafka felhasználói hibanaplók|
|Microsoft.EventHub/névterek|EventHubVNetConnectionevent|Virtuális hálózat/IP-szűrési kapcsolatnaplók|
|Microsoft.EventHub/névterek|CustomerManagedKeyUserLogs|Ügyfél által kezelt kulcsnaplók|
|Microsoft.HealthcareApis/services|AuditLogok|Naplók|
|Microsoft.Insights/AutoscaleSettings|Automatikus skálázáskiértékelések|Automatikus skálázási kiértékelések|
|Microsoft.Insights/AutoscaleSettings|Automatikus skálázási műveletek|Automatikus méretezési méretezési műveletek|
|Microsoft.IoTSpaces/Graph|Nyomkövetés|Nyomkövetés|
|Microsoft.IoTSpaces/Graph|Működési|Működési|
|Microsoft.IoTSpaces/Graph|Naplózás|Naplózás|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Bejövő forgalom|Bejövő forgalom|
|Microsoft.IoTSpaces/Graph|Kimenő forgalom|Kimenő forgalom|
|Microsoft.KeyVault/vaults|AuditEvent|Naplók|
|Microsoft.Kusto/Fürtök|SucceededIngestion (Sikeres szerkesztés)|Sikeres betöltési műveletek|
|Microsoft.Kusto/Fürtök|Sikertelen|Sikertelen betöltési műveletek|
|Microsoft.Logic/munkafolyamatok|Munkafolyamat-futtatási idő|Munkafolyamat futásidejű diagnosztikai eseményei|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integrációs fiók nyomon követhető eseményei|
|Microsoft.MachineLearningServices/munkaterületek|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/munkaterületek|AmlComputeClusterNodeEsemény|AmlComputeClusterNodeEsemény|
|Microsoft.MachineLearningServices/munkaterületek|AmlComputeJobEsemény|AmlComputeJobEsemény|
|Microsoft.Media/mediaservices|KeyDeliveryRequests|Kulcskézbesítési kérelmek|
|Microsoft.Network/Networksecuritygroups|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|Microsoft.Network/Networksecuritygroups|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályszámlálója|
|Microsoft.Network/Networksecuritygroups|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoport szabályának folyamatának eseménye|
|Microsoft.Network/publicIPAddresses|DdosProtectionÉrtesítések|DDoS védelmi értesítések|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|A DDoS-kockázatcsökkentési döntések folyamatnaplói|
|Microsoft.Network/publicIPAddresses|Ddosmitigationreports|Jelentések a DDoS mérséklése|
|Microsoft.Network/virtualNetworks|VMProtectionRiasztások|Virtuális gép védelmi riasztásai|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Alkalmazásátjáró-hozzáférési napló|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Alkalmazásátjáró teljesítménynaplója|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Alkalmazásátjáró tűzfalának naplója|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure tűzfal alkalmazásszabály|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure tűzfal hálózati szabály|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog (Átjáródiagnosztikai napló)|Átjáró diagnosztikai naplói|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog (TunnelDiagnosticLog)|Alagútdiagnosztikai naplók|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog (Útvonaldiagnosztikai napló)|Útvonaldiagnosztikai naplók|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Internetes kulcscsere diagnosztikai naplói|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S diagnosztikai naplók|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEsemények|Traffic Manager probe állapoteredmény-eseménye|
|Microsoft.Network/expressRouteCircuits|Társviszony-létesítési útvonalnaplója|Társviszony-létesítési útvonaltábla-naplók|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog (Átjáródiagnosztikai napló)|Átjáró diagnosztikai naplói|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog (TunnelDiagnosticLog)|Alagútdiagnosztikai naplók|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog (Útvonaldiagnosztikai napló)|Útvonaldiagnosztikai naplók|
|Microsoft.Network/vpnGateways|IKEDiagnosticLog|Internetes kulcscsere diagnosztikai naplói|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Előtér-hozzáférési napló|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor webes alkalmazás tűzfalának naplója|
|Microsoft.Network/p2sVpnGateways|GatewayDiagnosticLog (Átjáródiagnosztikai napló)|Átjáró diagnosztikai naplói|
|Microsoft.Network/p2sVpnGateways|IKEDiagnosticLog|Internetes kulcscsere diagnosztikai naplói|
|Microsoft.Network/p2sVpnGateways|P2SDiagnosticLog|P2S diagnosztikai naplók|
|Microsoft.Network/bastionHosts|Megerősített AuditLogok|Megerősített naplónaplók|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Terheléselosztó riasztási eseményei|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Terheléselosztó mintavételének állapota|
|Microsoft.PowerBIDedikált/kapacitások|Motor|Motor|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure biztonsági mentési jelentési adatai|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Alapvető Azure biztonsági mentési adatok|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupJobs|Addon Azure biztonsági mentési feladat adatai|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Addon Azure biztonsági mentési riasztási adatai|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupPolicy|Addon Azure biztonsági mentési szabályzat adatai|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupTárhely|Addon Azure biztonsági mentési tároló adatai|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupProtectedInstance|Addon Azure biztonsági mentésvédett példányadatai|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure-hely helyreállítási feladatok|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEsemények|Azure-webhely-helyreállítási események|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicateditems|Az Azure webhely-helyreállítási replikált elemek|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Az Azure-webhely helyreállítási replikációjának statisztikái|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints pontok|Az Azure-webhely helyreállítási helyreállítási pontjai|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Az Azure site recovery replikációs replikációs adatok feltöltési sebessége|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure-webhely-helyreállítási védett lemezadat-változási forgalom|
|Microsoft.Search/searchServices|OperationLogs|Műveleti naplók|
|Microsoft.ServiceBus/névterek|Operatív naplók|Operatív naplók|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|Automatikus hangolás|Automatikus hangolás|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatisztika|Lekérdezéstároló futásidejű statisztikája|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatisztikák|Lekérdezéstároló várakozási statisztikája|
|Microsoft.Sql/servers/databases|Hibák|Hibák|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics (Adatbázis–várakozási statisztika)|Adatbázis várakozási statisztikája|
|Microsoft.Sql/servers/databases|Időtúllépések|Időtúllépések|
|Microsoft.Sql/servers/databases|Blokkok|Blokkok|
|Microsoft.Sql/servers/databases|Holtpontok|Holtpontok|
|Microsoft.Sql/servers/databases|Naplózás|Naplók|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Microsoft.Sql/servers/databases|DmsWorkers|Dms dolgozók|
|Microsoft.Sql/servers/databases|ExecRequests|Exec kérések|
|Microsoft.Sql/servers/databases|Kéréslépései|Lépések kérése|
|Microsoft.Sql/servers/databases|SqlRequests|Sql-kérelmek|
|Microsoft.Sql/servers/databases|Vár|Vár|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Erőforrás-használati statisztika|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatisztika|Lekérdezéstároló futásidejű statisztikája|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatisztikák|Lekérdezéstároló várakozási statisztikája|
|Microsoft.Sql/managedInstances/databases|Hibák|Hibák|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite (Tárolás írása)|StorageWrite (Tárolás írása)|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete (Tárolástörlése)|StorageDelete (Tárolástörlése)|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite (Tárolás írása)|StorageWrite (Tárolás írása)|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete (Tárolástörlése)|StorageDelete (Tárolástörlése)|
|Microsoft.Storage/storageAccounts/fileServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite (Tárolás írása)|StorageWrite (Tárolás írása)|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete (Tárolástörlése)|StorageDelete (Tárolástörlése)|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite (Tárolás írása)|StorageWrite (Tárolás írása)|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete (Tárolástörlése)|StorageDelete (Tárolástörlése)|
|Microsoft.StreamAnalytics/streamingjobs|Futtatási|Futtatási|
|Microsoft.StreamAnalytics/streamingjobs|Tartalomkészítés|Tartalomkészítés|
|microsoft.web/hostingkörnyezetek|AppServiceEnvironmentPlatformLogs|App Service-környezet platformnaplói|
|microsoft.web/webhelyek|FunctionAppLogs|Függvény alkalmazásnaplói|
|microsoft.web/webhelyek|AppServiceHTTPLogs|HTTP-naplók|
|microsoft.web/webhelyek|AppServiceConsoleLogs|App Service-konzol naplók|
|microsoft.web/webhelyek|AppServiceAppLogs|App Service-alkalmazásnaplók|
|microsoft.web/webhelyek|AppServiceFileAuditLogs|Webhely tartalomváltozási naplói|
|microsoft.web/webhelyek|AppServiceAuditLogs|Hozzáférés-naplónaplók|
|microsoft.web/sites/slot|FunctionAppLogs|Függvény alkalmazásnaplói|
|microsoft.web/sites/slot|AppServiceHTTPLogs|HTTP-naplók|
|microsoft.web/sites/slot|AppServiceConsoleLogs|Konzolnaplók|
|microsoft.web/sites/slot|AppServiceAppLogs|Alkalmazásnaplók|
|microsoft.web/sites/slot|AppServiceFileAuditLogs|Webhely tartalomváltozási naplói|
|microsoft.web/sites/slot|AppServiceAuditLogs|Hozzáférés-naplónaplók|

## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrásnaplókról](../../azure-monitor/platform/platform-logs-overview.md)
* [Erőforrás-erőforrás-naplók streamelése **az Eseményközpontokba**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Erőforrásnapló diagnosztikai beállításainak módosítása az Azure Monitor REST API-val](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Az Azure Storage naplóinak elemzése a Log Analytics szolgáltatással](../../azure-monitor/platform/collect-azure-metrics-logs.md)
