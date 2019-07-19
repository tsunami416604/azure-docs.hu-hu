---
title: Azure Resource Manager teljes módú törlés erőforrás-típus szerint
description: Azt mutatja, hogy az erőforrástípusok hogyan kezelik a teljes módú törlést Azure Resource Manager-sablonokban.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302842"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Azure-erőforrások törlése a teljes módú üzembe helyezésekhez
Ez a cikk azt ismerteti, hogyan kezeli az erőforrástípusok a törlést, ha nem a teljes módban telepített sablonban vannak.

A (z) jelölésű `Yes` erőforrástípusok akkor törlődnek, ha a típus nincs a teljes módban üzembe helyezett sablonban. 

A (z) jelölésű `No` erőforrástípusok nem törlődnek automatikusan, ha nem a sablonban, hanem törlődnek, ha a szülő erőforrás törölve lett. A viselkedés teljes leírását lásd: [Azure Resource Manager telepítési módok](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DomainServices | Igen | 
> | DomainServices/oucontainer | Nem | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem | 
> | diagnosticSettingsCategories | Nem | 

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | supportProviders | Nem | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | aadsupportcases | Nem | 
> | addsservices | Nem | 
> | ügynökök | Nem | 
> | anonymousapiusers | Nem | 
> | konfiguráció | Nem | 
> | logs | Nem | 
> | jelentések | Nem | 
> | services | Nem | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | konfigurációk | Nem | 
> | generateRecommendations | Nem | 
> | javaslatok | Nem | 
> | fóliakondenzát | Nem | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | actionRules | Nem | 
> | riasztások | Nem | 
> | alertsList | Nem | 
> | alertsSummary | Nem | 
> | alertsSummaryList | Nem | 
> | smartDetectorAlertRules | Nem | 
> | smartDetectorRuntimeEnvironments | Nem | 
> | smartGroups | Nem | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | reportFeedback | Nem | 
> | szolgáltatás | Igen | 
> | validateServiceName | Nem | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | attestationProviders | Nem | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicAdministrators | Nem | 
> | denyAssignments | Nem | 
> | elevateAccess | Nem | 
> | zárak | Nem | 
> | engedélyek | Nem | 
> | policyAssignments | Nem | 
> | policyDefinitions | Nem | 
> | policySetDefinitions | Nem | 
> | providerOperations | Nem | 
> | roleAssignments | Nem | 
> | roleDefinitions | Nem | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | automationAccounts | Igen | 
> | automationAccounts/konfigurációk | Igen | 
> | automationAccounts/feladatok | Nem | 
> | automationAccounts/runbooks | Igen | 
> | automationAccounts/softwareUpdateConfigurations | Nem | 
> | automationAccounts/webhooks | Nem | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | környezetben | Nem | 
> | környezetek/fiókok | Nem | 
> | környezetek/fiókok/névterek | Nem | 
> | környezetek/fiókok/névterek/konfigurációk | Nem | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | b2cDirectories | Igen | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | regisztrációk | Igen | 
> | registrations/customerSubscriptions | Nem | 
> | regisztrációk/termékek | Nem | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | batchAccounts | Igen | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | billingAccounts | Nem | 
> | billingAccounts/billingProfiles | Nem | 
> | billingAccounts/billingProfiles/billingSubscriptions | Nem | 
> | billingAccounts/billingProfiles/invoices | Nem | 
> | billingAccounts/billingProfiles/invoices/pricesheet | Nem | 
> | billingAccounts/billingProfiles/operationStatus | Nem | 
> | billingAccounts/billingProfiles/paymentMethods | Nem | 
> | billingAccounts/billingProfiles/házirendek | Nem | 
> | billingAccounts/billingProfiles/pricesheet | Nem | 
> | billingAccounts/billingProfiles/products | Nem | 
> | billingAccounts/billingProfiles/transactions | Nem | 
> | billingAccounts/billingSubscriptions | Nem | 
> | billingAccounts/részlegek | Nem | 
> | billingAccounts/eligibleOffers | Nem | 
> | billingAccounts/enrollmentAccounts | Nem | 
> | billingAccounts/számlák | Nem | 
> | billingAccounts/invoiceSections | Nem | 
> | billingAccounts/invoiceSections/billingSubscriptions | Nem | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nem | 
> | billingAccounts/invoiceSections/importRequests | Nem | 
> | billingAccounts/invoiceSections/initiateImportRequest | Nem | 
> | billingAccounts/invoiceSections/initiateTransfer | Nem | 
> | billingAccounts/invoiceSections/operationStatus | Nem | 
> | billingAccounts/invoiceSections/products | Nem | 
> | billingAccounts/invoiceSections/transfers | Nem | 
> | billingAccounts/termékek | Nem | 
> | billingAccounts/projektek | Nem | 
> | billingAccounts/projects/billingSubscriptions | Nem | 
> | billingAccounts/projects/importRequests | Nem | 
> | billingAccounts/projects/initiateImportRequest | Nem | 
> | billingAccounts/projects/operationStatus | Nem | 
> | billingAccounts/projektek/termékek | Nem | 
> | billingAccounts/transactions | Nem | 
> | billingPeriods | Nem | 
> | BillingPermissions | Nem | 
> | billingProperty | Nem | 
> | BillingRoleAssignments | Nem | 
> | BillingRoleDefinitions | Nem | 
> | CreateBillingRoleAssignment | Nem | 
> | részlegek | Nem | 
> | enrollmentAccounts | Nem | 
> | importRequests | Nem | 
> | importRequests/acceptImportRequest | Nem | 
> | importRequests/declineImportRequest | Nem | 
> | számlák | Nem | 
> | transzferek | Nem | 
> | átvitelek/acceptTransfer | Nem | 
> | átvitelek/declineTransfer | Nem | 
> | transfers/operationStatus | Nem | 
> | usagePlans | Nem | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mapApis | Igen | 
> | updateCommunicationPreference | Nem | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | BizTalk | Igen | 

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blueprintAssignments | Nem | 
> | blueprintAssignments/assignmentOperations | Nem | 
> | blueprintAssignments/műveletek | Nem | 
> | tervrajzok | Nem | 
> | tervrajzok/összetevők | Nem | 
> | tervezetek/verziók | Nem | 
> | tervrajzok/verziók/összetevők | Nem | 

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | botServices | Igen | 
> | botServices/csatornák | Nem | 
> | botServices/kapcsolatok | Nem | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Redis | Igen | 
> | RedisConfigDefinition | Nem | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appliedReservations | Nem | 
> | calculatePrice | Nem | 
> | katalógusok | Nem | 
> | commercialReservationOrders | Nem | 
> | reservationOrders | Nem | 
> | reservationOrders/calculateRefund | Nem | 
> | reservationOrders/egyesítés | Nem | 
> | reservationOrders/foglalások | Nem | 
> | reservationOrders/foglalások/változatok | Nem | 
> | reservationOrders/Return | Nem | 
> | reservationOrders/felosztás | Nem | 
> | reservationOrders/swap | Nem | 
> | foglalások | Nem | 
> | Erőforrások | Nem | 
> | validateReservationOrder | Nem | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | edgenodes | Nem | 
> | profiles | Igen | 
> | profilok/végpontok | Igen | 
> | profiles/endpoints/customdomains | Nem | 
> | profilok/végpontok/eredetek | Nem | 
> | validateProbe | Nem | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tanúsítványrendelések | Igen | 
> | Tanúsítványrendelések/tanúsítványok | Nem | 
> | validateCertificateRegistrationInformation | Nem | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | képességek | Nem | 
> | Tartománynevek | Nem | 
> | domainNames/capabilities | Nem | 
> | domainNames/internalLoadBalancers | Nem | 
> | domainNames/serviceCertificates | Nem | 
> | Tartománynevek/tárolóhelyek | Nem | 
> | Tartománynevek/bővítőhelyek/szerepkörök | Nem | 
> | moveSubscriptionResources | Nem | 
> | operatingSystemFamilies | Nem | 
> | operatingSystems | Nem | 
> | kvóták | Nem | 
> | resourceTypes | Nem | 
> | validateSubscriptionMoveAvailability | Nem | 
> | VirtualMachines | Nem | 
> | virtualMachines/diagnosticSettings | Nem | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nem | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | képességek | Nem | 
> | expressRouteCrossConnections | Nem | 
> | expressRouteCrossConnections/peerings | Nem | 
> | gatewaySupportedDevices | Nem | 
> | networkSecurityGroups | Nem | 
> | kvóták | Nem | 
> | reservedIps | Nem | 
> | virtualNetworks | Nem | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem | 
> | virtualNetworks/virtualNetworkPeerings | Nem | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | képességek | Nem | 
> | lemezek | Nem | 
> | images | Nem | 
> | osImages | Nem | 
> | osPlatformImages | Nem | 
> | publicImages | Nem | 
> | kvóták | Nem | 
> | storageAccounts | Nem | 
> | storageAccounts/szolgáltatások | Nem | 
> | storageAccounts/szolgáltatások/diagnosticSettings | Nem | 
> | storageAccounts/vmImages | Nem | 
> | Lemezképet | Nem | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | RateCard | Nem | 
> | UsageAggregates | Nem | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilitySets | Igen | 
> | lemezek | Igen | 
> | images | Igen | 
> | restorePointCollections | Igen | 
> | restorePointCollections/restorePoints | Nem | 
> | sharedVMImages | Igen | 
> | sharedVMImages/verziók | Igen | 
> | Pillanatképek | Igen | 
> | VirtualMachines | Igen | 
> | virtualMachines/diagnosticSettings | Nem | 
> | virtualMachines/bővítmények | Igen | 
> | virtualMachineScaleSets | Igen | 
> | virtualMachineScaleSets/extensions | Nem | 
> | virtualMachineScaleSets/networkInterfaces | Nem | 
> | virtualMachineScaleSets/publicIPAddresses | Nem | 
> | virtualMachineScaleSets/virtualMachines | Nem | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | AggregatedCost | Nem | 
> | Egyenlegek | Nem | 
> | Költségvetések | Nem | 
> | Díjak | Nem | 
> | CostTags | Nem | 
> | hitelek | Nem | 
> | események | Nem | 
> | Előrejelzések | Nem | 
> | számos | Nem | 
> | Piacterek | Nem | 
> | Pricesheets | Nem | 
> | termékek | Nem | 
> | ReservationDetails | Nem | 
> | ReservationRecommendations | Nem | 
> | ReservationSummaries | Nem | 
> | ReservationTransactions | Nem | 
> | Tags | Nem | 
> | Fogalmak | Nem | 
> | UsageDetails | Nem | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerGroups | Igen | 
> | serviceAssociationLinks | Nem | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kibocsátásiegység | Igen | 
> | kibocsátásiegység-forgalmi jegyzékek/buildek | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/buildek/Mégse | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/buildek/getLogLink | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks | Igen | 
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések | Nem | 
> | registries/eventGridFilters | Nem | 
> | registries/getBuildSourceUploadUrl | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/GetCredentials | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/importImage | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/queueBuild | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredential | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredentials | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | 
> | kibocsátásiegység-forgalmi jegyzékek/futtatások | Nem | 
> | nyilvántartások/futtatások/megszakítás | Nem | 
> | registries/scheduleRun | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | 
> | registries/updatePolicies | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | 
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/getCallbackConfig | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/ping | Nem | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerServices | Igen | 
> | managedClusters | Igen | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Igen | 
> | updateCommunicationPreference | Nem | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Riasztások | Nem | 
> | billingAccounts | Nem | 
> | Összekötők | Igen | 
> | részlegek | Nem | 
> | Dimenziók | Nem | 
> | enrollmentAccounts | Nem | 
> | Lekérdezés | Nem | 
> | Regisztráció | Nem | 
> | Reportconfigs | Nem | 
> | Jelentések | Nem | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Hubs | Igen | 
> | hubok/Applyallocationpolicy | Nem | 
> | hubok és összekötők | Nem | 
> | hubok/összekötők/leképezések | Nem | 
> | hubok/interakciók | Nem | 
> | hubs/kpi | Nem | 
> | hubok/hivatkozások | Nem | 
> | hubok/profilok | Nem | 
> | hubok/roleAssignments | Nem | 
> | hubok/szerepkörök | Nem | 
> | hubs/suggestTypeSchema | Nem | 
> | hubok/nézetek | Nem | 
> | hubok/widgetTypes | Nem | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | feladatok | Igen | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Igen | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkaterületek | Igen | 
> | munkaterületek/virtualNetworkPeerings | Nem | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | katalógusok | Igen | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | connectionManagers | Igen | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataFactories | Igen | 
> | dataFactories/diagnosticSettings | Nem | 
> | dataFactorySchema | Nem | 
> | előállítók | Igen | 
> | gyárak/integrationRuntimes | Nem | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 
> | accounts/dataLakeStoreAccounts | Nem | 
> | fiókok/storageAccounts | Nem | 
> | fiókok/storageAccounts/tárolók | Nem | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 
> | fiókok/eventGridFilters | Nem | 
> | fiókok/firewallRules | Nem | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen | 
> | szolgáltatások/projektek | Igen | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen | 
> | kiszolgálók/recoverableServers | Nem | 
> | servers/virtualNetworkRules | Nem | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen | 
> | kiszolgálók/recoverableServers | Nem | 
> | servers/virtualNetworkRules | Nem | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen | 
> | kiszolgálók/tanácsadók | Nem | 
> | kiszolgálók/queryTexts | Nem | 
> | kiszolgálók/recoverableServers | Nem | 
> | kiszolgálók/topQueryStatistics | Nem | 
> | servers/virtualNetworkRules | Nem | 
> | kiszolgálók/waitStatistics | Nem | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | IotHubs | Igen | 
> | IotHubs/eventGridFilters | Nem | 
> | ProvisioningServices | Igen | 
> | használat | Nem | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | tartományvezérlők | Igen | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Labs | Igen | 
> | labs/serviceRunners | Igen | 
> | Labor/virtualMachines | Igen | 
> | menetrend | Igen | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | databaseAccountNames | Nem | 
> | databaseAccounts | Igen | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | tartományok | Igen | 
> | tartományok/domainOwnershipIdentifiers | Nem | 
> | generateSsoRequest | Nem | 
> | topLevelDomains | Nem | 
> | validateDomainRegistrationInformation | Nem | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lcsprojects | Nem | 
> | lcsprojects/clouddeployments | Nem | 
> | lcsprojects/összekötők | Nem | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | tartományok | Igen | 
> | tartományok/témakörök | Nem | 
> | eventSubscriptions | Nem | 
> | extensionTopics | Nem | 
> | témakörök | Igen | 
> | topicTypes | Nem | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen | 
> | névterek | Igen | 
> | névterek/engedélyezési szabályok | Nem | 
> | névterek/disasterrecoveryconfigs | Nem | 
> | névterek/eventhubs | Nem | 
> | névterek/eventhubs/engedélyezési szabályok | Nem | 
> | névterek/eventhubs/consumergroups | Nem | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | funkciókkal | Nem | 
> | Szolgáltatók | Nem | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | beiratkozik | Nem | 
> | galleryitems | Nem | 
> | generateartifactaccessuri | Nem | 
> | myareas | Nem | 
> | myareas/területek | Nem | 
> | myareas/területek/területek | Nem | 
> | myareas/területek/területek/galleryitems | Nem | 
> | myareas/területek/galleryitems | Nem | 
> | myareas/galleryitems | Nem | 
> | Regisztráció | Nem | 
> | Erőforrások | Nem | 
> | retrieveresourcesbyid | Nem | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Nem | 
> | Szoftver | Nem | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hanaInstances | Igen | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen | 
> | fürtök/alkalmazások | Nem | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | feladatok | Igen | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labelGroups | Nem | 
> | labelGroups/Címkék | Nem | 
> | labelGroups/címkék/feltételek | Nem | 
> | labelGroups/labels/subLabels | Nem | 
> | labelGroups/labels/subLabels/conditions | Nem | 

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | actiongroups | Igen | 
> | activityLogAlerts | Igen | 
> | alertrules | Igen | 
> | automatedExportSettings | Nem | 
> | autoscalesettings | Igen | 
> | alapkonfiguráció | Nem | 
> | calculatebaseline | Nem | 
> | Összetevők | Igen | 
> | összetevők/események | Nem | 
> | components/pricingPlans | Nem | 
> | összetevők/lekérdezés | Nem | 
> | diagnosticSettings | Nem | 
> | diagnosticSettingsCategories | Nem | 
> | eventCategories | Nem | 
> | eventtypes | Nem | 
> | extendedDiagnosticSettings | Nem | 
> | logDefinitions | Nem | 
> | logprofiles | Nem | 
> | logs | Nem | 
> | metricAlerts | Igen |
> | migrateToNewPricingModel | Nem | 
> | myWorkbooks | Nem | 
> | lekérdezés | Nem | 
> | rollbackToLegacyPricingModel | Nem | 
> | scheduledqueryrules | Igen | 
> | vmInsightsOnboardingStatuses | Nem | 
> | webteszteket | Igen | 
> | Munkafüzetek | Igen | 

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem | 
> | diagnosticSettingsCategories | Nem | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | IoTApps | Igen | 

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Graph | Igen | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deletedVaults | Nem | 
> | boltívek | Igen | 
> | tárolók/accessPolicies | Nem | 
> | tárolók/titkok | Nem | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen | 
> | fürtök/adatbázisok | Nem | 
> | fürtök/adatbázisok/dataconnections | Nem | 
> | fürtök/adatbázisok/eventhubconnections | Nem | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labaccounts | Igen | 
> | felhasználók | Nem | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | logs | Nem | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | integrationAccounts | Igen | 
> | munkafolyamatok | Igen | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | commitmentPlans | Igen | 
> | webServices | Igen | 
> | Munkaterületek | Igen | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 
> | fiókok/munkaterületek | Igen | 
> | fiókok/munkaterületek/projektek | Igen | 
> | teamAccounts | Igen | 
> | teamAccounts/munkaterületek | Igen | 
> | teamAccounts/munkaterületek/projektek | Igen | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkaterületek | Igen | 
> | munkaterületek/számítások | Nem | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Identitások | Nem | 
> | userAssignedIdentities | Igen | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | getEntities | Nem | 
> | managementGroups | Nem | 
> | Erőforrások | Nem | 
> | startTenantBackfill | Nem | 
> | tenantBackfillStatus | Nem | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 
> | fiókok/eventGridFilters | Nem | 

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kínál | Nem | 
> | offerTypes | Nem | 
> | offerTypes/közzétevők | Nem | 
> | offerTypes/kiadók/ajánlatok | Nem | 
> | offerTypes/kiadók/ajánlatok/csomagok | Nem | 
> | offerTypes/kiadók/ajánlatok/csomagok/szerződések | Nem | 
> | offerTypes/kiadók/ajánlatok/csomagok/konfigurációk | Nem | 
> | offerTypes/publishers/offers/plans/configs/importImage | Nem | 
> | privategalleryitems | Nem | 
> | termékek | Nem | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicDevServices | Igen | 
> | updateCommunicationPreference | Nem | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | megállapodások | Nem | 
> | offertypes | Nem | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Mediaservices | Igen | 
> | Mediaservices/accountFilters | Nem | 
> | Mediaservices/-eszközök | Nem | 
> | Mediaservices/eszközök/assetFilters | Nem | 
> | mediaservices/contentKeyPolicies | Nem | 
> | Mediaservices/eventGridFilters | Nem | 
> | Mediaservices/liveEventOperations | Nem | 
> | Mediaservices/liveEvents | Igen | 
> | Mediaservices/liveEvents/liveOutputs | Nem | 
> | Mediaservices/liveOutputOperations | Nem | 
> | Mediaservices/streamingEndpointOperations | Nem | 
> | Mediaservices/streamingEndpoints | Igen | 
> | Mediaservices/streamingLocators | Nem | 
> | Mediaservices/streamingPolicies | Nem | 
> | Mediaservices/átalakítások | Nem | 
> | Mediaservices/átalakítások/feladatok | Nem | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | projektek | Igen | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationGateways | Igen | 
> | applicationSecurityGroups | Igen | 
> | azureFirewallFqdnTags | Nem | 
> | azureFirewalls | Igen | 
> | bgpServiceCommunities | Nem | 
> | kapcsolatok | Igen | 
> | ddosCustomPolicies | Igen | 
> | ddosProtectionPlans | Igen | 
> | dnsOperationStatuses | Nem | 
> | dnszones | Igen | 
> | dnszones/A | Nem | 
> | dnszones/AAAA | Nem | 
> | dnszones/mind | Nem | 
> | dnszones/CAA | Nem | 
> | dnszones/CNAME | Nem | 
> | dnszones/MX | Nem | 
> | dnszones/NS | Nem | 
> | dnszones/PTR | Nem | 
> | dnszones/recordsets | Nem | 
> | dnszones/SOA | Nem | 
> | dnszones/SRV | Nem | 
> | dnszones/TXT | Nem | 
> | expressRouteCircuits | Igen | 
> | expressRouteServiceProviders | Nem | 
> | frontdoors | Igen | 
> | frontdoorWebApplicationFirewallPolicies | Igen | 
> | getDnsResourceReference | Nem | 
> | interfaceEndpoints | Igen | 
> | internalNotify | Nem | 
> | loadBalancers | Igen | 
> | localNetworkGateways | Igen | 
> | natGateways | Igen | 
> | networkIntentPolicies | Igen | 
> | networkInterfaces | Igen | 
> | networkProfiles | Igen | 
> | networkSecurityGroups | Igen | 
> | networkWatchers | Igen | 
> | networkWatchers/connectionMonitors | Igen | 
> | networkWatchers/objektívek | Igen | 
> | networkWatchers/pingMeshes | Igen | 
> | privateLinkServices | Igen | 
> | publicIPAddresses | Igen | 
> | publicIPPrefixes | Igen | 
> | routeFilters | Igen | 
> | routeTables | Igen | 
> | serviceEndpointPolicies | Igen | 
> | trafficManagerGeographicHierarchies | Nem | 
> | trafficmanagerprofiles | Igen | 
> | trafficmanagerprofiles/heatMaps | Nem | 
> | virtualHubs | Igen | 
> | virtualNetworkGateways | Igen | 
> | virtualNetworks | Igen | 
> | virtualNetworkTaps | Igen | 
> | virtualWans | Igen | 
> | vpnGateways | Igen | 
> | vpnSites | Igen | 
> | webApplicationFirewallPolicies | Igen | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | névterek | Igen | 
> | névterek/notificationHubs | Igen | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | eszközök | Nem | 
> | linkTargets | Nem | 
> | storageInsightConfigs | Nem | 
> | Munkaterületek | Igen | 
> | munkaterületek/adatforrások | Nem | 
> | workspaces/linkedServices | Nem | 
> | munkaterületek/lekérdezés | Nem | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managementassociations | Nem | 
> | managementconfigurations | Igen | 
> | megoldások | Igen | 
> | Kilátással | Igen | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | policyEvents | Nem | 
> | policyStates | Nem | 
> | policyTrackedResources | Nem | 
> | szervizelések | Nem | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | konzolok | Nem | 
> | Irányítópultok | Igen | 
> | userSettings | Nem | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | workspaceCollections | Igen | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kapacitások | Igen | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | backupProtectedItems | Nem | 
> | boltívek | Igen | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | névterek | Igen | 
> | névterek/engedélyezési szabályok | Nem | 
> | névterek/hybridconnections | Nem | 
> | névterek/hybridconnections/engedélyezési szabályok | Nem | 
> | névterek/wcfrelays | Nem | 
> | névterek/wcfrelays/engedélyezési szabályok | Nem | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Erőforrások | Nem | 
> | subscriptionsStatus | Nem | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilityStatuses | Nem | 
> | childAvailabilityStatuses | Nem | 
> | childResources | Nem | 
> | események | Nem | 
> | impactedResources | Nem | 
> | Értesítések | Nem | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | központi telepítések | Nem | 
> | üzembe helyezések/műveletek | Nem | 
> | Linkek | Nem | 
> | notifyResourceJobs | Nem | 
> | Szolgáltatók | Nem | 
> | resourceGroups | Nem | 
> | Erőforrások | Nem | 
> | előfizetések | Nem | 
> | előfizetések/szolgáltatók | Nem | 
> | előfizetések/resourceGroups | Nem | 
> | előfizetések/resourcegroups/erőforrások | Nem | 
> | előfizetések/erőforrások | Nem | 
> | előfizetések/tagNames | Nem | 
> | előfizetések/tagNames/tagValues | Nem | 
> | bérlők | Nem | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Igen | 
> | saasresources | Nem | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | flow | Igen | 
> | feladatgyűjtemények | Igen | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nem | 
> | searchServices | Igen | 

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | advancedThreatProtectionSettings | Nem | 
> | riasztások | Nem | 
> | allowedConnections | Nem | 
> | berendezések | Nem | 
> | applicationWhitelistings | Nem | 
> | AutoProvisioningSettings | Nem | 
> | Felelésről | Nem | 
> | dataCollectionAgents | Nem | 
> | discoveredSecuritySolutions | Nem | 
> | externalSecuritySolutions | Nem | 
> | InformationProtectionPolicies | Nem | 
> | jitNetworkAccessPolicies | Nem | 
> | Figyelés | Nem | 
> | figyelés/kártevő szoftverek | Nem | 
> | figyelés/alapterv | Nem | 
> | figyelés/javítás | Nem | 
> | szabályzatok | Nem | 
> | pricings | Nem | 
> | securityContacts | Nem | 
> | securitySolutions | Nem | 
> | securitySolutionsReferenceData | Nem | 
> | securityStatus | Nem | 
> | securityStatus/végpontok | Nem | 
> | securityStatus/alhálózatok | Nem | 
> | securityStatus/virtualMachines | Nem | 
> | securityStatuses | Nem | 
> | securityStatusesSummaries | Nem | 
> | beállítások | Nem | 
> | feladatok | Nem | 
> | topológiák | Nem | 
> | workspaceSettings | Nem | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem | 
> | diagnosticSettingsCategories | Nem | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | névterek | Igen | 
> | névterek/engedélyezési szabályok | Nem | 
> | névterek/disasterrecoveryconfigs | Nem | 
> | névterek/eventgridfilters | Nem | 
> | névterek/várólisták | Nem | 
> | névterek/várólisták/engedélyezési szabályok | Nem | 
> | névterek/témakörök | Nem | 
> | névterek/témakörök/engedélyezési szabályok | Nem | 
> | névterek/témakörök/előfizetések | Nem | 
> | névterek/témakörök/előfizetések/szabályok | Nem | 
> | premiumMessagingRegions | Nem | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen | 
> | fürtök/alkalmazások | Nem | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Igen | 
> | átjárók | Igen | 
> | hálózatok | Igen | 
> | titkok | Igen | 
> | volumes | Igen | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SignalR | Igen | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applianceDefinitions | Igen | 
> | berendezések | Igen | 
> | applicationDefinitions | Igen | 
> | alkalmazások | Igen | 
> | jitRequests | Igen | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managedInstances | Igen |
> | managedInstances/adatbázisok | Igen |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nem |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nem |
> | managedInstances/databases/vulnerabilityAssessments | Nem |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nem |
> | managedInstances/encryptionProtector | Nem |
> | managedInstances/keys | Nem |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem |
> | managedInstances/vulnerabilityAssessments | Nem |
> | Kiszolgálók | Igen | 
> | kiszolgálók/rendszergazdák | Nem | 
> | kiszolgálók/communicationLinks | Nem | 
> | kiszolgálók/adatbázisok | Igen | 
> | servers/encryptionProtector | Nem | 
> | kiszolgálók/firewallRules | Nem | 
> | kiszolgálók/kulcsok | Nem | 
> | servers/restorableDroppedDatabases | Nem | 
> | kiszolgálók/serviceobjectives | Nem | 
> | kiszolgálók/tdeCertificates | Nem | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Igen | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nem | 
> | SqlVirtualMachines | Igen | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageAccounts | Igen | 
> | storageAccounts/blobServices | Nem | 
> | storageAccounts/fileServices | Nem | 
> | storageAccounts/queueServices | Nem | 
> | storageAccounts/szolgáltatások | Nem | 
> | storageAccounts/tableServices | Nem | 
> | használat | Nem | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen | 
> | storageSyncServices/registeredServers | Nem | 
> | storageSyncServices/syncGroups | Nem | 
> | storageSyncServices/syncGroups/cloudEndpoints | Nem | 
> | storageSyncServices/syncGroups/serverEndpoints | Nem | 
> | storageSyncServices/munkafolyamatok | Nem | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kezelők | Igen | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | streamingjobs | Igen | 
> | streamingjobs/diagnosticSettings | Nem | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | CreateSubscription | Nem | 
> | SubscriptionDefinitions | Nem | 
> | SubscriptionOperations | Nem | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | supporttickets | Nem | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | providerRegistrations | Igen | 
> | Erőforrások | Igen | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | környezetben | Igen | 
> | környezetek/accessPolicies | Nem | 
> | környezetek/eventsources | Igen | 
> | környezetek/referenceDataSets | Igen | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | account | Igen | 
> | fiók/bővítmény | Igen | 
> | fiók/projekt | Igen | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | apiManagementAccounts | Nem | 
> | apiManagementAccounts/apiAcls | Nem | 
> | apiManagementAccounts/apis | Nem | 
> | apiManagementAccounts/apis/apiAcls | Nem | 
> | apiManagementAccounts/apis/connectionAcls | Nem | 
> | apiManagementAccounts/apis/connections | Nem | 
> | apiManagementAccounts/apis/connections/connectionAcls | Nem | 
> | apiManagementAccounts/apis/localizedDefinitions | Nem | 
> | apiManagementAccounts/connectionAcls | Nem | 
> | apiManagementAccounts/connections | Nem | 
> | billingMeters | Nem | 
> | Tanúsítványok | Igen | 
> | connectionGateways | Igen | 
> | kapcsolatok | Igen | 
> | customApis | Igen | 
> | deletedSites | Nem | 
> | functions | Nem | 
> | hostingEnvironments | Igen | 
> | hostingEnvironments/multiRolePools | Nem | 
> | hostingEnvironments/multiRolePools/instances | Nem | 
> | hostingEnvironments/workerPools | Nem | 
> | hostingEnvironments/workerPools/példányok | Nem | 
> | publishingUsers | Nem | 
> | javaslatok | Nem | 
> | resourceHealthMetadata | Nem | 
> | Runtimes | Nem | 
> | Kiszolgálófarmok | Igen | 
> | Kiszolgálófarmok/feldolgozók | Nem | 
> | helyek | Igen | 
> | helyek/domainOwnershipIdentifiers | Nem | 
> | sites/hostNameBindings | Nem | 
> | helyek/példányok | Nem | 
> | helyek/példányok/bővítmények | Nem | 
> | helyek/premieraddons | Igen | 
> | helyek/javaslatok | Nem | 
> | sites/resourceHealthMetadata | Nem | 
> | helyek/bővítőhelyek | Igen | 
> | sites/slots/hostNameBindings | Nem | 
> | helyek/bővítőhelyek/példányok | Nem | 
> | helyek/bővítőhelyek/példányok/bővítmények | Nem | 
> | sourceControls | Nem | 
> | érvényesít | Nem | 
> | verifyHostingEnvironmentVnet | Nem | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem | 
> | diagnosticSettingsCategories | Nem | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DeviceServices | Igen | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összetevők | Nem | 
> | componentsSummary | Nem | 
> | monitorInstances | Nem | 
> | monitorInstancesSummary | Nem | 
> | figyeli | Nem | 
> | notificationSettings | Nem | 

## <a name="next-steps"></a>További lépések

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [Complete-Mode-Deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)fájlt.