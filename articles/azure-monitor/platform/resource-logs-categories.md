---
title: Azure Monitor erőforrás-naplók támogatott szolgáltatásai és kategóriái
description: Azure Monitor az Azure-erőforrás-naplók támogatott szolgáltatásainak és az esemény sémájának ismertetése.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 81f79b81c03e7996d7f6d45b002d8160740c3c14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318300"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Az Azure erőforrás-naplók támogatott kategóriái

> [!NOTE]
> Az erőforrás-naplók korábban diagnosztikai naplóként ismertek. A név 2019 októberében módosult, mivel az Azure Monitor által összegyűjtött naplók típusai többek között az Azure-erőforrást is tartalmazzák.

[Azure monitor az erőforrás-naplókat](./platform-logs-overview.md) az Azure-szolgáltatások által kibocsátott naplók, amelyek leírják ezeknek a szolgáltatásoknak vagy erőforrásoknak a működését. A Azure Monitoron keresztül elérhető összes erőforrás-napló közös legfelső szintű sémával rendelkezik, és minden szolgáltatás számára rugalmasságot biztosít, hogy egyedi tulajdonságokat bocsát ki a saját eseményeihez.

Az erőforrástípus (a `resourceId` tulajdonságban elérhető) és a `category` séma egyedileg azonosítható kombinációja. A szolgáltatás-specifikus mezőket tartalmazó összes erőforrás-naplóhoz közös séma tartozik, majd a különböző naplózási kategóriákhoz hozzáadva. További információ: [Common és Service-specifikus séma Azure-beli erőforrás-naplókhoz]()

## <a name="supported-log-categories-per-resource-type"></a>Támogatott naplózási kategóriák/erőforrás típusok

Az alábbi lista felsorolja az egyes erőforrástípusok számára elérhető naplók típusait. 

Bizonyos kategóriák csak bizonyos típusú erőforrások esetén támogatottak. Ha úgy érzi, hogy hiányzik egy erőforrás, tekintse meg az erőforrás-specifikus dokumentációt. Például a Microsoft. SQL/kiszolgálók/adatbázisok kategóriái nem érhetők el minden típusú adatbázishoz. További információ: [SQL Database diagnosztikai naplózással kapcsolatos információk](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Ha még valami hiányzik, megnyithatja a cikk alján található GitHub-megjegyzést.

## <a name="microsoftaadiamtenants"></a>Microsoft. aadiam/bérlők

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Bejelentkezési|Bejelentkezési|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/kiszolgálók

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Motor|Motor|
|Szolgáltatás|Szolgáltatás|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategória|Kategória megjelenítendő neve|
|---|---|
|GatewayLogs|A ApiManagement-átjáróval kapcsolatos naplók|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ApplicationConsole|Application Console|
|SystemLogs|Rendszernaplók|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategória|Kategória megjelenítendő neve|
|---|---|
|JobLogs|Feladatok naplói|
|JobStreams|Feladatok adatfolyamai|
|DscNodeStatus|DSC-csomópont állapota|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ServiceLog|Szolgáltatási naplók|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/munkaterületek

|Kategória|Kategória megjelenítendő neve|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. Blockchain/blockchainMembers

|Kategória|Kategória megjelenítendő neve|
|---|---|
|BlockchainApplication|Blockchain-alkalmazás|
|Proxy|Proxy|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. Blockchain/cordaMembers

|Kategória|Kategória megjelenítendő neve|
|---|---|
|BlockchainApplication|Blockchain-alkalmazás|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategória|Kategória megjelenítendő neve|
|---|---|
|WebApplicationFirewallLogs|Webalkalmazási tűzfal naplófájljai|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profilok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AzureCdnAccessLog|Azure CDN hozzáférési napló|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/profilok/végpontok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|CoreAnalytics|Lekéri a végpont metrikáit, például a sávszélességet, a kimenő forgalmat stb.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/fiókok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Naplózás|Naplók|
|RequestResponse|Kérelmek és válaszok naplói|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/nyilvántartók

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ContainerRegistryLoginEvents|Bejelentkezési események|
|ContainerRegistryRepositoryEvents|RepositoryEvent-naplók|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. Tárolószolgáltatás/managedClusters

|Kategória|Kategória megjelenítendő neve|
|---|---|
|fürt – automéretező|Kubernetes-fürt autoskálázása|
|Kube – apiserver|Kubernetes API-kiszolgáló|
|Kube – naplózás|Kubernetes naplózása|
|Kube – vezérlő – kezelő|Kubernetes Controller Manager|
|Kube – ütemező|Kubernetes Scheduler|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AuditLogs|MiniRP-hívások naplófájljai|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. Databricks/munkaterületek

|Kategória|Kategória megjelenítendő neve|
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


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. DataCatalog/datacatalogs

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ScanStatusLogEvent|ScanStatus|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/gyárak

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ActivityRuns|Folyamat-futtatási tevékenység naplója|
|PipelineRuns|Folyamat-futtatási napló|
|TriggerRuns|Trigger futtatási naplója|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. Data Lake Store/fiókok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Naplózás|Naplók|
|Kérelmek|Kérelmek naplói|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/kiszolgálók

|Kategória|Kategória megjelenítendő neve|
|---|---|
|MySqlAuditLogs|MariaDB naplófájlok|
|MySqlSlowLogs|MariaDB-kiszolgáló naplófájljai|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/kiszolgálók

|Kategória|Kategória megjelenítendő neve|
|---|---|
|MySqlAuditLogs|MySQL-naplók|
|MySqlSlowLogs|MySQL-kiszolgáló naplófájljai|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/kiszolgálók

|Kategória|Kategória megjelenítendő neve|
|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|
|QueryStoreRuntimeStatistics|PostgreSQL Query Store futásidejű statisztikái|
|QueryStoreWaitStatistics|PostgreSQL lekérdezési tár várakozási statisztikái|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategória|Kategória megjelenítendő neve|
|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Kategória|Kategória megjelenítendő neve|
|---|---|
|PostgreSQLLogs|PostgreSQL-kiszolgáló naplófájljai|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Checkpoint|Checkpoint|
|Hiba|Hiba|
|Kezelés|Kezelés|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Checkpoint|Checkpoint|
|Kapcsolat|Kapcsolat|
|Hiba|Hiba|
|Gazdagép regisztrációja|Gazdagép regisztrációja|
|Kezelés|Kezelés|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/munkaterületek

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Checkpoint|Checkpoint|
|Hiba|Hiba|
|Adatcsatorna|Adatcsatorna|
|Kezelés|Kezelés|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategória|Kategória megjelenítendő neve|
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

|Kategória|Kategória megjelenítendő neve|
|---|---|
|DeviceOperations|Eszköz műveletei|
|ServiceOperations|Szolgáltatási műveletek|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategória|Kategória megjelenítendő neve|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/szolgáltatások

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AuditEvent|AuditEvent-napló|
|DataIssue|DataIssue-napló|
|Kérelmek|Konfigurációs napló|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/tartományok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|
|PublishFailures|Sikertelen naplók közzététele|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategória|Kategória megjelenítendő neve|
|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témakörök

|Kategória|Kategória megjelenítendő neve|
|---|---|
|DeliveryFailures|Kézbesítési hibák naplói|
|PublishFailures|Sikertelen naplók közzététele|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/névterek

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ArchiveLogs|Archiválási naplók|
|AutoScaleLogs|Naplók automatikus méretezése|
|CustomerManagedKeyUserLogs|Customer-Managed kulcsfontosságú naplók|
|EventHubVNetConnectionEvent|VNet/IP-szűrési kapcsolatok naplófájljai|
|KafkaCoordinatorLogs|Kafka-koordinátor naplói|
|KafkaUserErrorLogs|Kafka felhasználói hibák naplói|
|OperationalLogs|Operatív naplók|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/szolgáltatások

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AuditLogs|Naplók|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. bepillantások/AutoscaleSettings

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AutoscaleEvaluations|Az autoscale-értékelések|
|AutoscaleScaleActions|Méretezési műveletek az autoskálázáshoz|


## <a name="microsoftinsightscomponents"></a>Microsoft. bepillantások/összetevők

|Kategória|Kategória megjelenítendő neve|
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


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Naplózás|Naplózás|
|Kimenő forgalom|Kimenő forgalom|
|Bejövő forgalom|Bejövő forgalom|
|Működik|Működik|
|Nyomkövetés|Nyomkövetés|
|UserDefinedFunction|UserDefinedFunction|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. kulcstartó/tárolók

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AuditEvent|Naplók|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/fürtök

|Kategória|Kategória megjelenítendő neve|
|---|---|
|FailedIngestion|Sikertelen betöltési műveletek|
|SucceededIngestion|Sikeres betöltési műveletek|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategória|Kategória megjelenítendő neve|
|---|---|
|IntegrationAccountTrackingEvents|Integrációs fiók követési eseményei|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/munkafolyamatok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|WorkflowRuntime|Munkafolyamat futásidejű diagnosztikai eseményei|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/munkaterületek

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Mediaservices

|Kategória|Kategória megjelenítendő neve|
|---|---|
|KeyDeliveryRequests|Kulcsok kézbesítésére vonatkozó kérelmek|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ApplicationGatewayAccessLog|Hozzáférési napló Application Gateway|
|ApplicationGatewayFirewallLog|Application Gateway tűzfal naplója|
|ApplicationGatewayPerformanceLog|Application Gateway Teljesítménynapló|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall alkalmazási szabály|
|AzureFirewallNetworkRule|Azure Firewall hálózati szabály|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategória|Kategória megjelenítendő neve|
|---|---|
|BastionAuditLogs|Megerősített naplók|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategória|Kategória megjelenítendő neve|
|---|---|
|PeeringRouteLog|Egyenrangú útválasztási táblázat naplói|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategória|Kategória megjelenítendő neve|
|---|---|
|FrontdoorAccessLog|Frontdoor hozzáférési naplója|
|FrontdoorWebApplicationFirewallLog|Frontdoor webalkalmazási tűzfal naplója|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategória|Kategória megjelenítendő neve|
|---|---|
|LoadBalancerAlertEvent|Riasztási események Load Balancer|
|LoadBalancerProbeHealthStatus|Load Balancer mintavétel állapotának állapota|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategória|Kategória megjelenítendő neve|
|---|---|
|NetworkSecurityGroupEvent|Hálózati biztonsági csoport eseménye|
|NetworkSecurityGroupFlowEvent|Hálózati biztonsági csoportra vonatkozó szabály folyamatának eseménye|
|NetworkSecurityGroupRuleCounter|Hálózati biztonsági csoport szabályának számlálója|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/nyilvános IP

|Kategória|Kategória megjelenítendő neve|
|---|---|
|DDoSMitigationFlowLogs|A DDoS-elhárítási döntések folyamatának naplói|
|DDoSMitigationReports|A DDoS enyhítésének jelentései|
|DDoSProtectionNotifications|DDoS Protection-értesítések|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategória|Kategória megjelenítendő neve|
|---|---|
|ProbeHealthStatusEvents|Traffic Manager mintavételi állapot eredményeinek eseménye|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategória|Kategória megjelenítendő neve|
|---|---|
|GatewayDiagnosticLog|Átjáró diagnosztikai naplói|
|IKEDiagnosticLog|IKE diagnosztikai naplók|
|P2SDiagnosticLog|P2S diagnosztikai naplók|
|RouteDiagnosticLog|Diagnosztikai naplók irányítása|
|TunnelDiagnosticLog|Alagutak diagnosztikai naplói|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategória|Kategória megjelenítendő neve|
|---|---|
|VMProtectionAlerts|VM-védelmi riasztások|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitások

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Motor|Motor|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. Recoveryservices szolgáltatónál/tárolók

|Kategória|Kategória megjelenítendő neve|
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

|Kategória|Kategória megjelenítendő neve|
|---|---|
|HybridConnectionsEvent|HybridConnections események|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategória|Kategória megjelenítendő neve|
|---|---|
|OperationLogs|Műveleti naplók|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/névterek

|Kategória|Kategória megjelenítendő neve|
|---|---|
|OperationalLogs|Operatív naplók|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/szignáló

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AllLogs|Az Azure Signaler szolgáltatás naplói.|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategória|Kategória megjelenítendő neve|
|---|---|
|DevOpsOperationsAudit|Devops-műveletek naplófájljai|
|ResourceUsageStats|Erőforrás-használati statisztika|
|SQLSecurityAuditEvents|SQL biztonsági naplózási esemény|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/adatbázisok

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Hibák|Hibák|
|QueryStoreRuntimeStatistics|Lekérdezési tár futásidejű statisztikái|
|QueryStoreWaitStatistics|Lekérdezési tároló várakozási statisztikája|
|SQLInsights|SQL-ismeretek|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/kiszolgálók/adatbázisok

|Kategória|Kategória megjelenítendő neve|
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

|Kategória|Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Kategória|Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategória|Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategória|Kategória megjelenítendő neve|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategória|Kategória megjelenítendő neve|
|---|---|
|Tartalomkészítés|Tartalomkészítés|
|Futtatási|Futtatási|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment platform naplói|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Kategória|Kategória megjelenítendő neve|
|---|---|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|
|AppServiceConsoleLogs|App Service konzol naplófájljai|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|
|AppServiceHTTPLogs|HTTP-naplók|
|FunctionAppLogs|Function Application-naplók|
|ScanLogs|Víruskeresési naplók|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/Slots


|Kategória|Kategória megjelenítendő neve|
|---|---|
|AppServiceAppLogs|App Service alkalmazás naplófájljai|
|AppServiceAuditLogs|Hozzáférés-naplózási naplók|
|AppServiceConsoleLogs|App Service konzol naplófájljai|
|AppServiceFileAuditLogs|Webhely tartalmának változási naplófájljai|
|AppServiceHTTPLogs|HTTP-naplók|
|FunctionAppLogs|Function Application-naplók|
|ScanLogs|Víruskeresési naplók|


## <a name="next-steps"></a>További lépések

* [További információ az erőforrás-naplókról](./platform-logs-overview.md)
* [Stream erőforrás-erőforrás naplófájljai **Event Hubs**](./resource-logs.md#send-to-azure-event-hubs)
* [Az erőforrás-napló diagnosztikai beállításainak módosítása a Azure Monitor használatával REST API](/rest/api/monitor/diagnosticsettings)
* [Naplók elemzése az Azure Storage-ból Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

