---
title: Az Azure Resource Manager teljes mód törlése erőforrástípus szerint
description: Bemutatja, hogyan kezelik az erőforrástípusok a teljes mód törlése az Azure Resource Manager-sablonok.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: fded37fee844a01f4d51518f2ca56dcf575704b2
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60150149"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Az Azure-erőforrások teljes üzemmód telepítéseit törlését
Ez a cikk bemutatja, hogyan kezelik az erőforrástípusok a törlés nem, amely egy sablont, amely teljes körű módban üzemel.

Az erőforrás típusa jelölése `Yes` törlődnek a típus nem a sablon üzembe helyezésekor teljes móddal. 

Az erőforrás típusa jelölése `No` automatikusan a rendszer nem törli a sablonban; a nem azonban Ön törli a szülőerőforrás törlése. A viselkedésének teljes leírását lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).

Első ugyanazokat az adatokat egy vesszővel tagolt formátumú fájlt, töltse le a [befejezéséhez mód – deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| DomainServices | Igen | 
| DomainServices/oucontainer | Nem | 

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| diagnosticSettings | Nem | 
| diagnosticSettingsCategories | Nem | 

## <a name="microsoftaddons"></a>Microsoft.Addons
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| supportProviders | Nem | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| aadsupportcases | Nem | 
| addsservices | Nem | 
| ügynökök | Nem | 
| anonymousapiusers | Nem | 
| konfiguráció | Nem | 
| naplók | Nem | 
| jelentések | Nem | 
| services | Nem | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Konfigurációk | Nem | 
| generateRecommendations | Nem | 
| javaslatok | Nem | 
| suppressions | Nem | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| actionRules | Nem | 
| riasztások | Nem | 
| alertsList | Nem | 
| alertsSummary | Nem | 
| alertsSummaryList | Nem | 
| smartDetectorAlertRules | Nem | 
| smartDetectorRuntimeEnvironments | Nem | 
| smartGroups | Nem | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| kiszolgáló | Igen | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| reportFeedback | Nem | 
| szolgáltatás | Igen | 
| validateServiceName | Nem | 

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| attestationProviders | Nem | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| classicAdministrators | Nem | 
| denyAssignments | Nem | 
| elevateAccess | Nem | 
| Zárolások | Nem | 
| engedélyek | Nem | 
| policyAssignments | Nem | 
| policyDefinitions | Nem | 
| policySetDefinitions | Nem | 
| providerOperations | Nem | 
| visszaadandó szerepkörkiosztások száma | Nem | 
| roleDefinitions | Nem | 

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| AutomationAccounts | Igen | 
| automationAccounts/configurations | Igen | 
| automationAccounts/feladatok | Nem | 
| automationAccounts/runbooks | Igen | 
| automationAccounts/softwareUpdateConfigurations | Nem | 
| automationAccounts/webhooks | Nem | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Környezetek | Nem | 
| környezetek és fiókok | Nem | 
| környezetek/fiókok/névterek | Nem | 
| környezetek/fiókok/névterek/konfigurációk | Nem | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| b2cDirectories | Igen | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| regisztrációk | Igen | 
| registrations/customerSubscriptions | Nem | 
| regisztrációk/termékek | Nem | 

## <a name="microsoftbatch"></a>Microsoft.Batch
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| batchAccounts | Igen | 

## <a name="microsoftbilling"></a>Microsoft.Billing
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| billingAccounts | Nem | 
| billingAccounts/billingProfiles | Nem | 
| billingAccounts/billingProfiles/billingSubscriptions | Nem | 
| billingAccounts/billingProfiles/invoices | Nem | 
| billingAccounts/billingProfiles/invoices/pricesheet | Nem | 
| billingAccounts/billingProfiles/operationStatus | Nem | 
| billingAccounts/billingProfiles/paymentMethods | Nem | 
| billingAccounts/billingProfiles/policies | Nem | 
| billingAccounts/billingProfiles/pricesheet | Nem | 
| billingAccounts/billingProfiles/products | Nem | 
| billingAccounts/billingProfiles/transactions | Nem | 
| billingAccounts/billingSubscriptions | Nem | 
| billingAccounts/szervezeti egységek | Nem | 
| billingAccounts/eligibleOffers | Nem | 
| billingAccounts/enrollmentAccounts | Nem | 
| billingAccounts/számlák | Nem | 
| billingAccounts/invoiceSections | Nem | 
| billingAccounts/invoiceSections/billingSubscriptions | Nem | 
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nem | 
| billingAccounts/invoiceSections/importRequests | Nem | 
| billingAccounts/invoiceSections/initiateImportRequest | Nem | 
| billingAccounts/invoiceSections/initiateTransfer | Nem | 
| billingAccounts/invoiceSections/operationStatus | Nem | 
| billingAccounts/invoiceSections/products | Nem | 
| billingAccounts/invoiceSections/transfers | Nem | 
| billingAccounts/termékek | Nem | 
| billingAccounts/projektek | Nem | 
| billingAccounts/projects/billingSubscriptions | Nem | 
| billingAccounts/projects/importRequests | Nem | 
| billingAccounts/projects/initiateImportRequest | Nem | 
| billingAccounts/projects/operationStatus | Nem | 
| billingAccounts/projects/products | Nem | 
| billingAccounts/transactions | Nem | 
| billingPeriods | Nem | 
| BillingPermissions | Nem | 
| billingProperty | Nem | 
| BillingRoleAssignments | Nem | 
| BillingRoleDefinitions | Nem | 
| CreateBillingRoleAssignment | Nem | 
| Szervezeti egységek | Nem | 
| enrollmentAccounts | Nem | 
| importRequests | Nem | 
| importRequests/acceptImportRequest | Nem | 
| importRequests/declineImportRequest | Nem | 
| Számlák | Nem | 
| Adatátvitel | Nem | 
| adatátvitel/acceptTransfer | Nem | 
| adatátvitel/declineTransfer | Nem | 
| transfers/operationStatus | Nem | 
| usagePlans | Nem | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| mapApis | Igen | 
| updateCommunicationPreference | Nem | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| BizTalk | Igen | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| blueprintAssignments | Nem | 
| blueprintAssignments/assignmentOperations | Nem | 
| az operations blueprintAssignments | Nem | 
| tervezetek | Nem | 
| tervezetek/összetevők | Nem | 
| tervezetek/verziója | Nem | 
| tervezetek/verziók és összetevők | Nem | 

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| botServices | Igen | 
| botServices/channels | Nem | 
| botServices/connections | Nem | 

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Redis | Igen | 
| RedisConfigDefinition | Nem | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| appliedReservations | Nem | 
| calculatePrice | Nem | 
| katalógusok | Nem | 
| commercialReservationOrders | Nem | 
| reservationOrders | Nem | 
| reservationOrders/calculateRefund | Nem | 
| reservationOrders/egyesítés | Nem | 
| reservationOrders/foglalások | Nem | 
| reservationOrders/foglalások/változatok | Nem | 
| reservationOrders, illetve adhatók vissza | Nem | 
| reservationOrders/felosztása | Nem | 
| reservationOrders/lapozófájl-kapacitás | Nem | 
| foglalások | Nem | 
| erőforrások | Nem | 
| validateReservationOrder | Nem | 

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| edgenodes | Nem | 
| Profilok | Igen | 
| profilok és végpontok | Igen | 
| profiles/endpoints/customdomains | Nem | 
| profilok és végpontok és források | Nem | 
| validateProbe | Nem | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Tanúsítványrendelések | Igen | 
| Tanúsítványrendelések/tanúsítványok | Nem | 
| validateCertificateRegistrationInformation | Nem | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Képességek | Nem | 
| domainNames | Nem | 
| domainNames/capabilities | Nem | 
| domainNames/internalLoadBalancers | Nem | 
| domainNames/serviceCertificates | Nem | 
| domainNames/slots | Nem | 
| domainNames/slots/roles | Nem | 
| moveSubscriptionResources | Nem | 
| operatingSystemFamilies | Nem | 
| operatingSystems | Nem | 
| kvóták | Nem | 
| resourceTypes | Nem | 
| validateSubscriptionMoveAvailability | Nem | 
| virtuális gép | Nem | 
| virtualMachines/diagnosticSettings | Nem | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| classicInfrastructureResources | Nem | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Képességek | Nem | 
| expressRouteCrossConnections | Nem | 
| expressRouteCrossConnections/peerings | Nem | 
| gatewaySupportedDevices | Nem | 
| networkSecurityGroups | Nem | 
| kvóták | Nem | 
| reservedIps | Nem | 
| virtualNetworks | Nem | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem | 
| virtualNetworks/virtualNetworkPeerings | Nem | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Képességek | Nem | 
| lemezek | Nem | 
| images | Nem | 
| osImages | Nem | 
| osPlatformImages | Nem | 
| publicImages | Nem | 
| kvóták | Nem | 
| storageAccounts | Nem | 
| storageAccounts/services | Nem | 
| storageAccounts/services/diagnosticSettings | Nem | 
| storageAccounts/vmImages | Nem | 
| vmImages | Nem | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| RateCard | Nem | 
| UsageAggregates | Nem | 

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| availabilitySets | Igen | 
| lemezek | Igen | 
| images | Igen | 
| restorePointCollections | Igen | 
| restorePointCollections/restorePoints | Nem | 
| sharedVMImages | Igen | 
| sharedVMImages/versions | Igen | 
| pillanatképek | Igen | 
| virtuális gép | Igen | 
| virtualMachines/diagnosticSettings | Nem | 
| virtualMachines/extensions | Igen | 
| virtualMachineScaleSets | Igen | 
| virtualMachineScaleSets/extensions | Nem | 
| virtualMachineScaleSets/networkInterfaces | Nem | 
| virtualMachineScaleSets/publicIPAddresses | Nem | 
| virtualMachineScaleSets/virtualMachines | Nem | 
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| AggregatedCost | Nem | 
| Egyenlegek | Nem | 
| Költségvetések | Nem | 
| Díjak | Nem | 
| CostTags | Nem | 
| Kreditek | Nem | 
| események | Nem | 
| Előrejelzések | Nem | 
| sok | Nem | 
| Piactér | Nem | 
| Pricesheets | Nem | 
| termék | Nem | 
| ReservationDetails | Nem | 
| ReservationRecommendations | Nem | 
| ReservationSummaries | Nem | 
| ReservationTransactions | Nem | 
| Címkék | Nem | 
| Fogalmak | Nem | 
| UsageDetails | Nem | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| containerGroups | Igen | 
| serviceAssociationLinks | Nem | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| beállításjegyzékek | Igen | 
| beállításjegyzékek/buildek | Nem | 
| beállításjegyzékek vagy buildek/törlése | Nem | 
| registries/builds/getLogLink | Nem | 
| registries/buildTasks | Igen | 
| registries/buildTasks/steps | Nem | 
| registries/eventGridFilters | Nem | 
| registries/getBuildSourceUploadUrl | Nem | 
| registries/GetCredentials | Nem | 
| beállításjegyzékek/importImage | Nem | 
| beállításjegyzékek/queueBuild | Nem | 
| registries/regenerateCredential | Nem | 
| registries/regenerateCredentials | Nem | 
| beállításjegyzékek/replikációk | Igen | 
| beállításjegyzékek/futtatások | Nem | 
| beállításjegyzékek vagy futtatása vagy törlése | Nem | 
| registries/scheduleRun | Nem | 
| beállításjegyzékek/feladatok | Igen | 
| registries/updatePolicies | Nem | 
| beállításjegyzékek és webhookok | Igen | 
| registries/webhooks/getCallbackConfig | Nem | 
| beállításjegyzékek/webhookok/ping | Nem | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| containerServices | Igen | 
| managedClusters | Igen | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| alkalmazások | Igen | 
| updateCommunicationPreference | Nem | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Riasztások | Nem | 
| billingAccounts | Nem | 
| Összekötők | Igen | 
| Részlegek | Nem | 
| Dimenziók | Nem | 
| enrollmentAccounts | Nem | 
| Lekérdezés | Nem | 
| Regisztráció | Nem | 
| Reportconfigs | Nem | 
| Jelentések | Nem | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| hubok | Igen | 
| hubok/authorizationPolicies | Nem | 
| hubs vagy összekötőkkel | Nem | 
| hubs/csatlakozók/leképezések | Nem | 
| hubs/interakciók | Nem | 
| hubs/kpi | Nem | 
| hubok és hivatkozások | Nem | 
| hubok és profilok | Nem | 
| hubs/visszaadandó szerepkörkiosztások száma | Nem | 
| hubs/szerepkörök | Nem | 
| hubs/suggestTypeSchema | Nem | 
| hubs/nézetek | Nem | 
| hubok/widgetTypes | Nem | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| feladatok | Igen | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| DataBoxEdgeDevices | Igen | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| munkaterületek | Igen | 
| workspaces/virtualNetworkPeerings | Nem | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| katalógusok | Igen | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| connectionManagers | Igen | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| dataFactories | Igen | 
| dataFactories/diagnosticSettings | Nem | 
| dataFactorySchema | Nem | 
| gyárak | Igen | 
| előállítók/integrationRuntimes | Nem | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 
| accounts/dataLakeStoreAccounts | Nem | 
| accounts/storageAccounts | Nem | 
| accounts/storageAccounts/containers | Nem | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 
| accounts/eventGridFilters | Nem | 
| accounts/firewallRules | Nem | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| services | Igen | 
| Services-projektek | Igen | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| servers/recoverableServers | Nem | 
| servers/virtualNetworkRules | Nem | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| servers/recoverableServers | Nem | 
| servers/virtualNetworkRules | Nem | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| kiszolgáló | Igen | 
| kiszolgálók/tanácsadók | Nem | 
| kiszolgálók/queryTexts | Nem | 
| servers/recoverableServers | Nem | 
| servers/topQueryStatistics | Nem | 
| servers/virtualNetworkRules | Nem | 
| servers/waitStatistics | Nem | 

## <a name="microsoftdevices"></a>Microsoft.Devices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| IotHubs | Igen | 
| IotHubs/eventGridFilters | Nem | 
| ProvisioningServices | Igen | 
| Használati listák | Nem | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Tartományvezérlők | Igen | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Laborgyakorlatok | Igen | 
| labs/serviceRunners | Igen | 
| labs/virtualMachines | Igen | 
| Ütemezések | Igen | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| databaseAccountNames | Nem | 
| databaseAccounts | Igen | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| tartományok | Igen | 
| domains/domainOwnershipIdentifiers | Nem | 
| generateSsoRequest | Nem | 
| topLevelDomains | Nem | 
| validateDomainRegistrationInformation | Nem | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| lcsprojects | Nem | 
| lcsprojects/clouddeployments | Nem | 
| lcsprojects vagy összekötőkkel | Nem | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| tartományok | Igen | 
| tartományok/kapcsolatos témakörök | Nem | 
| eventSubscriptions | Nem | 
| extensionTopics | Nem | 
| kapcsolatos témakörök | Igen | 
| topicTypes | Nem | 

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Fürtök | Igen | 
| névterek | Igen | 
| névtér/szabályok | Nem | 
| namespaces/disasterrecoveryconfigs | Nem | 
| névtér/eventhubs | Nem | 
| névtér/eventhubs/szabályok | Nem | 
| névtér/eventhubs/consumergroups | Nem | 

## <a name="microsoftfeatures"></a>Microsoft.Features
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| funkciókkal | Nem | 
| Szolgáltatók | Nem | 

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Regisztrálása | Nem | 
| galleryitems | Nem | 
| generateartifactaccessuri | Nem | 
| myareas | Nem | 
| myareas/területek | Nem | 
| myareas/területek/területek | Nem | 
| galleryitems myareas/területek/területek | Nem | 
| myareas/területek/galleryitems | Nem | 
| myareas/galleryitems | Nem | 
| Regisztráció | Nem | 
| erőforrások | Nem | 
| retrieveresourcesbyid | Nem | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| guestConfigurationAssignments | Nem | 
| Szoftver | Nem | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| hanaInstances | Igen | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Fürtök | Nem | 
| fürtök és alkalmazások | Nem | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| feladatok | Igen | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| labelGroups | Nem | 
| labelGroups/labels | Nem | 
| labelGroups/labels/conditions | Nem | 
| labelGroups/labels/subLabels | Nem | 
| labelGroups/labels/subLabels/conditions | Nem | 

## <a name="microsoftinsights"></a>microsoft.insights
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| actiongroups | Igen | 
| activityLogAlerts | Igen | 
| alertrules | Igen | 
| automatedExportSettings | Nem | 
| autoscalesettings beállítás | Igen | 
| alapterv | Nem | 
| calculatebaseline | Nem | 
| Összetevők | Igen | 
| összetevő/események | Nem | 
| components/pricingPlans | Nem | 
| összetevő/lekérdezés | Nem | 
| diagnosticSettings | Nem | 
| diagnosticSettingsCategories | Nem | 
| eventCategories | Nem | 
| eventtypes | Nem | 
| extendedDiagnosticSettings | Nem | 
| logDefinitions | Nem | 
| logprofiles | Nem | 
| naplók | Nem | 
| metricAlerts | Igen |
| migrateToNewPricingModel | Nem | 
| myWorkbooks | Nem | 
| lekérdezés | Nem | 
| rollbackToLegacyPricingModel | Nem | 
| scheduledqueryrules | Igen | 
| vmInsightsOnboardingStatuses | Nem | 
| webteszt | Igen | 
| munkafüzetek | Igen | 

## <a name="microsoftintune"></a>Microsoft.Intune
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| diagnosticSettings | Nem | 
| diagnosticSettingsCategories | Nem | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| IoTApps | Igen | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Graph | Igen | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| deletedVaults | Nem | 
| tárolók | Igen | 
| tárolók/accessPolicies | Nem | 
| Tárolók vagy titkos kódokkal | Nem | 

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Fürtök | Igen | 
| fürt/adatbázis | Nem | 
| fürt/adatbázisok/dataconnections | Nem | 
| fürt/adatbázisok/eventhubconnections | Nem | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| labaccounts | Igen | 
| felhasználók | Nem | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| naplók | Nem | 

## <a name="microsoftlogic"></a>Microsoft.Logic
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| integrationAccounts | Igen | 
| A munkafolyamatok | Igen | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| commitmentPlans | Igen | 
| webServices | Igen | 
| Munkaterületek | Igen | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 
| fiókok és munkaterületek | Igen | 
| fiókok/munkaterületeket és projekteket | Igen | 
| teamAccounts | Igen | 
| teamAccounts/munkaterületek | Igen | 
| teamAccounts/workspaces/projects | Igen | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| munkaterületek | Igen | 
| munkaterületek és számítási erőforrások | Nem | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Identitások | Nem | 
| userAssignedIdentities | Igen | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| getEntities | Nem | 
| managementGroups | Nem | 
| erőforrások | Nem | 
| startTenantBackfill | Nem | 
| tenantBackfillStatus | Nem | 

## <a name="microsoftmaps"></a>Microsoft.Maps
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 
| accounts/eventGridFilters | Nem | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Ajánlatok | Nem | 
| offerTypes | Nem | 
| offerTypes/publishers | Nem | 
| offerTypes/publishers/offers | Nem | 
| offerTypes/publishers/offers/plans | Nem | 
| offerTypes/publishers/offers/plans/agreements | Nem | 
| offerTypes/publishers/offers/plans/configs | Nem | 
| offerTypes/publishers/offers/plans/configs/importImage | Nem | 
| privategalleryitems | Nem | 
| termék | Nem | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| classicDevServices | Igen | 
| updateCommunicationPreference | Nem | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Szerződések | Nem | 
| offertypes | Nem | 

## <a name="microsoftmedia"></a>Microsoft.Media
| Erőforrás típusa | Törlés Teljes mód esetén |
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
| mediaservices/streamingEndpointOperations | Nem | 
| mediaservices/Streamvégpontok | Igen | 
| mediaservices/streamingLocators | Nem | 
| mediaservices/streamingPolicies | Nem | 
| mediaservices/átalakítások | Nem | 
| mediaservices/transforms/jobs | Nem | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| projektek | Igen | 

## <a name="microsoftnetwork"></a>Microsoft.Network
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| applicationGateways | Igen | 
| applicationSecurityGroups | Igen | 
| azureFirewallFqdnTags | Nem | 
| azureFirewalls | Igen | 
| bgpServiceCommunities | Nem | 
| kapcsolatok | Igen | 
| ddosCustomPolicies | Igen | 
| ddosProtectionPlans | Igen | 
| dnsOperationStatuses | Nem | 
| dnszones | Igen | 
| dnszones/A | Nem | 
| dnszones/AAAA | Nem | 
| dnszones/all | Nem | 
| dnszones/CAA | Nem | 
| dnszones/CNAME | Nem | 
| dnszones/MX | Nem | 
| dnszones/NS | Nem | 
| dnszones/PTR | Nem | 
| dnszones/recordsets | Nem | 
| dnszones/SOA | Nem | 
| dnszones/SRV | Nem | 
| dnszones/TXT | Nem | 
| expressRouteCircuits | Igen | 
| expressRouteServiceProviders | Nem | 
| frontdoorok | Igen | 
| frontdoorWebApplicationFirewallPolicies | Igen | 
| getDnsResourceReference | Nem | 
| interfaceEndpoints | Igen | 
| internalNotify | Nem | 
| loadBalancers | Igen | 
| localNetworkGateways | Igen | 
| natGateways | Igen | 
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
| trafficManagerGeographicHierarchies | Nem | 
| trafficmanagerprofiles | Igen | 
| trafficmanagerprofiles/heatMaps | Nem | 
| virtualHubs | Igen | 
| virtualNetworkGateways | Igen | 
| virtualNetworks | Igen | 
| virtualNetworkTaps | Igen | 
| virtualWans | Igen | 
| vpnGateways | Igen | 
| vpnSites | Igen | 
| webApplicationFirewallPolicies | Igen | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| névterek | Igen | 
| névtér/notificationHubs | Igen | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| eszközök | Nem | 
| linkTargets | Nem | 
| storageInsightConfigs | Nem | 
| munkaterületek | Igen | 
| workspaces/dataSources | Nem | 
| workspaces/linkedServices | Nem | 
| munkaterületek és lekérdezési | Nem | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| managementassociations | Nem | 
| managementconfigurations | Igen | 
| megoldások | Igen | 
| megtekintés | Igen | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| policyEvents | Nem | 
| policyStates | Nem | 
| policyTrackedResources | Nem | 
| szervizelések | Nem | 

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| a konzolok | Nem | 
| Az irányítópultok | Igen | 
| userSettings | Nem | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| workspaceCollections | Igen | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Kapacitások | Igen | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| fiókok | Igen | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| backupProtectedItems | Nem | 
| tárolók | Igen | 

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| névterek | Igen | 
| névtér/szabályok | Nem | 
| névtér/hybridconnections | Nem | 
| névtér/hybridconnections/szabályok | Nem | 
| névtér/wcfrelays | Nem | 
| névtér/wcfrelays/szabályok | Nem | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| erőforrások | Nem | 
| subscriptionsStatus | Nem | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| availabilityStatuses | Nem | 
| childAvailabilityStatuses | Nem | 
| childResources | Nem | 
| események | Nem | 
| impactedResources | Nem | 
| értesítések | Nem | 

## <a name="microsoftresources"></a>Microsoft.Resources
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| üzemelő példányok | Nem | 
| Az operations központi telepítések | Nem | 
| Hivatkozások | Nem | 
| notifyResourceJobs | Nem | 
| Szolgáltatók | Nem | 
| resourceGroups | Nem | 
| erőforrások | Nem | 
| előfizetés | Nem | 
| Az előfizetések és szolgáltatók | Nem | 
| előfizetés/resourceGroups | Nem | 
| előfizetés/resourcegroups/erőforrások | Nem | 
| Az előfizetések és erőforrások | Nem | 
| előfizetések/tagnames | Nem | 
| Előfizetések/tagNames/tagValues | Nem | 
| Bérlők | Nem | 

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| alkalmazások | Igen | 
| saasresources | Nem | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| forgalom | Igen | 
| feladatgyűjtemények | Igen | 

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| resourceHealthMetadata | Nem | 
| searchServices | Igen | 

## <a name="microsoftsecurity"></a>Microsoft.Security
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| advancedThreatProtectionSettings | Nem | 
| riasztások | Nem | 
| allowedConnections | Nem | 
| készülékek | Nem | 
| applicationWhitelistings | Nem | 
| AutoProvisioningSettings | Nem | 
| Megfelelőségre | Nem | 
| dataCollectionAgents | Nem | 
| discoveredSecuritySolutions | Nem | 
| externalSecuritySolutions | Nem | 
| InformationProtectionPolicies | Nem | 
| jitNetworkAccessPolicies | Nem | 
| figyelés | Nem | 
| figyelés és kártevők elleni | Nem | 
| figyelés és referenciakonfiguráció | Nem | 
| figyelési/patch | Nem | 
| házirendek | Nem | 
| díjcsoportokkal | Nem | 
| securityContacts | Nem | 
| securitySolutions | Nem | 
| securitySolutionsReferenceData | Nem | 
| securityStatus | Nem | 
| securityStatus/endpoints | Nem | 
| securityStatus/subnets | Nem | 
| securityStatus/virtualMachines | Nem | 
| securityStatuses | Nem | 
| securityStatusesSummaries | Nem | 
| beállítások | Nem | 
| feladatok | Nem | 
| Topológiák | Nem | 
| workspaceSettings | Nem | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| diagnosticSettings | Nem | 
| diagnosticSettingsCategories | Nem | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| névterek | Igen | 
| névtér/szabályok | Nem | 
| namespaces/disasterrecoveryconfigs | Nem | 
| névtér/eventgridfilters | Nem | 
| névtér/üzenetsorok | Nem | 
| névtér/üzenetsorok/szabályok | Nem | 
| névtér/kapcsolatos témakörök | Nem | 
| névtér/témakörök/szabályok | Nem | 
| névtér/témakörök/előfizetések | Nem | 
| névtér/témakörök/előfizetések/szabályok | Nem | 
| premiumMessagingRegions | Nem | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Fürtök | Igen | 
| fürtök és alkalmazások | Nem | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| alkalmazások | Igen | 
| Átjárók | Igen | 
| Hálózatok | Igen | 
| titkos kódok | Igen | 
| volumes | Igen | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| SignalR | Igen | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| applianceDefinitions | Igen | 
| készülékek | Igen | 
| applicationDefinitions | Igen | 
| alkalmazások | Igen | 
| jitRequests | Igen | 

## <a name="microsoftsql"></a>Microsoft.SQL
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| managedInstances | Igen |
| managedInstances/databases | Igen (lásd az alábbi megjegyzést) |
| managedInstances/databases/backupShortTermRetentionPolicies | Nem |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nem |
| managedInstances/databases/vulnerabilityAssessments | Nem |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nem |
| managedInstances/encryptionProtector | Nem |
| managedInstances/keys | Nem |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem |
| managedInstances/vulnerabilityAssessments | Nem |
| kiszolgáló | Igen | 
| kiszolgálók és a rendszergazdák | Nem | 
| servers/communicationLinks | Nem | 
| kiszolgálók és adatbázisok | Igen (lásd az alábbi megjegyzést) | 
| servers/encryptionProtector | Nem | 
| servers/firewallRules | Nem | 
| kiszolgálók és kulcs | Nem | 
| servers/restorableDroppedDatabases | Nem | 
| kiszolgálók/serviceobjectives | Nem | 
| servers/tdeCertificates | Nem | 

> [!NOTE]
> A Master adatbázis nem támogatja a címkék, de más adatbázisok, beleértve az adatraktár-adatbázishoz, támogatja a címkék.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| SqlVirtualMachineGroups | Igen | 
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nem | 
| SqlVirtualMachines | Igen | 

## <a name="microsoftstorage"></a>Microsoft.Storage
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| storageAccounts | Igen | 
| storageAccounts/blobServices | Nem | 
| storageAccounts/fileServices | Nem | 
| storageAccounts/queueServices | Nem | 
| storageAccounts/services | Nem | 
| storageAccounts/tableServices | Nem | 
| Használati listák | Nem | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| storageSyncServices | Igen | 
| storageSyncServices/registeredServers | Nem | 
| storageSyncServices/syncGroups | Nem | 
| storageSyncServices/syncGroups/cloudEndpoints | Nem | 
| storageSyncServices/syncGroups/serverEndpoints | Nem | 
| storageSyncServices/munkafolyamatok | Nem | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| vezetők | Igen | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| streamingjobs | Igen (lásd az alábbi megjegyzést) | 
| streamingjobs/diagnosticSettings | Nem | 

> [!NOTE]
> Nem adhat meg egy címkét, streamingjobs futtatásakor. Címke hozzáadásához az erőforrás leállítása.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| CreateSubscription | Nem | 
| SubscriptionDefinitions | Nem | 
| SubscriptionOperations | Nem | 

## <a name="microsoftsupport"></a>microsoft.support
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| supporttickets | Nem | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| providerRegistrations | Igen | 
| erőforrások | Igen | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Környezetek | Igen | 
| környezetek/accessPolicies | Nem | 
| környezetek/eventsources | Igen | 
| environments/referenceDataSets | Igen | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| account | Igen | 
| fiók/bővítmény | Igen | 
| fiók és a projekt | Igen | 

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| apiManagementAccounts | Nem | 
| apiManagementAccounts/apiAcls | Nem | 
| apiManagementAccounts/apis | Nem | 
| apiManagementAccounts/apis/apiAcls | Nem | 
| apiManagementAccounts/apis/connectionAcls | Nem | 
| apiManagementAccounts/apis/connections | Nem | 
| apiManagementAccounts/apis/connections/connectionAcls | Nem | 
| apiManagementAccounts/apis/localizedDefinitions | Nem | 
| apiManagementAccounts/connectionAcls | Nem | 
| apiManagementAccounts/connections | Nem | 
| billingMeters | Nem | 
| tanúsítványok | Igen | 
| connectionGateways | Igen | 
| kapcsolatok | Igen | 
| customApis | Igen | 
| deletedSites | Nem | 
| functions | Nem | 
| hostingEnvironments | Igen | 
| hostingEnvironments/multiRolePools | Nem | 
| hostingEnvironments/multiRolePools/instances | Nem | 
| hostingEnvironments/workerPools | Nem | 
| hostingEnvironments/workerPools/instances | Nem | 
| publishingUsers | Nem | 
| javaslatok | Nem | 
| resourceHealthMetadata | Nem | 
| modulok | Nem | 
| Kiszolgálófarmok | Igen | 
| Kiszolgálófarmok/feldolgozó | Nem | 
| webhelyek | Igen | 
| sites/domainOwnershipIdentifiers | Nem | 
| sites/hostNameBindings | Nem | 
| helyek és példányok | Nem | 
| webhelyek/példány/bővítmények | Nem | 
| webhelyek/premieraddons | Igen | 
| helyek és javaslatok | Nem | 
| sites/resourceHealthMetadata | Nem | 
| helyek és tárhelyek | Igen | 
| sites/slots/hostNameBindings | Nem | 
| helyek és tárolóhelyek/példány | Nem | 
| webhelyek/tárolóhelyek/példány/bővítmények | Nem | 
| sourceControls | Nem | 
| Ellenőrzése | Nem | 
| verifyHostingEnvironmentVnet | Nem | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| diagnosticSettings | Nem | 
| diagnosticSettingsCategories | Nem | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| DeviceServices | Igen | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Erőforrás típusa | Törlés Teljes mód esetén |
| ------------- | ----------- |
| Összetevők | Nem | 
| componentsSummary | Nem | 
| monitorInstances | Nem | 
| monitorInstancesSummary | Nem | 
| Figyelők | Nem | 
| notificationSettings | Nem | 

## <a name="next-steps"></a>További lépések
Erőforrások címkékkel kezelésével kapcsolatos információkért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md).
