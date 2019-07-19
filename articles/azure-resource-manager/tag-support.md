---
title: Az erőforrások támogatásának Azure Resource Manager címkézése
description: Megjeleníti, hogy mely Azure-erőforrástípusok támogatják a címkéket. Az összes Azure-szolgáltatás részleteit tartalmazza.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304869"
---
# <a name="tag-support-for-azure-resources"></a>Azure-erőforrások támogatásának címkézése
Ez a cikk azt mutatja be, hogy az erőforrástípus támogatja-e a [címkéket](resource-group-using-tags.md). A címkével ellátott oszlopban szereplő **címke jelzi,** hogy az erőforrástípus rendelkezik-e tulajdonsággal a címkéhez. A **címke a Cost jelentésben** feliratú oszlop jelzi, hogy az erőforrástípus átadja-e a címkét a Cost jelentésnek.

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)fájlt.

## <a name="microsoftaad"></a>Microsoft.AAD
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| DomainServices | Igen | Igen |
| DomainServices/oucontainer | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| supportProviders | Nem |  Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nem |  Nem |
| addsservices | Nem |  Nem |
| ügynökök | Nem |  Nem |
| anonymousapiusers | Nem |  Nem |
| konfiguráció | Nem |  Nem |
| logs | Nem |  Nem |
| jelentések | Nem |  Nem |
| services | Nem |  Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| konfigurációk | Nem |  Nem |
| generateRecommendations | Nem |  Nem |
| javaslatok | Nem |  Nem |
| fóliakondenzát | Nem |  Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| actionRules | Nem |  Nem |
| riasztások | Nem |  Nem |
| alertsList | Nem |  Nem |
| alertsSummary | Nem |  Nem |
| alertsSummaryList | Nem |  Nem |
| smartDetectorAlertRules | Nem |  Nem |
| smartDetectorRuntimeEnvironments | Nem |  Nem |
| smartGroups | Nem |  Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| reportFeedback | Nem |  Nem |
| szolgáltatás | Igen | Igen |
| validateServiceName | Nem |  Nem |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| attestationProviders | Nem |  Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nem |  Nem |
| denyAssignments | Nem |  Nem |
| elevateAccess | Nem |  Nem |
| zárak | Nem |  Nem |
| engedélyek | Nem |  Nem |
| policyAssignments | Nem |  Nem |
| policyDefinitions | Nem |  Nem |
| policySetDefinitions | Nem |  Nem |
| providerOperations | Nem |  Nem |
| roleAssignments | Nem |  Nem |
| roleDefinitions | Nem |  Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| automationAccounts | Igen | Igen |
| automationAccounts/konfigurációk | Igen | Igen |
| automationAccounts/feladatok | Nem |  Nem |
| automationAccounts/runbooks | Igen | Igen |
| automationAccounts/softwareUpdateConfigurations | Nem | Nem |
| automationAccounts/webhooks | Nem |  Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| környezetben | Nem |  Nem |
| környezetek/fiókok | Nem |  Nem |
| környezetek/fiókok/névterek | Nem |  Nem |
| környezetek/fiókok/névterek/konfigurációk | Nem |  Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| b2cDirectories | Igen | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| regisztrációk | Igen | Igen |
| registrations/customerSubscriptions | Nem |  Nem |
| regisztrációk/termékek | Nem |  Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| batchAccounts | Igen | Igen |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| billingAccounts | Nem |  Nem |
| billingAccounts/billingProfiles | Nem |  Nem |
| billingAccounts/billingProfiles/billingSubscriptions | Nem |  Nem |
| billingAccounts/billingProfiles/invoices | Nem |  Nem |
| billingAccounts/billingProfiles/invoices/pricesheet | Nem |  Nem |
| billingAccounts/billingProfiles/operationStatus | Nem |  Nem |
| billingAccounts/billingProfiles/paymentMethods | Nem |  Nem |
| billingAccounts/billingProfiles/házirendek | Nem |  Nem |
| billingAccounts/billingProfiles/pricesheet | Nem |  Nem |
| billingAccounts/billingProfiles/products | Nem |  Nem |
| billingAccounts/billingProfiles/transactions | Nem |  Nem |
| billingAccounts/billingSubscriptions | Nem |  Nem |
| billingAccounts/részlegek | Nem |  Nem |
| billingAccounts/eligibleOffers | Nem |  Nem |
| billingAccounts/enrollmentAccounts | Nem |  Nem |
| billingAccounts/számlák | Nem |  Nem |
| billingAccounts/invoiceSections | Nem |  Nem |
| billingAccounts/invoiceSections/billingSubscriptions | Nem |  Nem |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Nem |  Nem |
| billingAccounts/invoiceSections/importRequests | Nem |  Nem |
| billingAccounts/invoiceSections/initiateImportRequest | Nem |  Nem |
| billingAccounts/invoiceSections/initiateTransfer | Nem |  Nem |
| billingAccounts/invoiceSections/operationStatus | Nem |  Nem |
| billingAccounts/invoiceSections/products | Nem |  Nem |
| billingAccounts/invoiceSections/transfers | Nem |  Nem |
| billingAccounts/termékek | Nem |  Nem |
| billingAccounts/projektek | Nem |  Nem |
| billingAccounts/projects/billingSubscriptions | Nem |  Nem |
| billingAccounts/projects/importRequests | Nem |  Nem |
| billingAccounts/projects/initiateImportRequest | Nem |  Nem |
| billingAccounts/projects/operationStatus | Nem |  Nem |
| billingAccounts/projektek/termékek | Nem |  Nem |
| billingAccounts/transactions | Nem |  Nem |
| billingPeriods | Nem |  Nem |
| BillingPermissions | Nem |  Nem |
| billingProperty | Nem |  Nem |
| BillingRoleAssignments | Nem |  Nem |
| BillingRoleDefinitions | Nem |  Nem |
| CreateBillingRoleAssignment | Nem |  Nem |
| részlegek | Nem |  Nem |
| enrollmentAccounts | Nem |  Nem |
| importRequests | Nem |  Nem |
| importRequests/acceptImportRequest | Nem |  Nem |
| importRequests/declineImportRequest | Nem |  Nem |
| számlák | Nem |  Nem |
| transzferek | Nem |  Nem |
| átvitelek/acceptTransfer | Nem |  Nem |
| átvitelek/declineTransfer | Nem |  Nem |
| transfers/operationStatus | Nem |  Nem |
| usagePlans | Nem |  Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| mapApis | Igen | Igen |
| updateCommunicationPreference | Nem |  Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| BizTalk | Igen | Igen |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nem |  Nem |
| blueprintAssignments/assignmentOperations | Nem |  Nem |
| blueprintAssignments/műveletek | Nem |  Nem |
| tervrajzok | Nem |  Nem |
| tervrajzok/összetevők | Nem |  Nem |
| tervezetek/verziók | Nem |  Nem |
| tervrajzok/verziók/összetevők | Nem |  Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| botServices | Igen | Igen |
| botServices/csatornák | Nem |  Nem |
| botServices/kapcsolatok | Nem |  Nem |

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Redis | Igen | Igen |
| RedisConfigDefinition | Nem |  Nem |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| appliedReservations | Nem |  Nem |
| calculatePrice | Nem |  Nem |
| katalógusok | Nem |  Nem |
| commercialReservationOrders | Nem |  Nem |
| reservationOrders | Nem |  Nem |
| reservationOrders/calculateRefund | Nem |  Nem |
| reservationOrders/egyesítés | Nem |  Nem |
| reservationOrders/foglalások | Nem |  Nem |
| reservationOrders/foglalások/változatok | Nem |  Nem |
| reservationOrders/Return | Nem |  Nem |
| reservationOrders/felosztás | Nem |  Nem |
| reservationOrders/swap | Nem |  Nem |
| foglalások | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| validateReservationOrder | Nem |  Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| edgenodes | Nem |  Nem |
| profiles | Igen | Igen |
| profilok/végpontok | Igen | Igen |
| profiles/endpoints/customdomains | Nem |  Nem |
| profilok/végpontok/eredetek | Nem |  Nem |
| validateProbe | Nem |  Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Tanúsítványrendelések | Igen | Igen |
| Tanúsítványrendelések/tanúsítványok | Nem |  Nem |
| validateCertificateRegistrationInformation | Nem |  Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| képességek | Nem |  Nem |
| Tartománynevek | Nem |  Nem |
| domainNames/capabilities | Nem |  Nem |
| domainNames/internalLoadBalancers | Nem |  Nem |
| domainNames/serviceCertificates | Nem |  Nem |
| Tartománynevek/tárolóhelyek | Nem |  Nem |
| Tartománynevek/bővítőhelyek/szerepkörök | Nem |  Nem |
| moveSubscriptionResources | Nem |  Nem |
| operatingSystemFamilies | Nem |  Nem |
| operatingSystems | Nem |  Nem |
| kvóták | Nem |  Nem |
| resourceTypes | Nem |  Nem |
| validateSubscriptionMoveAvailability | Nem |  Nem |
| VirtualMachines | Nem |  Nem |
| virtualMachines/diagnosticSettings | Nem |  Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nem |  Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| képességek | Nem |  Nem |
| expressRouteCrossConnections | Nem |  Nem |
| expressRouteCrossConnections/peerings | Nem |  Nem |
| gatewaySupportedDevices | Nem |  Nem |
| networkSecurityGroups | Nem |  Nem |
| kvóták | Nem |  Nem |
| reservedIps | Nem |  Nem |
| virtualNetworks | Nem |  Nem |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem |  Nem |
| virtualNetworks/virtualNetworkPeerings | Nem |  Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| képességek | Nem |  Nem |
| lemezek | Nem |  Nem |
| images | Nem |  Nem |
| osImages | Nem |  Nem |
| osPlatformImages | Nem |  Nem |
| publicImages | Nem |  Nem |
| kvóták | Nem |  Nem |
| storageAccounts | Nem |  Nem |
| storageAccounts/szolgáltatások | Nem |  Nem |
| storageAccounts/szolgáltatások/diagnosticSettings | Nem |  Nem |
| storageAccounts/vmImages | Nem |  Nem |
| Lemezképet | Nem |  Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| RateCard | Nem |  Nem |
| UsageAggregates | Nem |  Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| availabilitySets | Igen | Igen |
| lemezek | Igen | Igen |
| images | Igen | Igen |
| restorePointCollections | Igen | Igen |
| restorePointCollections/restorePoints | Nem |  Nem |
| sharedVMImages | Igen | Igen |
| sharedVMImages/verziók | Igen | Igen |
| Pillanatképek | Igen | Igen |
| VirtualMachines | Igen | Igen |
| virtualMachines/diagnosticSettings | Nem |  Nem |
| virtualMachines/bővítmények | Igen | Igen |
| virtualMachineScaleSets | Igen | Igen |
| virtualMachineScaleSets/extensions | Nem |  Nem |
| virtualMachineScaleSets/networkInterfaces | Nem |  Nem |
| virtualMachineScaleSets/publicIPAddresses | Nem |  Nem |
| virtualMachineScaleSets/virtualMachines | Nem |  Nem |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem |  Nem |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nem |  Nem |
| Egyenlegek | Nem |  Nem |
| Költségvetések | Nem |  Nem |
| Díjak | Nem |  Nem |
| CostTags | Nem |  Nem |
| hitelek | Nem |  Nem |
| események | Nem |  Nem |
| Előrejelzések | Nem |  Nem |
| számos | Nem |  Nem |
| Piacterek | Nem |  Nem |
| Pricesheets | Nem |  Nem |
| termékek | Nem |  Nem |
| ReservationDetails | Nem |  Nem |
| ReservationRecommendations | Nem |  Nem |
| ReservationSummaries | Nem |  Nem |
| ReservationTransactions | Nem |  Nem |
| Tags | Nem |  Nem |
| Fogalmak | Nem |  Nem |
| UsageDetails | Nem |  Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| containerGroups | Igen | Igen |
| serviceAssociationLinks | Nem |  Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| kibocsátásiegység | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/buildek | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/buildek/Mégse | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/buildek/getLogLink | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/buildTasks | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések | Nem |  Nem |
| registries/eventGridFilters | Nem |  Nem |
| registries/getBuildSourceUploadUrl | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/GetCredentials | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/importImage | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/queueBuild | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/regenerateCredential | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/regenerateCredentials | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/futtatások | Nem |  Nem |
| nyilvántartások/futtatások/megszakítás | Nem |  Nem |
| registries/scheduleRun | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Igen |
| registries/updatePolicies | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/webhookok/getCallbackConfig | Nem |  Nem |
| kibocsátásiegység-forgalmi jegyzékek/webhookok/ping | Nem |  Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| containerServices | Igen | Igen |
| managedClusters | Igen | Igen |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| alkalmazások | Igen | Igen |
| updateCommunicationPreference | Nem |  Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Riasztások | Nem |  Nem |
| billingAccounts | Nem |  Nem |
| Összekötők | Igen | Igen |
| részlegek | Nem |  Nem |
| Dimenziók | Nem |  Nem |
| enrollmentAccounts | Nem |  Nem |
| Lekérdezés | Nem |  Nem |
| Regisztráció | Nem |  Nem |
| Reportconfigs | Nem |  Nem |
| Jelentések | Nem |  Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Hubs | Igen | Igen |
| hubok/Applyallocationpolicy | Nem |  Nem |
| hubok és összekötők | Nem |  Nem |
| hubok/összekötők/leképezések | Nem |  Nem |
| hubok/interakciók | Nem |  Nem |
| hubs/kpi | Nem |  Nem |
| hubok/hivatkozások | Nem |  Nem |
| hubok/profilok | Nem |  Nem |
| hubok/roleAssignments | Nem |  Nem |
| hubok/szerepkörök | Nem |  Nem |
| hubs/suggestTypeSchema | Nem |  Nem |
| hubok/nézetek | Nem |  Nem |
| hubok/widgetTypes | Nem |  Nem |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| feladatok | Igen | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Igen | Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Munkaterületek | Igen | Nem |
| munkaterületek/virtualNetworkPeerings | Nem |  Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| katalógusok | Igen | Igen |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| connectionManagers | Igen | Igen |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| dataFactories | Igen | Nem |
| dataFactories/diagnosticSettings | Nem |  Nem |
| dataFactorySchema | Nem |  Nem |
| előállítók | Igen | Nem |
| gyárak/integrationRuntimes | Nem |  Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| accounts/dataLakeStoreAccounts | Nem |  Nem |
| fiókok/storageAccounts | Nem |  Nem |
| fiókok/storageAccounts/tárolók | Nem |  Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| fiókok/eventGridFilters | Nem |  Nem |
| fiókok/firewallRules | Nem |  Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| services | Igen | Igen |
| szolgáltatások/projektek | Igen | Igen |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |
| kiszolgálók/recoverableServers | Nem |  Nem |
| servers/virtualNetworkRules | Nem |  Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |
| kiszolgálók/recoverableServers | Nem |  Nem |
| servers/virtualNetworkRules | Nem |  Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |
| kiszolgálók/tanácsadók | Nem |  Nem |
| kiszolgálók/queryTexts | Nem |  Nem |
| kiszolgálók/recoverableServers | Nem |  Nem |
| kiszolgálók/topQueryStatistics | Nem |  Nem |
| servers/virtualNetworkRules | Nem |  Nem |
| kiszolgálók/waitStatistics | Nem |  Nem |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| IotHubs | Igen | Igen |
| IotHubs/eventGridFilters | Nem |  Nem |
| ProvisioningServices | Igen | Igen |
| használat | Nem |  Nem |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| tartományvezérlők | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Labs | Igen | Igen |
| labs/serviceRunners | Igen | Igen |
| Labor/virtualMachines | Igen | Igen |
| menetrend | Igen | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nem |  Nem |
| databaseAccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| tartományok | Igen | Igen |
| tartományok/domainOwnershipIdentifiers | Nem |  Nem |
| generateSsoRequest | Nem |  Nem |
| topLevelDomains | Nem |  Nem |
| validateDomainRegistrationInformation | Nem |  Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| lcsprojects | Nem |  Nem |
| lcsprojects/clouddeployments | Nem |  Nem |
| lcsprojects/összekötők | Nem |  Nem |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| tartományok | Igen | Nem |
| tartományok/témakörök | Nem |  Nem |
| eventSubscriptions | Nem |  Nem |
| extensionTopics | Nem |  Nem |
| témakörök | Igen | Nem |
| topicTypes | Nem |  Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fürtök | Igen | Nem |
| névterek | Igen | Nem |
| névterek/engedélyezési szabályok | Nem |  Nem |
| névterek/disasterrecoveryconfigs | Nem |  Nem |
| névterek/eventhubs | Nem |  Nem |
| névterek/eventhubs/engedélyezési szabályok | Nem |  Nem |
| névterek/eventhubs/consumergroups | Nem |  Nem |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| funkciókkal | Nem |  Nem |
| Szolgáltatók | Nem |  Nem |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| beiratkozik | Nem |  Nem |
| galleryitems | Nem |  Nem |
| generateartifactaccessuri | Nem |  Nem |
| myareas | Nem |  Nem |
| myareas/területek | Nem |  Nem |
| myareas/területek/területek | Nem |  Nem |
| myareas/területek/területek/galleryitems | Nem |  Nem |
| myareas/területek/galleryitems | Nem |  Nem |
| myareas/galleryitems | Nem |  Nem |
| Regisztráció | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| retrieveresourcesbyid | Nem |  Nem |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nem |  Nem |
| Szoftver | Nem |  Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| hanaInstances | Igen |  Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fürtök | Igen | Igen |
| fürtök/alkalmazások | Nem |  Nem |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| feladatok | Igen | Igen |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| labelGroups | Nem |  Nem |
| labelGroups/Címkék | Nem |  Nem |
| labelGroups/címkék/feltételek | Nem |  Nem |
| labelGroups/labels/subLabels | Nem |  Nem |
| labelGroups/labels/subLabels/conditions | Nem |  Nem |

## <a name="microsoftinsights"></a>Microsoft. bepillantások
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| actiongroups | Igen | Igen |
| activityLogAlerts | Igen | Igen |
| alertrules | Igen | Igen |
| automatedExportSettings | Nem |  Nem |
| autoscalesettings | Igen | Igen |
| alapkonfiguráció | Nem |  Nem |
| calculatebaseline | Nem |  Nem |
| Összetevők | Igen | Igen |
| összetevők/események | Nem |  Nem |
| components/pricingPlans | Nem |  Nem |
| összetevők/lekérdezés | Nem |  Nem |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |
| eventCategories | Nem |  Nem |
| eventtypes | Nem |  Nem |
| extendedDiagnosticSettings | Nem |  Nem |
| logDefinitions | Nem |  Nem |
| logprofiles | Nem |  Nem |
| logs | Nem |  Nem |
| metricAlerts | Igen | Igen |
| migrateToNewPricingModel | Nem |  Nem |
| myWorkbooks | Nem |  Nem |
| lekérdezés | Nem |  Nem |
| rollbackToLegacyPricingModel | Nem |  Nem |
| scheduledqueryrules | Igen | Igen |
| vmInsightsOnboardingStatuses | Nem |  Nem |
| webteszteket | Igen | Igen |
| Munkafüzetek | Igen | Igen |

## <a name="microsoftintune"></a>Microsoft.Intune
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| IoTApps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| deletedVaults | Nem |  Nem |
| boltívek | Igen | Igen |
| tárolók/accessPolicies | Nem |  Nem |
| tárolók/titkok | Nem |  Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fürtök | Igen | Igen |
| fürtök/adatbázisok | Nem |  Nem |
| fürtök/adatbázisok/dataconnections | Nem |  Nem |
| fürtök/adatbázisok/eventhubconnections | Nem |  Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| labaccounts | Igen | Igen |
| felhasználók | Nem |  Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| logs | Nem |  Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| integrationAccounts | Igen | Igen |
| munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| commitmentPlans | Igen | Igen |
| webServices | Igen | Igen |
| Munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| fiókok/munkaterületek | Igen | Igen |
| fiókok/munkaterületek/projektek | Igen | Igen |
| teamAccounts | Igen | Igen |
| teamAccounts/munkaterületek | Igen | Igen |
| teamAccounts/munkaterületek/projektek | Igen | Igen |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Munkaterületek | Igen | Igen |
| munkaterületek/számítások | Nem |  Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Identitások | Nem |  Nem |
| userAssignedIdentities | Igen | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| getEntities | Nem |  Nem |
| managementGroups | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| startTenantBackfill | Nem |  Nem |
| tenantBackfillStatus | Nem |  Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| fiókok/eventGridFilters | Nem |  Nem |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| kínál | Nem |  Nem |
| offerTypes | Nem |  Nem |
| offerTypes/közzétevők | Nem |  Nem |
| offerTypes/kiadók/ajánlatok | Nem |  Nem |
| offerTypes/kiadók/ajánlatok/csomagok | Nem |  Nem |
| offerTypes/kiadók/ajánlatok/csomagok/szerződések | Nem |  Nem |
| offerTypes/kiadók/ajánlatok/csomagok/konfigurációk | Nem |  Nem |
| offerTypes/publishers/offers/plans/configs/importImage | Nem |  Nem |
| privategalleryitems | Nem |  Nem |
| termékek | Nem |  Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| classicDevServices | Igen | Igen |
| updateCommunicationPreference | Nem |  Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| megállapodások | Nem |  Nem |
| offertypes | Nem |  Nem |

## <a name="microsoftmedia"></a>Microsoft.Media
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Mediaservices | Igen | Igen |
| Mediaservices/accountFilters | Nem |  Nem |
| Mediaservices/-eszközök | Nem |  Nem |
| Mediaservices/eszközök/assetFilters | Nem |  Nem |
| mediaservices/contentKeyPolicies | Nem |  Nem |
| Mediaservices/eventGridFilters | Nem |  Nem |
| Mediaservices/liveEventOperations | Nem |  Nem |
| Mediaservices/liveEvents | Igen | Igen |
| Mediaservices/liveEvents/liveOutputs | Nem |  Nem |
| Mediaservices/liveOutputOperations | Nem |  Nem |
| Mediaservices/streamingEndpointOperations | Nem |  Nem |
| Mediaservices/streamingEndpoints | Igen | Igen |
| Mediaservices/streamingLocators | Nem |  Nem |
| Mediaservices/streamingPolicies | Nem |  Nem |
| Mediaservices/átalakítások | Nem |  Nem |
| Mediaservices/átalakítások/feladatok | Nem |  Nem |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| projektek | Igen | Igen |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| applicationGateways | Igen | Nem |
| applicationSecurityGroups | Igen | Igen |
| azureFirewallFqdnTags | Nem |  Nem |
| azureFirewalls | Igen | Nem |
| bgpServiceCommunities | Nem |  Nem |
| kapcsolatok | Igen | Igen |
| ddosCustomPolicies | Igen | Igen |
| ddosProtectionPlans | Igen | Igen |
| dnsOperationStatuses | Nem |  Nem |
| dnszones | Igen | Igen |
| dnszones/A | Nem |  Nem |
| dnszones/AAAA | Nem |  Nem |
| dnszones/mind | Nem |  Nem |
| dnszones/CAA | Nem |  Nem |
| dnszones/CNAME | Nem |  Nem |
| dnszones/MX | Nem |  Nem |
| dnszones/NS | Nem |  Nem |
| dnszones/PTR | Nem |  Nem |
| dnszones/recordsets | Nem |  Nem |
| dnszones/SOA | Nem |  Nem |
| dnszones/SRV | Nem |  Nem |
| dnszones/TXT | Nem |  Nem |
| expressRouteCircuits | Igen  | Nem |
| expressRouteServiceProviders | Nem |  Nem |
| frontdoors | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) | Igen |
| frontdoorWebApplicationFirewallPolicies | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) | Igen |
| getDnsResourceReference | Nem |  Nem |
| interfaceEndpoints | Igen | Igen |
| internalNotify | Nem |  Nem |
| loadBalancers | Igen | Nem |
| localNetworkGateways | Igen | Igen |
| natGateways | Igen | Igen |
| networkIntentPolicies | Igen | Igen |
| networkInterfaces | Igen | Igen |
| networkProfiles | Igen | Igen |
| networkSecurityGroups | Igen | Igen |
| networkWatchers | Igen | Nem |
| networkWatchers/connectionMonitors | Igen | Nem |
| networkWatchers/objektívek | Igen | Nem |
| networkWatchers/pingMeshes | Igen | Nem |
| privateLinkServices | Igen | Igen |
| publicIPAddresses | Igen | Igen |
| publicIPPrefixes | Igen | Igen |
| routeFilters | Igen | Igen |
| routeTables | Igen | Igen |
| serviceEndpointPolicies | Igen | Igen |
| trafficManagerGeographicHierarchies | Nem |  Nem |
| trafficmanagerprofiles | Igen | Igen |
| trafficmanagerprofiles/heatMaps | Nem |  Nem |
| virtualHubs | Igen | Igen |
| virtualNetworkGateways | Igen | Igen |
| virtualNetworks | Igen | Igen |
| virtualNetworks/subnets | Nem |  Nem |
| virtualNetworkTaps | Igen | Igen |
| virtualWans | Igen | Igen |
| vpnGateways | Igen | Nem |
| vpnSites | Igen | Igen |
| webApplicationFirewallPolicies | Igen | Igen |

<a id="frontdoor" />

Az Azure bejárati szolgáltatásához címkéket alkalmazhat az erőforrás létrehozásakor, de a címkék frissítése vagy hozzáadása jelenleg nem támogatott.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| névterek | Igen | Nem |
| névterek/notificationHubs | Igen | Nem |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| eszközök | Nem |  Nem |
| linkTargets | Nem |  Nem |
| storageInsightConfigs | Nem |  Nem |
| Munkaterületek | Igen | Igen |
| munkaterületek/adatforrások | Nem |  Nem |
| workspaces/linkedServices | Nem |  Nem |
| munkaterületek/lekérdezés | Nem |  Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| managementassociations | Nem |  Nem |
| managementconfigurations | Igen | Igen |
| megoldások | Igen | Igen |
| Kilátással | Igen | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| policyEvents | Nem |  Nem |
| policyStates | Nem |  Nem |
| policyTrackedResources | Nem |  Nem |
| szervizelések | Nem |  Nem |

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| konzolok | Nem |  Nem |
| Irányítópultok | Igen | Igen |
| userSettings | Nem |  Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| workspaceCollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| kapacitások | Igen | Igen |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nem |  Nem |
| boltívek | Igen | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| névterek | Igen | Igen |
| névterek/engedélyezési szabályok | Nem |  Nem |
| névterek/hybridconnections | Nem |  Nem |
| névterek/hybridconnections/engedélyezési szabályok | Nem |  Nem |
| névterek/wcfrelays | Nem |  Nem |
| névterek/wcfrelays/engedélyezési szabályok | Nem |  Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Erőforrások | Nem |  Nem |
| subscriptionsStatus | Nem |  Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nem |  Nem |
| childAvailabilityStatuses | Nem |  Nem |
| childResources | Nem |  Nem |
| események | Nem |  Nem |
| impactedResources | Nem |  Nem |
| Értesítések | Nem |  Nem |

## <a name="microsoftresources"></a>Microsoft.Resources
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| központi telepítések | Nem |  Nem |
| üzembe helyezések/műveletek | Nem |  Nem |
| Linkek | Nem |  Nem |
| notifyResourceJobs | Nem |  Nem |
| Szolgáltatók | Nem |  Nem |
| resourceGroups | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| előfizetések | Nem |  Nem |
| előfizetések/szolgáltatók | Nem |  Nem |
| előfizetések/resourceGroups | Nem |  Nem |
| előfizetések/resourcegroups/erőforrások | Nem |  Nem |
| előfizetések/erőforrások | Nem |  Nem |
| előfizetések/tagNames | Nem |  Nem |
| előfizetések/tagNames/tagValues | Nem |  Nem |
| bérlők | Nem |  Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| alkalmazások | Igen | Igen |
| saasresources | Nem |  Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| flow | Igen | Igen |
| feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nem |  Nem |
| searchServices | Igen | Igen |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nem |  Nem |
| riasztások | Nem |  Nem |
| allowedConnections | Nem |  Nem |
| berendezések | Nem |  Nem |
| applicationWhitelistings | Nem |  Nem |
| AutoProvisioningSettings | Nem |  Nem |
| Felelésről | Nem |  Nem |
| dataCollectionAgents | Nem |  Nem |
| discoveredSecuritySolutions | Nem |  Nem |
| externalSecuritySolutions | Nem |  Nem |
| InformationProtectionPolicies | Nem |  Nem |
| jitNetworkAccessPolicies | Nem |  Nem |
| Figyelés | Nem |  Nem |
| figyelés/kártevő szoftverek | Nem |  Nem |
| figyelés/alapterv | Nem |  Nem |
| figyelés/javítás | Nem |  Nem |
| szabályzatok | Nem |  Nem |
| pricings | Nem |  Nem |
| securityContacts | Nem |  Nem |
| securitySolutions | Nem |  Nem |
| securitySolutionsReferenceData | Nem |  Nem |
| securityStatus | Nem |  Nem |
| securityStatus/végpontok | Nem |  Nem |
| securityStatus/alhálózatok | Nem |  Nem |
| securityStatus/virtualMachines | Nem |  Nem |
| securityStatuses | Nem |  Nem |
| securityStatusesSummaries | Nem |  Nem |
| beállítások | Nem |  Nem |
| feladatok | Nem |  Nem |
| topológiák | Nem |  Nem |
| workspaceSettings | Nem |  Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| névterek | Igen | Nem |
| névterek/engedélyezési szabályok | Nem |  Nem |
| névterek/disasterrecoveryconfigs | Nem |  Nem |
| névterek/eventgridfilters | Nem |  Nem |
| névterek/várólisták | Nem |  Nem |
| névterek/várólisták/engedélyezési szabályok | Nem |  Nem |
| névterek/témakörök | Nem |  Nem |
| névterek/témakörök/engedélyezési szabályok | Nem |  Nem |
| névterek/témakörök/előfizetések | Nem |  Nem |
| névterek/témakörök/előfizetések/szabályok | Nem |  Nem |
| premiumMessagingRegions | Nem |  Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| fürtök | Igen | Igen |
| fürtök/alkalmazások | Nem |  Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| alkalmazások | Igen | Igen |
| átjárók | Igen | Igen |
| hálózatok | Igen | Igen |
| titkok | Igen | Igen |
| volumes | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| SignalR | Igen | Igen |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Igen | Igen |
| berendezések | Igen | Igen |
| applicationDefinitions | Igen | Igen |
| alkalmazások | Igen | Igen |
| jitRequests | Igen | Igen |

## <a name="microsoftsql"></a>Microsoft.SQL
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| managedInstances | Igen | Igen |
| managedInstances/adatbázisok | Igen (lásd az alábbi megjegyzést) | Igen |
| managedInstances/databases/backupShortTermRetentionPolicies | Nem | Nem |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nem | Nem |
| managedInstances/databases/vulnerabilityAssessments | Nem | Nem |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nem | Nem |
| managedInstances/encryptionProtector | Nem | Nem |
| managedInstances/keys | Nem | Nem |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem | Nem |
| managedInstances/vulnerabilityAssessments | Nem | Nem |
| Kiszolgálók | Igen | Igen |
| kiszolgálók/rendszergazdák | Nem |  Nem |
| kiszolgálók/communicationLinks | Nem |  Nem |
| kiszolgálók/adatbázisok | Igen (lásd az alábbi megjegyzést) | Igen |
| servers/encryptionProtector | Nem |  Nem |
| kiszolgálók/firewallRules | Nem |  Nem |
| kiszolgálók/kulcsok | Nem |  Nem |
| servers/restorableDroppedDatabases | Nem |  Nem |
| kiszolgálók/serviceobjectives | Nem |  Nem |
| kiszolgálók/tdeCertificates | Nem |  Nem |

> [!NOTE]
> A főadatbázis nem támogatja a címkéket, de más adatbázisokat is, beleértve a Azure SQL Data Warehouse-adatbázisokat, a támogatási címkéket. Azure SQL Data Warehouse adatbázisoknak aktív (nem szüneteltetett) állapotban kell lenniük.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Igen | Igen |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nem |  Nem |
| SqlVirtualMachines | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| storageAccounts | Igen | Igen |
| storageAccounts/blobServices | Nem |  Nem |
| storageAccounts/fileServices | Nem |  Nem |
| storageAccounts/queueServices | Nem |  Nem |
| storageAccounts/szolgáltatások | Nem |  Nem |
| storageAccounts/tableServices | Nem |  Nem |
| használat | Nem |  Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| storageSyncServices | Igen | Igen |
| storageSyncServices/registeredServers | Nem |  Nem |
| storageSyncServices/syncGroups | Nem |  Nem |
| storageSyncServices/syncGroups/cloudEndpoints | Nem |  Nem |
| storageSyncServices/syncGroups/serverEndpoints | Nem |  Nem |
| storageSyncServices/munkafolyamatok | Nem |  Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| kezelők | Igen | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| streamingjobs | Igen (lásd az alábbi megjegyzést) | Igen |
| streamingjobs/diagnosticSettings | Nem |  Nem |

> [!NOTE]
> Nem adhat hozzá címkét, ha a streamingjobs fut. Egy címke hozzáadásához állítsa le az erőforrást.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| CreateSubscription | Nem |  Nem |
| SubscriptionDefinitions | Nem |  Nem |
| SubscriptionOperations | Nem |  Nem |

## <a name="microsoftsupport"></a>microsoft.support
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| supporttickets | Nem |  Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| providerRegistrations | Igen | Igen |
| Erőforrások | Igen | Igen |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| környezetben | Igen | Nem |
| környezetek/accessPolicies | Nem |  Nem |
| környezetek/eventsources | Igen | Nem |
| környezetek/referenceDataSets | Igen | Nem |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| account | Igen | Igen |
| fiók/bővítmény | Igen | Igen |
| fiók/projekt | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Nem |  Nem |
| apiManagementAccounts/apiAcls | Nem |  Nem |
| apiManagementAccounts/apis | Nem |  Nem |
| apiManagementAccounts/apis/apiAcls | Nem |  Nem |
| apiManagementAccounts/apis/connectionAcls | Nem |  Nem |
| apiManagementAccounts/apis/connections | Nem |  Nem |
| apiManagementAccounts/apis/connections/connectionAcls | Nem |  Nem |
| apiManagementAccounts/apis/localizedDefinitions | Nem |  Nem |
| apiManagementAccounts/connectionAcls | Nem |  Nem |
| apiManagementAccounts/connections | Nem |  Nem |
| billingMeters | Nem |  Nem |
| Tanúsítványok | Igen | Igen |
| connectionGateways | Igen | Igen |
| kapcsolatok | Igen | Igen |
| customApis | Igen | Igen |
| deletedSites | Nem |  Nem |
| functions | Nem |  Nem |
| hostingEnvironments | Igen | Igen |
| hostingEnvironments/multiRolePools | Nem |  Nem |
| hostingEnvironments/multiRolePools/instances | Nem |  Nem |
| hostingEnvironments/workerPools | Nem |  Nem |
| hostingEnvironments/workerPools/példányok | Nem |  Nem |
| publishingUsers | Nem |  Nem |
| javaslatok | Nem |  Nem |
| resourceHealthMetadata | Nem |  Nem |
| Runtimes | Nem |  Nem |
| Kiszolgálófarmok | Igen | Igen |
| Kiszolgálófarmok/feldolgozók | Nem |  Nem |
| helyek | Igen | Igen |
| helyek/domainOwnershipIdentifiers | Nem |  Nem |
| sites/hostNameBindings | Nem |  Nem |
| helyek/példányok | Nem |  Nem |
| helyek/példányok/bővítmények | Nem |  Nem |
| helyek/premieraddons | Igen | Igen |
| helyek/javaslatok | Nem |  Nem |
| sites/resourceHealthMetadata | Nem |  Nem |
| helyek/bővítőhelyek | Igen | Igen |
| sites/slots/hostNameBindings | Nem |  Nem |
| helyek/bővítőhelyek/példányok | Nem |  Nem |
| helyek/bővítőhelyek/példányok/bővítmények | Nem |  Nem |
| sourceControls | Nem |  Nem |
| érvényesít | Nem |  Nem |
| verifyHostingEnvironmentVnet | Nem |  Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| DeviceServices | Igen | Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
| ------------- | ----------- | ----------- |
| Összetevők | Nem |  Nem |
| componentsSummary | Nem |  Nem |
| monitorInstances | Nem |  Nem |
| monitorInstancesSummary | Nem |  Nem |
| figyeli | Nem |  Nem |
| notificationSettings | Nem |  Nem |

## <a name="next-steps"></a>További lépések
Ha szeretné megtudni, hogyan alkalmazhat címkéket az erőforrásokra, tekintse meg [a címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md)című témakört.
