---
title: Azure Monitor erőforrás-naplók támogatott szolgáltatásai és kategóriái
description: Azure Monitor az Azure-erőforrás-naplók támogatott szolgáltatásainak és az esemény sémájának ismertetése.
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: aeac069b4e9382867664a82af62e29e72da7585e
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232247"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Az Azure erőforrás-naplók támogatott kategóriái

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek. A név 2019 októberében módosult, mivel az Azure Monitor által összegyűjtött naplók típusai többek között az Azure-erőforrást is tartalmazzák.

[Azure monitor az erőforrás-naplókat](./platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) és a `category` séma egyedileg azonosítható kombinációja. A Service-specifikus mezőket tartalmazó összes erőforrás-naplóhoz közös séma tartozik, majd a különböző naplózási kategóriákhoz hozzáadva. További információ: [Common és Service-specifikus séma Azure-beli erőforrás-naplókhoz]()


## <a name="costs"></a>Költségek

Az adatok a Log Analyticsba, az Azure Storage-ba és/vagy az Event Hubbe való küldésével és tárolásával kapcsolatos költségek. Előfordulhat, hogy a költségeket az ilyen helyszínekre való beszerzéshez és a megtartásához kell fizetnie.  Az erőforrás-naplók egy adattípusok, amelyeket elküldhet a következő helyszínekre. Az [erőforrás-naplók egyes kategóriáinak exportálására további költségek vonatkoznak](https://azure.microsoft.com/pricing/details/monitor/) , míg mások az exportálási költségek nélkül. Az export Cost-specifikusak az alábbi táblázatban láthatók.

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplózási kategóriák/erőforrás típusok

Az alábbi lista felsorolja az egyes erőforrástípusok számára elérhető naplók típusait. 

Bizonyos kategóriák csak bizonyos típusú erőforrások esetén támogatottak. Ha úgy érzi, hogy hiányzik egy erőforrás, tekintse meg az erőforrás-specifikus dokumentációt. Például a Microsoft. SQL/kiszolgálók/adatbázisok kategóriái nem érhetők el minden típusú adatbázishoz. További információ: [SQL Database diagnosztikai naplózással kapcsolatos információk](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Ha még valami hiányzik, megnyithatja a cikk alján található GitHub-megjegyzést.
## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Motor|Motor|
|Szolgáltatás|Szolgáltatás|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ApplicationConsole|Application Console|
|SystemLogs|Rendszernaplók|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DscNodeStatus|DSC-csomópont állapota|
|JobLogs|Feladatok naplói|
|JobStreams|Feladatok adatfolyamai|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ServiceLog|Szolgáltatási naplók|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|BlockchainApplication|Blockchain-alkalmazás|
|FabricOrderer|Háló-sorrend|
|FabricPeer|Háló társ|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|BlockchainApplication|Blockchain-alkalmazás|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|WebApplicationFirewallLogs|Webalkalmazási tűzfal naplófájljai|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profilok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AzureCdnAccessLog|Azure CDN hozzáférési napló|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profilok/végpontok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő adatokat stb.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Naplózás|Naplók|
|RequestResponse|Kérelmek és válaszok naplói|
|Nyomkövetés|Nyomkövetési naplók|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ContainerRegistryLoginEvents|Bejelentkezési események|
|ContainerRegistryRepositoryEvents|RepositoryEvent-naplók|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|fürt – automéretező|Kubernetes-fürt autoskálázása|
|Kube – apiserver|Kubernetes API-kiszolgáló|
|Kube – naplózás|Kubernetes naplózása|
|Kube – vezérlő – kezelő|Kubernetes Controller Manager|
|Kube – ütemező|Kubernetes Scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AuditLogs|MiniRP-hívások naplófájljai|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/munkaterületek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|fiókok|Databricks-fiókok|
|fürtök|Databricks-fürtök|
|dbfs|Databricks fájlrendszer|
|instancePools|Példányok készletei|
|feladatok|Databricks-feladatok|
|jegyzetfüzet|Databricks-jegyzetfüzet|
|titkok|Databricks titkai|
|sqlPermissions|Databricks SQLPermissions|
|SSH|Databricks SSH|
|munkaterület|Databricks-munkaterület|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ActivityRuns|Folyamat-futtatási tevékenység naplója|
|PipelineRuns|Folyamat-futtatási napló|
|TriggerRuns|Trigger futtatási naplója|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Naplózás|Naplók|
|Kérelmek|Kérelmek naplói|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/fiókok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ReceivedShareSnapshots|Fogadott megosztási Pillanatképek|
|SentShareSnapshots|Eljuttatott megosztási Pillanatképek|
|Megosztások|Megosztások|
|ShareSubscriptions|Előfizetések megosztása|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|MySqlAuditLogs|MariaDB naplófájlok|
|MySqlSlowLogs|MariaDB-kiszolgáló naplófájljai|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|MySqlAuditLogs|MySQL-naplók|
|MySqlSlowLogs|Lassú MySQL-naplók|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|MySqlAuditLogs|MySQL-naplók|
|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|
|QueryStoreRuntimeStatistics|PostgreSQL Query Store futásidejű statisztikái|
|QueryStoreWaitStatistics|PostgreSQL lekérdezési tár várakozási statisztikái|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Checkpoint|Checkpoint|
|Hiba|Hiba|
|Kezelés|Kezelés|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Checkpoint|Checkpoint|
|Kapcsolat|Kapcsolat|
|Hiba|Hiba|
|Gazdagép regisztrációja|Gazdagép regisztrációja|
|Kezelés|Kezelés|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/munkaterületek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Checkpoint|Checkpoint|
|Hiba|Hiba|
|Adatcsatorna|Adatcsatorna|
|Kezelés|Kezelés|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|C2DCommands|C2D parancsok|
|C2DTwinOperations|C2D – két művelet|
|Konfigurációk|Konfigurációk|
|Kapcsolatok|Kapcsolatok|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Eszköz-identitási műveletek|
|DeviceStreams|Eszköz streamek (előzetes verzió)|
|DeviceTelemetry|Eszköztelemetria|
|DirectMethods|Közvetlen metódusok|
|DistributedTracing|Elosztott nyomkövetés (előzetes verzió)|
|FileUploadOperations|Fájlfeltöltés-műveletek|
|JobsOperations|Feladatok műveletei|
|Útvonalak|Útvonalak|
|TwinQueries|Dupla lekérdezés|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DeviceOperations|Eszköz műveletei|
|ServiceOperations|Szolgáltatási műveletek|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|
|PublishFailures|Sikertelen naplók közzététele|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|
|PublishFailures|Sikertelen naplók közzététele|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ArchiveLogs|Archiválási naplók|
|AutoScaleLogs|Naplók automatikus méretezése|
|CustomerManagedKeyUserLogs|Ügyfél által felügyelt kulcsok naplói|
|EventHubVNetConnectionEvent|VNet/IP-szűrési kapcsolatok naplófájljai|
|KafkaCoordinatorLogs|Kafka-koordinátor naplói|
|KafkaUserErrorLogs|Kafka felhasználói hibák naplói|
|OperationalLogs|Operatív naplók|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/szolgáltatások

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AuditLogs|Naplók|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AutoscaleEvaluations|Az autoscale-értékelések|
|AutoscaleScaleActions|Méretezési műveletek az autoskálázáshoz|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AppAvailabilityResults|Rendelkezésre állási eredmények|
|AppBrowserTimings|Böngésző időzítése|
|AppDependencies|Függőségek|
|AppEvents|Események|
|AppExceptions|Kivételek|
|AppMetrics|Mérőszámok|
|AppPageViews|Lapok nézetei|
|AppPerformanceCounters|Teljesítményszámlálók|
|AppRequests|Kérelmek|
|AppSystemEvents|Rendszeresemények|
|AppTraces|Hívásláncok|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AuditEvent|Naplók|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Parancs|Parancs|
|FailedIngestion|Sikertelen betöltési műveletek|
|IngestionBatching|Ingestion batching|
|Lekérdezés|Lekérdezés|
|SucceededIngestion|Sikeres betöltési műveletek|
|TableDetails|Táblázat részletei|
|TableUsageStatistics|Táblázat-használati statisztika|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|KeyDeliveryRequests|Kulcsok kézbesítésére vonatkozó kérelmek|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|
|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|
|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|BastionAuditLogs|Megerősített naplók|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|FrontdoorAccessLog|Frontdoor hozzáférési naplója|
|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|LoadBalancerAlertEvent|Riasztási események Load Balancer|
|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|
|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|
|DDoSMitigationReports|A DDoS enyhítésének jelentései|
|DDoSProtectionNotifications|DDoS Protection-értesítések|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|IKEDiagnosticLog|IKE diagnosztikai naplók|
|P2SDiagnosticLog|P2S diagnosztikai naplók|
|RouteDiagnosticLog|Diagnosztikai naplók irányítása|
|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|VMProtectionAlerts|VM-védelmi riasztások|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Motor|Motor|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. Recoveryservices szolgáltatónál/tárolók

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AddonAzureBackupAlerts|Addon Azure Backup riasztási adatkezelési|
|AddonAzureBackupJobs|Addon Azure Backup feladatok adatai|
|AddonAzureBackupPolicy|Addon Azure Backup házirend-adatbázis|
|AddonAzureBackupProtectedInstance|Addon Azure Backup védett példányok adatvédelme|
|AddonAzureBackupStorage|Addon Azure Backup Storage-beli adattárak|
|AzureBackupReport|Jelentéskészítési adatgyűjtés Azure Backup|
|AzureSiteRecoveryEvents|Események Azure Site Recovery|
|AzureSiteRecoveryJobs|Azure Site Recovery feladatok|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery védett lemez adatváltozása|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery helyreállítási pontok|
|AzureSiteRecoveryReplicatedItems|Replikált elemek Azure Site Recovery|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery replikációs adatok feltöltési sebessége|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery replikációs statisztika|
|CoreAzureBackup|Alapvető Azure Backup-adathalmazok|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/névterek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|HybridConnectionsEvent|HybridConnections események|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|OperationLogs|Műveleti naplók|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|OperationalLogs|Operatív naplók|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AllLogs|Az Azure Signaler szolgáltatás naplói.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|
|ResourceUsageStats|Erőforrás-használati statisztika|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/adatbázisok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Hibák|Hibák|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|SQLInsights|SQL-ismeretek|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AutomaticTuning|Automatikus hangolás|
|Blokkok|Blokkok|
|DatabaseWaitStatistics|Adatbázis várakozási statisztikája|
|Holtpontok|Holtpontok|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|
|DmsWorkers|DMS-feldolgozók|
|Hibák|Hibák|
|ExecRequests|Exec-kérelmek|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|RequestSteps|Kérelem lépései|
|SQLInsights|SQL-ismeretek|
|SqlRequests|SQL-kérelmek|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Időtúllépések|Időtúllépések|
|Megvárja|Megvárja|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

Exportálási költség: a [Azure monitor díjszabási oldal](https://azure.microsoft.com/pricing/details/monitor/) platform naplók szakaszában leírt módon kell fizetni. 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

Exportálási költség: a [Azure monitor díjszabási oldal](https://azure.microsoft.com/pricing/details/monitor/) platform naplók szakaszában leírt módon kell fizetni. 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

Exportálási költség: a [Azure monitor díjszabási oldal](https://azure.microsoft.com/pricing/details/monitor/) platform naplók szakaszában leírt módon kell fizetni. 
 
|Kategória |Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

Exportálási költség: a [Azure monitor díjszabási oldal](https://azure.microsoft.com/pricing/details/monitor/) platform naplók szakaszában leírt módon kell fizetni. 
 
|Kategória |Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|Tartalomkészítés|Tartalomkészítés|
|Futtatási|Futtatási|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. szinapszis/munkaterületek

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|BuiltinSqlReqsEnded|A beépített SQL Pool-kérelmek véget ért|
|GatewayApiRequests|Szinapszis Gateway API-kérelmek|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|SynapseRbacOperations|Szinapszis RBAC-műveletek|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. szinapszis/munkaterületek/bigDataPools

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|BigDataPoolAppsEnded|A Big adattár-alkalmazások véget ért|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. szinapszis/munkaterületek/sqlPools

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|DmsWorkers|DMS-feldolgozók|
|ExecRequests|Exec-kérelmek|
|RequestSteps|Kérelem lépései|
|SqlRequests|SQL-kérelmek|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|
|Megvárja|Megvárja|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

Exportálási díj: ingyenes 

|Kategória |Kategória megjelenítendő neve|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment platform naplói|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

Exportálási díj: ingyenes 


|Kategória |Kategória megjelenítendő neve|
|---|---|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|
|AppServiceConsoleLogs|App Service konzol naplófájljai|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|
|AppServiceHTTPLogs|HTTP-naplók|
|FunctionAppLogs|Function Application-naplók|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots

Exportálási díj: ingyenes 


|Kategória |Kategória megjelenítendő neve|
|---|---|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|
|AppServiceConsoleLogs|App Service konzol naplófájljai|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|
|AppServiceHTTPLogs|HTTP-naplók|
|FunctionAppLogs|Function Application-naplók|


## <a name="next-steps"></a>Következő lépések

* [További információ az erőforrás-naplókról](./platform-logs-overview.md)
* [Stream erőforrás-erőforrás naplófájljai **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Az erőforrás-napló diagnosztikai beállításainak módosítása a Azure Monitor használatával REST API](/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

