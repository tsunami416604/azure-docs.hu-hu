---
title: Az Azure Resource Manager-címke támogatás az erőforrások
description: Látható, hogy melyik Azure-erőforrástípus támogatja a címkék. Az összes Azure-szolgáltatások részletes adatokat biztosít.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000291"
---
# <a name="tag-support-for-azure-resources"></a>Az Azure-erőforrások címkék támogatása
Ez a cikk ismerteti, hogy támogatja-e egy erőforrástípus [címkézés](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>AAD tartományi szolgáltatásokra
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| tartományok | Nem | 

## <a name="ad-hybrid-health-service"></a>Hibrid AD Állapotfigyelő szolgáltatás
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| addsservices | Nem |
| aadsupportcases | Nem | 
| ügynökök | Nem | 
| anonymousapiusers | Nem | 
| konfiguráció | Nem | 
| naplók | Nem | 
| jelentések | Nem | 
| services | Nem | 
| servicehealthmetrics | Nem | 

## <a name="aks"></a>AKS
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| managedClusters | Igen | 

## <a name="analysis-services"></a>Analysis Services
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 

## <a name="api-hubs"></a>API-hubok
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| apiManagementAccounts | Nem | 
| apiManagementAccounts/API-k | Nem | 
| apiManagementAccounts/connectionAcls | Nem | 
| apiManagementAccounts/connectionProviders | Nem | 
| apiManagementAccounts/connectionProviderAcls | Nem | 
| apiManagementAccounts/kapcsolatok | Nem | 

## <a name="api-management"></a>API Management
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| szolgáltatás | Igen | 

## <a name="automation"></a>Automation
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| AutomationAccounts | Igen | 
| automationAccounts/konfigurációk | Igen | 
| automationAccounts/feladatok | Nem | 
| automationAccounts/runbookok | Igen | 
| automationAccounts/softwareUpdateConfigurations | Nem | 
| automationAccounts és webhookok | Nem | 

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/konfigurációk | Nem |
| kiszolgálók és adatbázisok | Nem |
| kiszolgálók/firewallRules | Nem |
| kiszolgálók/recoverableServers | Nem | 
| kiszolgálók/securityAlertPolicies | Nem |
| kiszolgálók/virtualNetworkRules | Nem | 

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/konfigurációk | Nem |
| kiszolgálók és adatbázisok | Nem |
| kiszolgálók/firewallRules | Nem |
| kiszolgálók/recoverableServers | Nem | 
| kiszolgálók/securityAlertPolicies | Nem |
| kiszolgálók/virtualNetworkRules | Nem | 

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/tanácsadók | Nem | 
| kiszolgálók/konfigurációk | Nem |
| kiszolgálók és adatbázisok | Nem |
| kiszolgálók/firewallRules | Nem |
| kiszolgálók/queryTexts | Nem | 
| kiszolgálók/recoverableServers | Nem | 
| kiszolgálók/securityAlertPolicies | Nem |
| kiszolgálók/topQueryStatistics | Nem | 
| kiszolgálók/virtualNetworkRules | Nem | 
| kiszolgálók/waitStatistics | Nem | 

## <a name="batch"></a>Batch
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| batchAccounts | Igen | 

## <a name="bing-maps"></a>Bing Térképek
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| mapApis | Igen | 

## <a name="biztalk-services"></a>Biztalk Services
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| BizTalk | Igen | 

## <a name="cache"></a>Gyorsítótár
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Redis | Igen | 

## <a name="cdn"></a>Tartalomkézbesítési hálózat (CDN)
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| edgenodes | Nem | 
| Profilok | Igen | 
| profilok és végpontok | Igen | 
| profilok és végpontok/customdomains | Nem | 
| profilok és végpontok és források | Nem | 
| validateProbe | Nem | 

## <a name="classic-compute"></a>Klasszikus Compute
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| domainNames | Nem | 
| domainNames/tárolóhelyek | Nem | 
| domainNames/tárolóhelyek/szerepkörök | Nem | 
| virtuális gép | Nem | 
| virtuális gép/diagnosticSettings | Nem | 
| virtuális gép/metricDefinitions | Nem | 
| virtuális gép/a mérőszámok | Nem | 

## <a name="classic-infrastructure-migrate"></a>Klasszikus infrastruktúra áttelepítése
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| classicInfrastructureResources | Nem | 

## <a name="classic-network"></a>Classic Network
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| virtualNetworks | Nem | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem | 
| virtualNetworks/virtualNetworkPeerings | Nem | 

## <a name="classic-storage"></a>Hagyományos tároló
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| storageAccounts/services | Nem | 
| storageAccounts/services/diagnosticSettings | Nem | 

## <a name="compute"></a>Compute
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| availabilitySets | Igen | 
| Lemezek | Igen | 
| images | Igen | 
| restorePointCollections | Igen | 
| restorePointCollections/restorePoints | Nem | 
| sharedVMImages | Igen | 
| sharedVMImages/verziója | Igen | 
| pillanatképek | Igen | 
| virtuális gép | Igen | 
| virtuális gép/diagnosticSettings | Nem | 
| virtuális gép/bővítmények | Igen | 
| virtuális gép/metricDefinitions | Nem | 
| virtualMachineScaleSets | Igen | 
| virtualMachineScaleSets/bővítmények | Nem | 
| virtualMachineScaleSets/hálózati | Nem | 
| virtualMachineScaleSets/publicIPAddresses | Nem | 
| virtualMachineScaleSets/virtuális gép | Nem | 
| virtualMachineScaleSets/virtuális gép/hálózati | Nem | 

## <a name="container"></a>Tároló
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| containerGroups | Igen | 

## <a name="container-instance"></a>Tárolópéldány
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| containerGroups | Igen | 
| serviceAssociationLinks | Nem | 

## <a name="container-registry"></a>Container Registry
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| beállításjegyzékek | Igen | 
| beállításjegyzékek/replikációk | Igen |
| beállításjegyzékek/feladatok | Igen |
| beállításjegyzékek és webhookok | Igen |

## <a name="container-service"></a>Container Service
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| containerServices | Igen | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="cosmos-db"></a>Cosmos DB
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| databaseAccounts | Igen | 
| databaseAccountNames | Nem | 

## <a name="cost-management"></a>Cost Management
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Összekötők | Igen | 

## <a name="data-box"></a>Data Box
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| feladatok | Igen | 

## <a name="data-box-edge"></a>Data Box Edge
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| DataBoxEdgeDevices | Igen | 

## <a name="data-catalog"></a>Data Catalog
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| katalógusok | Igen | 

## <a name="data-connect"></a>Adatok csatlakoztatása
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| connectionManagers | Igen | 

## <a name="data-factory"></a>Data Factory
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| dataFactories | Igen | 
| dataFactories/diagnosticSettings | Nem | 
| dataFactories/metricDefinitions | Nem | 
| dataFactorySchema | Nem | 
| gyárak | Igen | 
| előállítók/integrationRuntimes | Nem | 

## <a name="devices"></a>Eszközök
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| IotHubs | Igen | 
| IotHubs/eventGridFilters | Nem | 
| ProvisioningServices | Igen | 

## <a name="devspaces"></a>Devspaces
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Tartományvezérlők | Igen | 

## <a name="devtest-lab"></a>DevTest Lab
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Laborgyakorlatok | Igen | 
| laborok/artifactsources | Igen |
| laborok/költségek | Igen |
| laborok/customimages | Igen |
| laborok/képletek | Igen |
| laborok/notificationchannels | Igen |
| laborok/policysets/házirendek | Igen |
| laborok/ütemezések | Igen |
| laborok/serviceRunners | Igen | 
| laborok/felhasználók | Igen |
| laborok/felhasználó/lemez | Igen |
| laborok/felhasználó/környezetek | Igen |
| laborok/felhasználó/titkos kulcsok | Igen |
| laborok/felhasználó/servicefabrics | Igen |
| ütemezések Labs/felhasználó/servicefabrics | Igen |
| laborok/virtuális gép | Igen | 
| laborok/virtuális gép/ütemezések | Igen |
| laborok/virtualnetworks | Igen |
| Ütemezések | Igen | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| lcsprojects | Nem | 
| lcsprojects vagy összekötőkkel | Nem | 
| lcsprojects/clouddeployments | Nem | 

## <a name="event-grid"></a>Event Grid
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| tartományok | Igen | 
| tartományok/kapcsolatos témakörök | Nem | 
| eventSubscriptions | Nem | 
| extensionTopics | Nem | 
| kapcsolatos témakörök | Igen | 
| topicTypes | Nem | 

## <a name="event-hub"></a>Eseményközpont
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Fürtök | Igen | 
| Névterek | Igen | 
| névtér/szabályok | Nem |
| névtér/disasterRecoveryConfigs | Nem |
| névtér/eventhubs | Nem |
| névtér/eventhubs/szabályok | Nem |
| névtér/eventhubs/consumergroups | Nem |

## <a name="hana-on-azure"></a>Azure-beli Hana
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| hanaInstances | Igen | 

## <a name="hdinsight"></a>HDInsight
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Fürtök | Igen | 
| fürtök és alkalmazások | Nem | 

## <a name="import-export"></a>Importálás és exportálás
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| feladatok | Igen | 

## <a name="insights"></a>Insights
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| actionGroups | Igen |
| activityLogAlerts | Igen |
| alertrules | Igen |
| automatedExportSettings | Nem | 
| Összetevők | Igen | 
| összetevő/események | Nem | 
| összetevő/a mérőszámok | Nem | 
| összetevő/pricingPlans | Nem | 
| összetevő/lekérdezés | Nem | 
| naplók | Nem | 
| metricAlerts | Igen |
| migrateToNewPricingModel | Nem | 
| myWorkbooks | Nem | 
| lekérdezés | Nem | 
| rollbackToLegacyPricingModel | Nem | 
| scheduledqueryrules | Igen | 
| webteszt | Igen | 
| munkafüzetek | Igen | 

## <a name="key-vault"></a>Key Vault
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| deletedVaults | Nem | 
| tárolók | Igen | 
| tárolók/accessPolicies | Nem | 
| Tárolók vagy titkos kódokkal | Nem | 

## <a name="log-analytics"></a>Log Analytics
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| naplók | Nem | 

## <a name="logic"></a>Logika
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| integrationAccounts | Igen | 
| A munkafolyamatok | Igen | 

## <a name="machine-learning-services"></a>Machine Learning Services
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| munkaterületek | Igen | 
| munkaterületek és számítási erőforrások | Nem | 

## <a name="managed-identity"></a>Felügyelt identitás
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Identitások | Nem | 
| userAssignedIdentities | Igen | 

## <a name="marketplace-apps"></a>Marketplace-alkalmazások
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| classicDevServices | Igen | 

## <a name="marketplace-ordering"></a>Marketplace-en rendezése
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Szerződések | Nem | 
| offertypes | Nem | 

## <a name="media"></a>Média
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| mediaservices | Igen | 
| mediaservices/accountFilters | Nem | 
| mediaservices/eszközök | Nem | 
| mediaservices/eszközök/assetFilters | Nem | 
| mediaservices/contentKeyPolicies | Nem | 
| mediaservices/eventGridFilters | Nem | 
| mediaservices/liveEventOperations | Nem | 
| mediaservices/liveEvents | Igen | 
| mediaservices/liveEvents/liveOutputs | Nem | 
| mediaservices/liveOutputOperations | Nem | 
| mediaservices/Streamvégpontok | Igen | 
| mediaservices/streamingEndpointOperations | Nem | 
| mediaservices/streamingLocators | Nem | 
| mediaservices/streamingPolicies | Nem | 
| mediaservices/átalakítások | Nem | 
| mediaservices/átalakítások és feladatok | Nem | 

## <a name="network"></a>Network (Hálózat)
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| applicationGateways | Igen | 
| applicationSecurityGroups | Igen | 
| azureFirewalls | Igen | 
| kapcsolatok | Igen | 
| ddosProtectionPlans | Igen | 
| expressRouteCircuits | Igen | 
| frontdoors | Igen | 
| frontdoorWebApplicationFirewallPolicies | Igen | 
| interfaceEndpoints | Igen | 
| Használatát | Igen | 
| localNetworkGateways | Igen | 
| networkIntentPolicies | Igen | 
| Hálózati | Igen | 
| networkProfiles | Igen | 
| networkSecurityGroups | Igen | 
| networkWatchers | Igen | 
| networkWatchers/connectionMonitors | Igen | 
| networkWatchers/átvilágított felülettel | Igen | 
| networkWatchers/pingMeshes | Igen | 
| privateLinkServices | Igen | 
| publicIPAddresses | Igen | 
| publicIPPrefixes | Igen | 
| routeFilters | Igen | 
| routeTables | Igen | 
| serviceEndpointPolicies | Igen | 
| virtualHubs | Igen | 
| virtualNetworks | Igen | 
| virtualnetworkgateways-hez | Igen | 
| virtualNetworkTaps | Igen | 
| virtualWans | Igen | 
| vpnGateways | Igen | 
| vpnSites | Igen | 
| webApplicationFirewallPolicies | Igen | 

## <a name="notification-hubs"></a>Notification Hubs
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Névterek | Igen | 
| névtér/notificationHubs | Igen | 

## <a name="operational-insights"></a>Operational Insights
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| munkaterületek | Igen |
| munkaterületek/adatforrások | Igen |
| munkaterületek/linkedServices | Igen |
| munkaterületek/savedSearches | Nem |
| munkaterületek/storageInsightConfigs | Igen |

## <a name="operations-management"></a>Operations Management
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| megoldások | Nem |

## <a name="portal"></a>Portál
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Az irányítópultok | Igen | 

## <a name="portal-sdk"></a>Portál SDK
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| rootResources | Igen | 

## <a name="power-bi"></a>Power BI
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| workspaceCollections | Igen | 

## <a name="recovery-services"></a>Recovery Services
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| backupProtectedItems | Nem | 
| tárolók | Igen | 

## <a name="relay"></a>Továbbító
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Névterek | Igen | 

## <a name="resources"></a>További források
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| resourceGroups | Igen | 
| előfizetés/resourceGroups | Igen | 

## <a name="scheduler"></a>Scheduler
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| feladatgyűjtemények | Igen | 
| forgalom | Igen | 

## <a name="search"></a>Keresés
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| resourceHealthMetadata | Nem | 
| searchServices | Igen | 

## <a name="security"></a>Biztonság
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| dataCollectionAgents | Nem | 

## <a name="service-bus"></a>Service Bus
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Névterek | Igen | 
| névtér/eventgridfilters | Nem | 

## <a name="service-fabric"></a>Service Fabric
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Fürtök | Igen | 
| fürtök és alkalmazások | Nem | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| alkalmazások | Igen | 
| Hálózatok | Igen | 
| volumes | Igen | 

## <a name="signalr-service"></a>SignalR szolgáltatás
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| SignalR | Igen | 

## <a name="site-recovery"></a>Site Recovery
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| SiteRecoveryVault | Igen | 

## <a name="solutions"></a>Megoldások
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| alkalmazások | Igen | 
| applicationDefinitions | Igen | 
| jitRequests | Igen | 

## <a name="sql"></a>SQL
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| helyek/instanceFailoverGroups | Nem |
| managedInstances | Igen |
| managedInstances/adatbázis | Igen |
| managedInstances/adatbázisok/backupShortTermRetentionPolicies | Nem |
| managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels | Nem |
| managedInstances/adatbázisok/vulnerabilityAssessments | Nem |
| managedInstances/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek | Nem |
| managedInstances/encryptionProtector | Nem |
| managedInstances és kulcs | Nem |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem |
| managedInstances/vulnerabilityAssessments | Nem |
| kiszolgáló | Igen |
| kiszolgálók és a rendszergazdák | Nem |
| kiszolgálók/tanácsadók | Nem |
| kiszolgálók/auditingSettings | Nem |
| kiszolgálók/backupLongTermRetentionVaults | Nem |
| kiszolgálók/communicationLinks | Nem |
| kiszolgálók/connectionPolicies | Nem |
| kiszolgálók és adatbázisok | Igen |
| kiszolgálók és adatbázisok/tanácsadók | Nem |
| kiszolgálók és adatbázisok/auditingSettings | Nem |
| kiszolgálók és adatbázisok/backupLongTermRetentionPolicies | Nem |
| kiszolgálók és adatbázisok/backupShortTermRetentionPolicies | Nem |
| kiszolgálók és adatbázisok/connectionPolicies | Nem |
| kiszolgálók és adatbázisok/dataMaskingPolicies | Nem |
| kiszolgálók és adatbázisok/dataMaskingPolicies/szabályok | Nem |
| kiszolgálók és adatbázisok/extendedAuditingSettings | Nem |
| kiszolgálók és adatbázisok/bővítmények | Nem |
| kiszolgálók és adatbázisok/geoBackupPolicies | Nem |
| kiszolgálók és adatbázisok/sémák/táblák/oszlopok/sensitivityLabels | Nem |
| kiszolgálók és adatbázisok/securityAlertPolicies | Nem |
| kiszolgálók és adatbázisok/syncGroups | Nem |
| kiszolgálók és adatbázisok/syncGroups/syncMembers | Nem |
| kiszolgálók és adatbázisok/transparentDataEncryption | Nem |
| kiszolgálók és adatbázisok/vulnerabilityAssessments | Nem |
| kiszolgálók és adatbázisok/vulnerabilityAssessments/szabályok/alaptervek | Nem |
| kiszolgálók/disasterRecoveryConfiguration | Nem |
| kiszolgálók/dnsAliases | Nem |
| kiszolgálók/elasticPools | Igen |
| kiszolgálók/encryptionProtector | Nem |
| kiszolgálók/extendedAuditingSettings | Nem |
| kiszolgálók/failoverGroups | Igen |
| kiszolgálók/firewallRules | Nem |
| kiszolgálók/jobAgents | Igen |
| kiszolgálók/jobAgents/hitelesítő adatok | Nem |
| kiszolgálók/jobAgents/feladatok | Nem |
| kiszolgálók/jobAgents/feladatok/végrehajtások | Nem |
| kiszolgálók/jobAgents/feladatok/lépéseket | Nem |
| kiszolgálók/jobAgents/célcsoportok | Nem |
| kiszolgálók és kulcs | Nem |
| kiszolgálók/securityAlertPolicies | Nem |
| kiszolgálók/syncAgents | Nem |
| kiszolgálók/virtualNetworkRules | Nem |
| kiszolgálók/vulnerabilityAssessments | Nem |

## <a name="sql-virtual-machine"></a>Az SQL virtuális gép
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| DWVM | Igen | 

## <a name="storage"></a>Storage
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Tárfiókok | Igen | 
| storageAccounts/blobServices | Nem | 
| storageAccounts/fileServices | Nem | 
| storageAccounts/queueServices | Nem | 
| storageAccounts/services | Nem | 
| storageAccounts/services/metricDefinitions | Nem | 
| storageAccounts/tableServices | Nem | 

## <a name="storage-sync"></a>Társzinkronizálási
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| storageSyncServices | Igen | 
| storageSyncServices/registeredServers | Nem | 
| storageSyncServices/syncGroups | Nem | 
| storageSyncServices/syncGroups/cloudEndpoints | Nem | 
| storageSyncServices/syncGroups/serverEndpoints | Nem | 
| storageSyncServices/munkafolyamatok | Nem | 

## <a name="storsimple"></a>A Storsimple
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| vezetők | Igen | 

## <a name="stream-analytics"></a>Stream Analytics
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| streamingjobs | Igen | 
| streamingjobs/diagnosticSettings | Nem | 
| streamingjobs/metricDefinitions | Nem | 

## <a name="subscription"></a>Előfizetés
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| SubscriptionDefinitions | Nem | 
| SubscriptionOperations | Nem | 

## <a name="support"></a>Támogatás
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| supporttickets | Nem | 

## <a name="visual-studio"></a>Visual Studio
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| account | Igen | 
| fiók/bővítmény | Igen | 
| fiók és a projekt | Igen | 

## <a name="web"></a>Web
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| apiManagementAccounts | Nem | 
| apiManagementAccounts/apiAcls | Nem | 
| apiManagementAccounts/API-k | Nem | 
| apiManagementAccounts/API/apiAcls | Nem | 
| apiManagementAccounts/API/connectionAcls | Nem | 
| apiManagementAccounts/API/kapcsolatok | Nem | 
| apiManagementAccounts/API/kapcsolat/connectionAcls | Nem | 
| apiManagementAccounts/API/localizedDefinitions | Nem | 
| apiManagementAccounts/connectionAcls | Nem | 
| apiManagementAccounts/kapcsolatok | Nem | 
| billingMeters | Nem | 
| tanúsítványok | Igen | 
| connectionGateways | Igen | 
| kapcsolatok | Igen | 
| customApis | Igen | 
| deletedSites | Nem | 
| functions | Nem | 
| hostingEnvironments | Igen | 
| hostingEnvironments/a mérőszámok | Nem | 
| hostingEnvironments/multiRolePools | Nem | 
| hostingEnvironments/workerPools | Nem | 
| publishingUsers | Nem | 
| Kiszolgálófarmok | Igen | 
| Kiszolgálófarmok/feldolgozó | Nem | 
| Helyek | Igen | 
| webhelyek/domainOwnershipIdentifiers | Nem | 
| webhelyek/hostNameBindings | Nem | 
| helyek és példányok | Nem | 
| webhelyek/példány/bővítmények | Nem | 
| helyek és metrikák | Nem | 
| webhelyek/premieraddons | Igen | 
| helyek és tárhelyek | Igen | 
| helyek és tárolóhelyek/hostNameBindings | Nem | 
| helyek és tárolóhelyek/példány | Nem | 
| webhelyek/tárolóhelyek/példány/bővítmények | Nem | 
| webhelyek/tárolóhelyek/a mérőszámok | Nem | 
| sourceControls | Nem | 
| Ellenőrzése | Nem | 
| verifyHostingEnvironmentVnet | Nem | 

## <a name="xrm"></a>XRM
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| organizations | Nem | 

## <a name="next-steps"></a>További lépések
Erőforrások címkékkel kezelésével kapcsolatos információkért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md).
