---
title: Az Azure Resource Manager-címke támogatás az erőforrások
description: Látható, hogy melyik Azure-erőforrástípus támogatja a címkék. Az összes Azure-szolgáltatások részletes adatokat biztosít.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 99bac69d096095f660e2624194b72e1390c6fc22
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275564"
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
| services | Nem | 
| addsservices | Nem | 
| konfiguráció | Nem | 
| ügynökök | Nem | 
| aadsupportcases | Nem | 
| Jelentések | Nem | 
| servicehealthmetrics | Nem | 
| naplók | Nem | 
| anonymousapiusers | Nem | 

## <a name="analysis-services"></a>Analysis Services
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 

## <a name="api-hubs"></a>API-hubok
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| apiManagementAccounts | Nem | 
| apiManagementAccounts/connectionProviders | Nem | 
| apiManagementAccounts/kapcsolatok | Nem | 
| apiManagementAccounts/connectionAcls | Nem | 
| apiManagementAccounts/connectionProviderAcls | Nem | 
| apiManagementAccounts/API-k | Nem | 

## <a name="api-management"></a>API Management
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| szolgáltatás | Igen | 

## <a name="automation"></a>Automation
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| AutomationAccounts | Igen | 
| automationAccounts/runbookok | Igen | 
| automationAccounts/konfigurációk | Igen | 
| automationAccounts és webhookok | Nem | 
| automationAccounts/softwareUpdateConfigurations | Nem | 
| automationAccounts/feladatok | Nem | 

## <a name="batch"></a>Batch
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| batchAccounts | Igen | 

## <a name="batch-ai"></a>Batch AI
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Fürtök | Igen | 
| feladatok | Igen | 
| fileservers | Igen | 
| munkaterületek | Igen | 
| munkaterületek /-fürt | Nem | 
| munkaterületek/fileservers | Nem | 
| munkaterületek/kísérletek | Nem | 
| munkaterületek/kísérletek/feladatok | Nem | 

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
| Profilok | Igen | 
| profilok és végpontok | Igen | 
| profilok és végpontok és források | Nem | 
| profilok és végpontok/customdomains | Nem | 
| validateProbe | Nem | 
| edgenodes | Nem | 

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
| virtualNetworks/virtualNetworkPeerings | Nem | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem | 

## <a name="classic-storage"></a>Hagyományos tároló
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| storageAccounts/services | Nem | 
| storageAccounts/services/diagnosticSettings | Nem | 

## <a name="compute"></a>Compute
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| availabilitySets | Igen | 
| virtuális gép | Igen | 
| virtuális gép/bővítmények | Igen | 
| virtualMachineScaleSets | Igen | 
| virtualMachineScaleSets/bővítmények | Nem | 
| virtualMachineScaleSets/virtuális gép | Nem | 
| virtualMachineScaleSets/hálózati | Nem | 
| virtualMachineScaleSets/virtuális gép/hálózati | Nem | 
| virtualMachineScaleSets/publicIPAddresses | Nem | 
| restorePointCollections | Igen | 
| restorePointCollections/restorePoints | Nem | 
| virtuális gép/diagnosticSettings | Nem | 
| virtuális gép/metricDefinitions | Nem | 
| sharedVMImages | Igen | 
| sharedVMImages/verziója | Igen | 
| Lemezek | Igen | 
| pillanatképek | Igen | 
| images | Igen | 

## <a name="container"></a>Tároló
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| containerGroups | Igen | 

## <a name="container-instance"></a>Tárolópéldány
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| containerGroups | Igen | 
| serviceAssociationLinks | Nem | 

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

## <a name="data-box-edge"></a>Data Box Edge
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| DataBoxEdgeDevices | Igen | 
| DataBoxEdgeDevices/checkNameAvailability | Nem | 

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
| gyárak | Igen | 
| előállítók/integrationRuntimes | Nem | 
| dataFactories/diagnosticSettings | Nem | 
| dataFactories/metricDefinitions | Nem | 
| dataFactorySchema | Nem | 

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
| Ütemezések | Igen | 
| laborok/virtuális gép | Igen | 
| laborok/serviceRunners | Igen | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| lcsprojects | Nem | 
| lcsprojects vagy összekötőkkel | Nem | 
| lcsprojects/clouddeployments | Nem | 

## <a name="event-grid"></a>Event Grid
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| eventSubscriptions | Nem | 
| kapcsolatos témakörök | Igen | 
| tartományok | Igen | 
| tartományok/kapcsolatos témakörök | Nem | 
| topicTypes | Nem | 
| extensionTopics | Nem | 

## <a name="event-hub"></a>Eseményközpont
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Névterek | Igen | 
| Fürtök | Igen | 

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
| Összetevők | Igen | 
| összetevő/lekérdezés | Nem | 
| összetevő/a mérőszámok | Nem | 
| összetevő/események | Nem | 
| webteszt | Igen | 
| lekérdezés | Nem | 
| scheduledqueryrules | Igen | 
| összetevő/pricingPlans | Nem | 
| migrateToNewPricingModel | Nem | 
| rollbackToLegacyPricingModel | Nem | 
| automatedExportSettings | Nem | 
| munkafüzetek | Igen | 
| myWorkbooks | Nem | 
| naplók | Nem | 

## <a name="key-vault"></a>Key Vault
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| tárolók | Igen | 
| Tárolók vagy titkos kódokkal | Nem | 
| tárolók/accessPolicies | Nem | 
| deletedVaults | Nem | 

## <a name="log-analytics"></a>Log Analytics
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| naplók | Nem | 

## <a name="logic"></a>Logika
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| A munkafolyamatok | Igen | 
| integrationAccounts | Igen | 

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

## <a name="mariadb"></a>MariaDB
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/recoverableServers | Nem | 
| kiszolgálók/virtualNetworkRules | Nem | 

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
| mediaservices/eszközök | Nem | 
| mediaservices/contentKeyPolicies | Nem | 
| mediaservices/streamingLocators | Nem | 
| mediaservices/streamingPolicies | Nem | 
| mediaservices/eventGridFilters | Nem | 
| mediaservices/átalakítások | Nem | 
| mediaservices/átalakítások és feladatok | Nem | 
| mediaservices/Streamvégpontok | Igen | 
| mediaservices/liveEvents | Igen | 
| mediaservices/liveEvents/liveOutputs | Nem | 
| mediaservices/streamingEndpointOperations | Nem | 
| mediaservices/liveEventOperations | Nem | 
| mediaservices/liveOutputOperations | Nem | 
| mediaservices/eszközök/assetFilters | Nem | 
| mediaservices/accountFilters | Nem | 

## <a name="mysql"></a>MySQL
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/recoverableServers | Nem | 
| kiszolgálók/virtualNetworkRules | Nem | 

## <a name="network"></a>Network (Hálózat)
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| virtualNetworks | Igen | 
| publicIPAddresses | Igen | 
| Hálózati | Igen | 
| interfaceEndpoints | Igen | 
| Használatát | Igen | 
| networkSecurityGroups | Igen | 
| applicationSecurityGroups | Igen | 
| serviceEndpointPolicies | Igen | 
| networkIntentPolicies | Igen | 
| routeTables | Igen | 
| publicIPPrefixes | Igen | 
| networkWatchers | Igen | 
| networkWatchers/connectionMonitors | Igen | 
| networkWatchers/átvilágított felülettel | Igen | 
| networkWatchers/pingMeshes | Igen | 
| virtualnetworkgateways-hez | Igen | 
| localNetworkGateways | Igen | 
| kapcsolatok | Igen | 
| applicationGateways | Igen | 
| expressRouteCircuits | Igen | 
| routeFilters | Igen | 
| virtualWans | Igen | 
| vpnSites | Igen | 
| virtualHubs | Igen | 
| vpnGateways | Igen | 
| azureFirewalls | Igen | 
| virtualNetworkTaps | Igen | 
| privateLinkServices | Igen | 
| ddosProtectionPlans | Igen | 
| networkProfiles | Igen | 
| frontdoors | Igen | 
| frontdoorWebApplicationFirewallPolicies | Igen | 
| webApplicationFirewallPolicies | Igen | 

## <a name="notification-hubs"></a>Notification Hubs
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Névterek | Igen | 
| névtér/notificationHubs | Igen | 

## <a name="portal"></a>Portál
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Az irányítópultok | Igen | 

## <a name="portal-sdk"></a>Portál SDK
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| rootResources | Igen | 

## <a name="postgresql"></a>PostgreSQL
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/recoverableServers | Nem | 
| kiszolgálók/virtualNetworkRules | Nem | 
| kiszolgálók/topQueryStatistics | Nem | 
| kiszolgálók/queryTexts | Nem | 
| kiszolgálók/waitStatistics | Nem | 
| kiszolgálók/tanácsadók | Nem | 

## <a name="power-bi"></a>Power BI
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| workspaceCollections | Igen | 

## <a name="recovery-services"></a>Recovery Services
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| tárolók | Igen | 
| backupProtectedItems | Nem | 

## <a name="relay"></a>Továbbító
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Névterek | Igen | 

## <a name="resources"></a>További források
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| resourceGroups | Nem | 
| előfizetés/resourceGroups | Nem | 

## <a name="scheduler"></a>Scheduler
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| feladatgyűjtemények | Igen | 
| forgalom | Igen | 

## <a name="search"></a>Keresés
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| searchServices | Igen | 
| resourceHealthMetadata | Nem | 

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

## <a name="sql-virtual-machine"></a>Az SQL virtuális gép
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| DWVM | Igen | 

## <a name="storage"></a>Storage
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| Tárfiókok | Igen | 
| storageAccounts/blobServices | Nem | 
| storageAccounts/tableServices | Nem | 
| storageAccounts/queueServices | Nem | 
| storageAccounts/fileServices | Nem | 
| storageAccounts/services | Nem | 
| storageAccounts/services/metricDefinitions | Nem | 

## <a name="storage-sync"></a>Társzinkronizálási
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| storageSyncServices | Igen | 
| storageSyncServices/syncGroups | Nem | 
| storageSyncServices/syncGroups/cloudEndpoints | Nem | 
| storageSyncServices/syncGroups/serverEndpoints | Nem | 
| storageSyncServices/registeredServers | Nem | 
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
| fiók és a projekt | Igen | 
| fiók/bővítmény | Igen | 
| account | Igen | 
| fiók és a projekt | Igen | 
| fiók/bővítmény | Igen | 

## <a name="web"></a>Web
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| helyek és példányok | Nem | 
| helyek és tárolóhelyek/példány | Nem | 
| webhelyek/példány/bővítmények | Nem | 
| webhelyek/tárolóhelyek/példány/bővítmények | Nem | 
| publishingUsers | Nem | 
| Ellenőrzése | Nem | 
| sourceControls | Nem | 
| webhelyek/hostNameBindings | Nem | 
| webhelyek/domainOwnershipIdentifiers | Nem | 
| helyek és tárolóhelyek/hostNameBindings | Nem | 
| tanúsítványok | Igen | 
| Kiszolgálófarmok | Igen | 
| Kiszolgálófarmok/feldolgozó | Nem | 
| Helyek | Igen | 
| helyek és tárhelyek | Igen | 
| helyek és metrikák | Nem | 
| webhelyek/tárolóhelyek/a mérőszámok | Nem | 
| webhelyek/premieraddons | Igen | 
| hostingEnvironments | Igen | 
| hostingEnvironments/multiRolePools | Nem | 
| hostingEnvironments/workerPools | Nem | 
| hostingEnvironments/a mérőszámok | Nem | 
| functions | Nem | 
| deletedSites | Nem | 
| apiManagementAccounts | Nem | 
| apiManagementAccounts/kapcsolatok | Nem | 
| apiManagementAccounts/connectionAcls | Nem | 
| apiManagementAccounts/API/kapcsolat/connectionAcls | Nem | 
| apiManagementAccounts/API/connectionAcls | Nem | 
| apiManagementAccounts/apiAcls | Nem | 
| apiManagementAccounts/API/apiAcls | Nem | 
| apiManagementAccounts/API-k | Nem | 
| apiManagementAccounts/API/localizedDefinitions | Nem | 
| apiManagementAccounts/API/kapcsolatok | Nem | 
| kapcsolatok | Igen | 
| customApis | Igen | 
| connectionGateways | Igen | 
| billingMeters | Nem | 
| verifyHostingEnvironmentVnet | Nem | 

## <a name="xrm"></a>XRM
| Erőforrás típusa | Támogatja a címkék |
| ------------- | ----------- |
| organizations | Nem | 

## <a name="next-steps"></a>További lépések
Erőforrások címkékkel kezelésével kapcsolatos információkért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md).
