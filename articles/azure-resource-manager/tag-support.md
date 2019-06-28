---
title: Az Azure Resource Manager-címke támogatás az erőforrások
description: Látható, hogy melyik Azure-erőforrástípus támogatja a címkék. Az összes Azure-szolgáltatások részletes adatokat biztosít.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 4f1bc1415fbb875120d7b64128cae69e1e3f442c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339843"
---
# <a name="tag-support-for-azure-resources"></a>Az Azure-erőforrások címkék támogatása
Ez a cikk ismerteti, hogy támogatja-e egy erőforrástípus [címkék](resource-group-using-tags.md). A címkével ellátott oszlop **támogatja a címkék** azt jelzi, hogy az erőforrástípust a címkével ellátott tulajdonság. A címkével ellátott oszlop **szóló jelentés a címke** azt jelzi, hogy az erőforrás típusát továbbítja a címke a jelentés.

Első ugyanazokat az adatokat egy vesszővel tagolt formátumú fájlt, töltse le a [címke-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| DomainServices | Igen | Igen |
| DomainServices/oucontainer | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| supportProviders | Nem |  Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| aadsupportcases | Nem |  Nem |
| addsservices | Nem |  Nem |
| Ügynökök | Nem |  Nem |
| anonymousapiusers | Nem |  Nem |
| konfiguráció | Nem |  Nem |
| logs | Nem |  Nem |
| jelentések | Nem |  Nem |
| services | Nem |  Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Konfigurációk | Nem |  Nem |
| generateRecommendations | Nem |  Nem |
| Javaslatok | Nem |  Nem |
| suppressions | Nem |  Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
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
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| reportFeedback | Nem |  Nem |
| szolgáltatás | Igen | Igen |
| validateServiceName | Nem |  Nem |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| attestationProviders | Nem |  Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| classicAdministrators | Nem |  Nem |
| denyAssignments | Nem |  Nem |
| elevateAccess | Nem |  Nem |
| Zárolások | Nem |  Nem |
| engedélyek | Nem |  Nem |
| policyAssignments | Nem |  Nem |
| policyDefinitions | Nem |  Nem |
| policySetDefinitions | Nem |  Nem |
| providerOperations | Nem |  Nem |
| visszaadandó szerepkörkiosztások száma | Nem |  Nem |
| roleDefinitions | Nem |  Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| AutomationAccounts | Igen | Igen |
| automationAccounts/configurations | Igen | Igen |
| automationAccounts/feladatok | Nem |  Nem |
| automationAccounts/runbooks | Igen | Igen |
| automationAccounts/softwareUpdateConfigurations | Nem | Nem |
| automationAccounts/webhooks | Nem |  Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Környezetek | Nem |  Nem |
| környezetek és fiókok | Nem |  Nem |
| környezetek/fiókok/névterek | Nem |  Nem |
| környezetek/fiókok/névterek/konfigurációk | Nem |  Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| b2cDirectories | Igen | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| regisztrációk | Igen | Igen |
| registrations/customerSubscriptions | Nem |  Nem |
| regisztrációk/termékek | Nem |  Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| batchAccounts | Igen | Igen |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| billingAccounts | Nem |  Nem |
| billingAccounts/billingProfiles | Nem |  Nem |
| billingAccounts/billingProfiles/billingSubscriptions | Nem |  Nem |
| billingAccounts/billingProfiles/invoices | Nem |  Nem |
| billingAccounts/billingProfiles/invoices/pricesheet | Nem |  Nem |
| billingAccounts/billingProfiles/operationStatus | Nem |  Nem |
| billingAccounts/billingProfiles/paymentMethods | Nem |  Nem |
| billingAccounts/billingProfiles/policies | Nem |  Nem |
| billingAccounts/billingProfiles/pricesheet | Nem |  Nem |
| billingAccounts/billingProfiles/products | Nem |  Nem |
| billingAccounts/billingProfiles/transactions | Nem |  Nem |
| billingAccounts/billingSubscriptions | Nem |  Nem |
| billingAccounts/szervezeti egységek | Nem |  Nem |
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
| billingAccounts/projects/products | Nem |  Nem |
| billingAccounts/transactions | Nem |  Nem |
| billingPeriods | Nem |  Nem |
| BillingPermissions | Nem |  Nem |
| billingProperty | Nem |  Nem |
| BillingRoleAssignments | Nem |  Nem |
| BillingRoleDefinitions | Nem |  Nem |
| CreateBillingRoleAssignment | Nem |  Nem |
| Szervezeti egységek | Nem |  Nem |
| enrollmentAccounts | Nem |  Nem |
| importRequests | Nem |  Nem |
| importRequests/acceptImportRequest | Nem |  Nem |
| importRequests/declineImportRequest | Nem |  Nem |
| Számlák | Nem |  Nem |
| Adatátvitel | Nem |  Nem |
| adatátvitel/acceptTransfer | Nem |  Nem |
| adatátvitel/declineTransfer | Nem |  Nem |
| transfers/operationStatus | Nem |  Nem |
| usagePlans | Nem |  Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| mapApis | Igen | Igen |
| updateCommunicationPreference | Nem |  Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| BizTalk | Igen | Igen |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Nem |  Nem |
| blueprintAssignments/assignmentOperations | Nem |  Nem |
| az operations blueprintAssignments | Nem |  Nem |
| tervezetek | Nem |  Nem |
| tervezetek/összetevők | Nem |  Nem |
| tervezetek/verziója | Nem |  Nem |
| tervezetek/verziók és összetevők | Nem |  Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| botServices | Igen | Igen |
| botServices/channels | Nem |  Nem |
| botServices/connections | Nem |  Nem |

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Redis | Igen | Igen |
| RedisConfigDefinition | Nem |  Nem |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
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
| reservationOrders, illetve adhatók vissza | Nem |  Nem |
| reservationOrders/felosztása | Nem |  Nem |
| reservationOrders/lapozófájl-kapacitás | Nem |  Nem |
| foglalások | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| validateReservationOrder | Nem |  Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| edgenodes | Nem |  Nem |
| Profilok | Igen | Igen |
| profilok és végpontok | Igen | Igen |
| profiles/endpoints/customdomains | Nem |  Nem |
| profilok és végpontok és források | Nem |  Nem |
| validateProbe | Nem |  Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Tanúsítványrendelések | Igen | Igen |
| Tanúsítványrendelések/tanúsítványok | Nem |  Nem |
| validateCertificateRegistrationInformation | Nem |  Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Képességek | Nem |  Nem |
| domainNames | Nem |  Nem |
| domainNames/capabilities | Nem |  Nem |
| domainNames/internalLoadBalancers | Nem |  Nem |
| domainNames/serviceCertificates | Nem |  Nem |
| domainNames/slots | Nem |  Nem |
| domainNames/slots/roles | Nem |  Nem |
| moveSubscriptionResources | Nem |  Nem |
| operatingSystemFamilies | Nem |  Nem |
| operatingSystems | Nem |  Nem |
| kvóták | Nem |  Nem |
| resourceTypes | Nem |  Nem |
| validateSubscriptionMoveAvailability | Nem |  Nem |
| virtuális gép | Nem |  Nem |
| virtualMachines/diagnosticSettings | Nem |  Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Nem |  Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Képességek | Nem |  Nem |
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
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Képességek | Nem |  Nem |
| Lemezek | Nem |  Nem |
| images | Nem |  Nem |
| osImages | Nem |  Nem |
| osPlatformImages | Nem |  Nem |
| publicImages | Nem |  Nem |
| kvóták | Nem |  Nem |
| storageAccounts | Nem |  Nem |
| storageAccounts/services | Nem |  Nem |
| storageAccounts/services/diagnosticSettings | Nem |  Nem |
| storageAccounts/vmImages | Nem |  Nem |
| vmImages | Nem |  Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| RateCard | Nem |  Nem |
| UsageAggregates | Nem |  Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| availabilitySets | Igen | Igen |
| Lemezek | Igen | Igen |
| images | Igen | Igen |
| restorePointCollections | Igen | Igen |
| restorePointCollections/restorePoints | Nem |  Nem |
| sharedVMImages | Igen | Igen |
| sharedVMImages/versions | Igen | Igen |
| snapshots | Igen | Igen |
| virtuális gép | Igen | Igen |
| virtualMachines/diagnosticSettings | Nem |  Nem |
| virtualMachines/extensions | Igen | Igen |
| virtualMachineScaleSets | Igen | Igen |
| virtualMachineScaleSets/extensions | Nem |  Nem |
| virtualMachineScaleSets/networkInterfaces | Nem |  Nem |
| virtualMachineScaleSets/publicIPAddresses | Nem |  Nem |
| virtualMachineScaleSets/virtualMachines | Nem |  Nem |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem |  Nem |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| AggregatedCost | Nem |  Nem |
| Egyenlegek | Nem |  Nem |
| Költségvetések | Nem |  Nem |
| Díjak | Nem |  Nem |
| CostTags | Nem |  Nem |
| Kreditek | Nem |  Nem |
| események | Nem |  Nem |
| Előrejelzések | Nem |  Nem |
| sok | Nem |  Nem |
| Piactér | Nem |  Nem |
| Pricesheets | Nem |  Nem |
| Termékek | Nem |  Nem |
| ReservationDetails | Nem |  Nem |
| ReservationRecommendations | Nem |  Nem |
| ReservationSummaries | Nem |  Nem |
| ReservationTransactions | Nem |  Nem |
| Tags | Nem |  Nem |
| Fogalmak | Nem |  Nem |
| UsageDetails | Nem |  Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| containerGroups | Igen | Igen |
| serviceAssociationLinks | Nem |  Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| beállításjegyzékek | Igen | Igen |
| beállításjegyzékek/buildek | Nem |  Nem |
| beállításjegyzékek vagy buildek/törlése | Nem |  Nem |
| registries/builds/getLogLink | Nem |  Nem |
| registries/buildTasks | Igen | Igen |
| registries/buildTasks/steps | Nem |  Nem |
| registries/eventGridFilters | Nem |  Nem |
| registries/getBuildSourceUploadUrl | Nem |  Nem |
| registries/GetCredentials | Nem |  Nem |
| beállításjegyzékek/importImage | Nem |  Nem |
| beállításjegyzékek/queueBuild | Nem |  Nem |
| registries/regenerateCredential | Nem |  Nem |
| registries/regenerateCredentials | Nem |  Nem |
| beállításjegyzékek/replikációk | Igen | Igen |
| beállításjegyzékek/futtatások | Nem |  Nem |
| beállításjegyzékek vagy futtatása vagy törlése | Nem |  Nem |
| registries/scheduleRun | Nem |  Nem |
| beállításjegyzékek/feladatok | Igen | Igen |
| registries/updatePolicies | Nem |  Nem |
| beállításjegyzékek és webhookok | Igen | Igen |
| registries/webhooks/getCallbackConfig | Nem |  Nem |
| beállításjegyzékek/webhookok/ping | Nem |  Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| containerServices | Igen | Igen |
| managedClusters | Igen | Igen |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| alkalmazások | Igen | Igen |
| updateCommunicationPreference | Nem |  Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Riasztások | Nem |  Nem |
| billingAccounts | Nem |  Nem |
| Összekötők | Igen | Igen |
| Szervezeti egységek | Nem |  Nem |
| Dimenziók | Nem |  Nem |
| enrollmentAccounts | Nem |  Nem |
| Lekérdezés | Nem |  Nem |
| Regisztráció | Nem |  Nem |
| Reportconfigs | Nem |  Nem |
| Jelentések | Nem |  Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| hubok | Igen | Igen |
| hubok/authorizationPolicies | Nem |  Nem |
| hubs vagy összekötőkkel | Nem |  Nem |
| hubs/csatlakozók/leképezések | Nem |  Nem |
| hubs/interakciók | Nem |  Nem |
| hubs/kpi | Nem |  Nem |
| hubok és hivatkozások | Nem |  Nem |
| hubok és profilok | Nem |  Nem |
| hubs/visszaadandó szerepkörkiosztások száma | Nem |  Nem |
| hubs/szerepkörök | Nem |  Nem |
| hubs/suggestTypeSchema | Nem |  Nem |
| hubs/nézetek | Nem |  Nem |
| hubok/widgetTypes | Nem |  Nem |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Feladatok | Igen | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Igen | Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Munkaterületek | Igen | Nem |
| workspaces/virtualNetworkPeerings | Nem |  Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| katalógusok | Igen | Igen |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| connectionManagers | Igen | Igen |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| dataFactories | Igen | Nem |
| dataFactories/diagnosticSettings | Nem |  Nem |
| dataFactorySchema | Nem |  Nem |
| gyárak | Igen | Nem |
| előállítók/integrationRuntimes | Nem |  Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| accounts/dataLakeStoreAccounts | Nem |  Nem |
| accounts/storageAccounts | Nem |  Nem |
| accounts/storageAccounts/containers | Nem |  Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| accounts/eventGridFilters | Nem |  Nem |
| accounts/firewallRules | Nem |  Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| services | Igen | Igen |
| Services-projektek | Igen | Igen |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |
| servers/recoverableServers | Nem |  Nem |
| servers/virtualNetworkRules | Nem |  Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |
| servers/recoverableServers | Nem |  Nem |
| servers/virtualNetworkRules | Nem |  Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Kiszolgálók | Igen | Igen |
| kiszolgálók/tanácsadók | Nem |  Nem |
| kiszolgálók/queryTexts | Nem |  Nem |
| servers/recoverableServers | Nem |  Nem |
| servers/topQueryStatistics | Nem |  Nem |
| servers/virtualNetworkRules | Nem |  Nem |
| servers/waitStatistics | Nem |  Nem |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| IotHubs | Igen | Igen |
| IotHubs/eventGridFilters | Nem |  Nem |
| ProvisioningServices | Igen | Igen |
| Használati listák | Nem |  Nem |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Tartományvezérlők | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Laborgyakorlatok | Igen | Igen |
| labs/serviceRunners | Igen | Igen |
| labs/virtualMachines | Igen | Igen |
| Ütemezések | Igen | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Nem |  Nem |
| databaseAccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| tartományok | Igen | Igen |
| domains/domainOwnershipIdentifiers | Nem |  Nem |
| generateSsoRequest | Nem |  Nem |
| topLevelDomains | Nem |  Nem |
| validateDomainRegistrationInformation | Nem |  Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| lcsprojects | Nem |  Nem |
| lcsprojects/clouddeployments | Nem |  Nem |
| lcsprojects vagy összekötőkkel | Nem |  Nem |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| tartományok | Igen | Nem |
| tartományok/kapcsolatos témakörök | Nem |  Nem |
| eventSubscriptions | Nem |  Nem |
| extensionTopics | Nem |  Nem |
| kapcsolatos témakörök | Igen | Nem |
| topicTypes | Nem |  Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Fürtök | Igen | Nem |
| névterek | Igen | Nem |
| névtér/szabályok | Nem |  Nem |
| namespaces/disasterrecoveryconfigs | Nem |  Nem |
| névtér/eventhubs | Nem |  Nem |
| névtér/eventhubs/szabályok | Nem |  Nem |
| névtér/eventhubs/consumergroups | Nem |  Nem |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| funkciókkal | Nem |  Nem |
| Szolgáltatók | Nem |  Nem |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Regisztrálása | Nem |  Nem |
| galleryitems | Nem |  Nem |
| generateartifactaccessuri | Nem |  Nem |
| myareas | Nem |  Nem |
| myareas/területek | Nem |  Nem |
| myareas/területek/területek | Nem |  Nem |
| galleryitems myareas/területek/területek | Nem |  Nem |
| myareas/területek/galleryitems | Nem |  Nem |
| myareas/galleryitems | Nem |  Nem |
| Regisztráció | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| retrieveresourcesbyid | Nem |  Nem |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Nem |  Nem |
| Szoftver | Nem |  Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| hanaInstances | Igen |  Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Fürtök | Igen | Igen |
| fürtök és alkalmazások | Nem |  Nem |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Feladatok | Igen | Igen |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| labelGroups | Nem |  Nem |
| labelGroups/labels | Nem |  Nem |
| labelGroups/labels/conditions | Nem |  Nem |
| labelGroups/labels/subLabels | Nem |  Nem |
| labelGroups/labels/subLabels/conditions | Nem |  Nem |

## <a name="microsoftinsights"></a>microsoft.insights
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| actiongroups | Igen | Igen |
| activityLogAlerts | Igen | Igen |
| alertrules | Igen | Igen |
| automatedExportSettings | Nem |  Nem |
| autoscalesettings beállítás | Igen | Igen |
| Alapkonfiguráció | Nem |  Nem |
| calculatebaseline | Nem |  Nem |
| Összetevők | Igen | Igen |
| összetevő/események | Nem |  Nem |
| components/pricingPlans | Nem |  Nem |
| összetevő/lekérdezés | Nem |  Nem |
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
| webteszt | Igen | Igen |
| munkafüzetek | Igen | Igen |

## <a name="microsoftintune"></a>Microsoft.Intune
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| IoTApps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| deletedVaults | Nem |  Nem |
| tárolók | Igen | Igen |
| tárolók/accessPolicies | Nem |  Nem |
| Tárolók vagy titkos kódokkal | Nem |  Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Fürtök | Igen | Igen |
| fürt/adatbázis | Nem |  Nem |
| fürt/adatbázisok/dataconnections | Nem |  Nem |
| fürt/adatbázisok/eventhubconnections | Nem |  Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| labaccounts | Igen | Igen |
| felhasználók | Nem |  Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| logs | Nem |  Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| integrationAccounts | Igen | Igen |
| A munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| commitmentPlans | Igen | Igen |
| webServices | Igen | Igen |
| Munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| fiókok és munkaterületek | Igen | Igen |
| fiókok/munkaterületeket és projekteket | Igen | Igen |
| teamAccounts | Igen | Igen |
| teamAccounts/munkaterületek | Igen | Igen |
| teamAccounts/workspaces/projects | Igen | Igen |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Munkaterületek | Igen | Igen |
| munkaterületek és számítási erőforrások | Nem |  Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Identitások | Nem |  Nem |
| userAssignedIdentities | Igen | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| getEntities | Nem |  Nem |
| managementGroups | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| startTenantBackfill | Nem |  Nem |
| tenantBackfillStatus | Nem |  Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |
| accounts/eventGridFilters | Nem |  Nem |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Ajánlatok | Nem |  Nem |
| offerTypes | Nem |  Nem |
| offerTypes/publishers | Nem |  Nem |
| offerTypes/publishers/offers | Nem |  Nem |
| offerTypes/publishers/offers/plans | Nem |  Nem |
| offerTypes/publishers/offers/plans/agreements | Nem |  Nem |
| offerTypes/publishers/offers/plans/configs | Nem |  Nem |
| offerTypes/publishers/offers/plans/configs/importImage | Nem |  Nem |
| privategalleryitems | Nem |  Nem |
| Termékek | Nem |  Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| classicDevServices | Igen | Igen |
| updateCommunicationPreference | Nem |  Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Szerződések | Nem |  Nem |
| offertypes | Nem |  Nem |

## <a name="microsoftmedia"></a>Microsoft.Media
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| mediaservices | Igen | Igen |
| mediaservices/accountFilters | Nem |  Nem |
| mediaservices/eszközök | Nem |  Nem |
| mediaservices/eszközök/assetFilters | Nem |  Nem |
| mediaservices/contentKeyPolicies | Nem |  Nem |
| mediaservices/eventGridFilters | Nem |  Nem |
| mediaservices/liveEventOperations | Nem |  Nem |
| mediaservices/liveEvents | Igen | Igen |
| mediaservices/liveEvents/liveOutputs | Nem |  Nem |
| mediaservices/liveOutputOperations | Nem |  Nem |
| mediaservices/streamingEndpointOperations | Nem |  Nem |
| mediaservices/Streamvégpontok | Igen | Igen |
| mediaservices/streamingLocators | Nem |  Nem |
| mediaservices/streamingPolicies | Nem |  Nem |
| mediaservices/átalakítások | Nem |  Nem |
| mediaservices/transforms/jobs | Nem |  Nem |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| projektek | Igen | Igen |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| applicationGateways | Igen | Nem |
| applicationSecurityGroups | Igen | Igen |
| azureFirewallFqdnTags | Nem |  Nem |
| azureFirewalls | Igen | Nem |
| bgpServiceCommunities | Nem |  Nem |
| Kapcsolatok | Igen | Igen |
| ddosCustomPolicies | Igen | Igen |
| ddosProtectionPlans | Igen | Igen |
| dnsOperationStatuses | Nem |  Nem |
| dnszones | Igen | Igen |
| dnszones/A | Nem |  Nem |
| dnszones/AAAA | Nem |  Nem |
| dnszones/all | Nem |  Nem |
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
| frontdoors | Igen, de korlátozott (lásd: [vegye figyelembe az alábbi](#frontdoor)) | Igen |
| frontdoorWebApplicationFirewallPolicies | Igen, de korlátozott (lásd: [vegye figyelembe az alábbi](#frontdoor)) | Igen |
| getDnsResourceReference | Nem |  Nem |
| interfaceEndpoints | Igen | Igen |
| internalNotify | Nem |  Nem |
| loadBalancers | Igen | Nem |
| localNetworkGateways | Igen | Igen |
| natGateways | Igen | Igen |
| networkIntentPolicies | Igen | Igen |
| Hálózati | Igen | Igen |
| networkProfiles | Igen | Igen |
| networkSecurityGroups | Igen | Igen |
| networkWatchers | Igen | Nem |
| networkWatchers/connectionMonitors | Igen | Nem |
| networkWatchers/átvilágított felülettel | Igen | Nem |
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
| virtualNetworkGateways | Igen | Nem |
| virtualNetworks | Igen | Igen |
| virtualNetworks/subnets | Nem |  Nem |
| virtualNetworkTaps | Igen | Igen |
| virtualWans | Igen | Igen |
| vpnGateways | Igen | Nem |
| vpnSites | Igen | Igen |
| webApplicationFirewallPolicies | Igen | Igen |

<a id="frontdoor" />

Címkékkel láthatja az erőforrás létrehozásakor Azure bejárati ajtajának Service-ben, de frissítés és a címkék hozzáadása jelenleg nem támogatott.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| névterek | Igen | Nem |
| névtér/notificationHubs | Igen | Nem |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| eszközök | Nem |  Nem |
| linkTargets | Nem |  Nem |
| storageInsightConfigs | Nem |  Nem |
| Munkaterületek | Igen | Igen |
| workspaces/dataSources | Nem |  Nem |
| workspaces/linkedServices | Nem |  Nem |
| munkaterületek és lekérdezési | Nem |  Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| managementassociations | Nem |  Nem |
| managementconfigurations | Igen | Igen |
| megoldások | Igen | Igen |
| Nézetek | Igen | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| policyEvents | Nem |  Nem |
| policyStates | Nem |  Nem |
| policyTrackedResources | Nem |  Nem |
| szervizelések | Nem |  Nem |

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| a konzolok | Nem |  Nem |
| Az irányítópultok | Igen | Igen |
| userSettings | Nem |  Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| workspaceCollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Kapacitások | Igen | Igen |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| fiókok | Igen | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Nem |  Nem |
| tárolók | Igen | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| névterek | Igen | Igen |
| névtér/szabályok | Nem |  Nem |
| névtér/hybridconnections | Nem |  Nem |
| névtér/hybridconnections/szabályok | Nem |  Nem |
| névtér/wcfrelays | Nem |  Nem |
| névtér/wcfrelays/szabályok | Nem |  Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Erőforrások | Nem |  Nem |
| subscriptionsStatus | Nem |  Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Nem |  Nem |
| childAvailabilityStatuses | Nem |  Nem |
| childResources | Nem |  Nem |
| események | Nem |  Nem |
| impactedResources | Nem |  Nem |
| Értesítések | Nem |  Nem |

## <a name="microsoftresources"></a>Microsoft.Resources
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Központi telepítések | Nem |  Nem |
| Az operations központi telepítések | Nem |  Nem |
| Hivatkozások | Nem |  Nem |
| notifyResourceJobs | Nem |  Nem |
| Szolgáltatók | Nem |  Nem |
| resourceGroups | Nem |  Nem |
| Erőforrások | Nem |  Nem |
| Előfizetések | Nem |  Nem |
| Az előfizetések és szolgáltatók | Nem |  Nem |
| előfizetés/resourceGroups | Nem |  Nem |
| előfizetés/resourcegroups/erőforrások | Nem |  Nem |
| Az előfizetések és erőforrások | Nem |  Nem |
| előfizetések/tagnames | Nem |  Nem |
| Előfizetések/tagNames/tagValues | Nem |  Nem |
| Bérlők | Nem |  Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| alkalmazások | Igen | Igen |
| saasresources | Nem |  Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| folyamatok | Igen | Igen |
| feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Nem |  Nem |
| searchServices | Igen | Igen |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Nem |  Nem |
| riasztások | Nem |  Nem |
| allowedConnections | Nem |  Nem |
| készülékek | Nem |  Nem |
| applicationWhitelistings | Nem |  Nem |
| AutoProvisioningSettings | Nem |  Nem |
| Megfelelőségre | Nem |  Nem |
| dataCollectionAgents | Nem |  Nem |
| discoveredSecuritySolutions | Nem |  Nem |
| externalSecuritySolutions | Nem |  Nem |
| InformationProtectionPolicies | Nem |  Nem |
| jitNetworkAccessPolicies | Nem |  Nem |
| Figyelés | Nem |  Nem |
| figyelés és kártevők elleni | Nem |  Nem |
| figyelés és referenciakonfiguráció | Nem |  Nem |
| figyelési/patch | Nem |  Nem |
| Szabályzatok | Nem |  Nem |
| díjcsoportokkal | Nem |  Nem |
| securityContacts | Nem |  Nem |
| securitySolutions | Nem |  Nem |
| securitySolutionsReferenceData | Nem |  Nem |
| securityStatus | Nem |  Nem |
| securityStatus/endpoints | Nem |  Nem |
| securityStatus/subnets | Nem |  Nem |
| securityStatus/virtualMachines | Nem |  Nem |
| securityStatuses | Nem |  Nem |
| securityStatusesSummaries | Nem |  Nem |
| beállítások | Nem |  Nem |
| feladatok | Nem |  Nem |
| Topológiák | Nem |  Nem |
| workspaceSettings | Nem |  Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| névterek | Igen | Nem |
| névtér/szabályok | Nem |  Nem |
| namespaces/disasterrecoveryconfigs | Nem |  Nem |
| névtér/eventgridfilters | Nem |  Nem |
| névtér/üzenetsorok | Nem |  Nem |
| névtér/üzenetsorok/szabályok | Nem |  Nem |
| névtér/kapcsolatos témakörök | Nem |  Nem |
| névtér/témakörök/szabályok | Nem |  Nem |
| névtér/témakörök/előfizetések | Nem |  Nem |
| névtér/témakörök/előfizetések/szabályok | Nem |  Nem |
| premiumMessagingRegions | Nem |  Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Fürtök | Igen | Igen |
| fürtök és alkalmazások | Nem |  Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| alkalmazások | Igen | Igen |
| Átjárók | Igen | Igen |
| Hálózatok | Igen | Igen |
| Titkos kulcsok | Igen | Igen |
| volumes | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| SignalR | Igen | Igen |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Igen | Igen |
| készülékek | Igen | Igen |
| applicationDefinitions | Igen | Igen |
| alkalmazások | Igen | Igen |
| jitRequests | Igen | Igen |

## <a name="microsoftsql"></a>Microsoft.SQL
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| managedInstances | Igen | Igen |
| managedInstances/databases | Igen (lásd az alábbi megjegyzést) | Igen |
| managedInstances/databases/backupShortTermRetentionPolicies | Nem | Nem |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nem | Nem |
| managedInstances/databases/vulnerabilityAssessments | Nem | Nem |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nem | Nem |
| managedInstances/encryptionProtector | Nem | Nem |
| managedInstances/keys | Nem | Nem |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem | Nem |
| managedInstances/vulnerabilityAssessments | Nem | Nem |
| Kiszolgálók | Igen | Igen |
| kiszolgálók és a rendszergazdák | Nem |  Nem |
| servers/communicationLinks | Nem |  Nem |
| kiszolgálók és adatbázisok | Igen (lásd az alábbi megjegyzést) | Igen |
| servers/encryptionProtector | Nem |  Nem |
| servers/firewallRules | Nem |  Nem |
| kiszolgálók és kulcs | Nem |  Nem |
| servers/restorableDroppedDatabases | Nem |  Nem |
| kiszolgálók/serviceobjectives | Nem |  Nem |
| servers/tdeCertificates | Nem |  Nem |

> [!NOTE]
> A Master adatbázis nem támogatja a címkék, de más adatbázisok, beleértve az Azure SQL Data Warehouse, támogatja a címkék. Az Azure SQL Data Warehouse-adatbázist kell lennie az aktív (nem felfüggesztett) állapotba.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Igen | Igen |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Nem |  Nem |
| SqlVirtualMachines | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| storageAccounts | Igen | Igen |
| storageAccounts/blobServices | Nem |  Nem |
| storageAccounts/fileServices | Nem |  Nem |
| storageAccounts/queueServices | Nem |  Nem |
| storageAccounts/services | Nem |  Nem |
| storageAccounts/tableServices | Nem |  Nem |
| Használati listák | Nem |  Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| storageSyncServices | Igen | Igen |
| storageSyncServices/registeredServers | Nem |  Nem |
| storageSyncServices/syncGroups | Nem |  Nem |
| storageSyncServices/syncGroups/cloudEndpoints | Nem |  Nem |
| storageSyncServices/syncGroups/serverEndpoints | Nem |  Nem |
| storageSyncServices/munkafolyamatok | Nem |  Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| vezetők | Igen | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| streamingjobs | Igen (lásd az alábbi megjegyzést) | Igen |
| streamingjobs/diagnosticSettings | Nem |  Nem |

> [!NOTE]
> Nem adhat meg egy címkét, streamingjobs futtatásakor. Címke hozzáadásához az erőforrás leállítása.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| CreateSubscription | Nem |  Nem |
| SubscriptionDefinitions | Nem |  Nem |
| SubscriptionOperations | Nem |  Nem |

## <a name="microsoftsupport"></a>microsoft.support
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| supporttickets | Nem |  Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| providerRegistrations | Igen | Igen |
| Erőforrások | Igen | Igen |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Környezetek | Igen | Nem |
| környezetek/accessPolicies | Nem |  Nem |
| környezetek/eventsources | Igen | Nem |
| environments/referenceDataSets | Igen | Nem |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| account | Igen | Igen |
| fiók/bővítmény | Igen | Igen |
| fiók és a projekt | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
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
| Kapcsolatok | Igen | Igen |
| customApis | Igen | Igen |
| deletedSites | Nem |  Nem |
| functions | Nem |  Nem |
| hostingEnvironments | Igen | Igen |
| hostingEnvironments/multiRolePools | Nem |  Nem |
| hostingEnvironments/multiRolePools/instances | Nem |  Nem |
| hostingEnvironments/workerPools | Nem |  Nem |
| hostingEnvironments/workerPools/instances | Nem |  Nem |
| publishingUsers | Nem |  Nem |
| Javaslatok | Nem |  Nem |
| resourceHealthMetadata | Nem |  Nem |
| modulok | Nem |  Nem |
| Kiszolgálófarmok | Igen | Igen |
| Kiszolgálófarmok/feldolgozó | Nem |  Nem |
| webhelyek | Igen | Igen |
| sites/domainOwnershipIdentifiers | Nem |  Nem |
| sites/hostNameBindings | Nem |  Nem |
| helyek és példányok | Nem |  Nem |
| webhelyek/példány/bővítmények | Nem |  Nem |
| webhelyek/premieraddons | Igen | Igen |
| helyek és javaslatok | Nem |  Nem |
| sites/resourceHealthMetadata | Nem |  Nem |
| helyek és tárhelyek | Igen | Igen |
| sites/slots/hostNameBindings | Nem |  Nem |
| helyek és tárolóhelyek/példány | Nem |  Nem |
| webhelyek/tárolóhelyek/példány/bővítmények | Nem |  Nem |
| sourceControls | Nem |  Nem |
| Ellenőrzése | Nem |  Nem |
| verifyHostingEnvironmentVnet | Nem |  Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Nem |  Nem |
| diagnosticSettingsCategories | Nem |  Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| DeviceServices | Igen | Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Erőforrás típusa | Támogatja a címkék | A jelentés megjelölése |
| ------------- | ----------- | ----------- |
| Összetevők | Nem |  Nem |
| componentsSummary | Nem |  Nem |
| monitorInstances | Nem |  Nem |
| monitorInstancesSummary | Nem |  Nem |
| Figyelők | Nem |  Nem |
| notificationSettings | Nem |  Nem |

## <a name="next-steps"></a>További lépések
Erőforrások címkékkel kezelésével kapcsolatos információkért lásd: [címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md).
