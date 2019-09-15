---
title: Az erőforrások támogatásának Azure Resource Manager címkézése
description: Megjeleníti, hogy mely Azure-erőforrástípusok támogatják a címkéket. Az összes Azure-szolgáltatás részleteit tartalmazza.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 7b8d3de3eaa9cdbb945879f2acc283d74c8b4e82
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996829"
---
# <a name="tag-support-for-azure-resources"></a>Azure-erőforrások támogatásának címkézése
Ez a cikk azt mutatja be, hogy az erőforrástípus támogatja-e a [címkéket](resource-group-using-tags.md). A címkével ellátott oszlopban szereplő **címke jelzi,** hogy az erőforrástípus rendelkezik-e tulajdonsággal a címkéhez. A **címke a Cost jelentésben** feliratú oszlop jelzi, hogy az erőforrástípus átadja-e a címkét a Cost jelentésnek.

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)fájlt.

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. igazolás](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genf](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. felhasználás](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. előfizetés](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | DomainServices | Igen |Igen |
> | DomainServices/oucontainer | Nem |Nem |
> | DomainServices/ReplicaSets | Igen |Igen |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nem |Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nem |Nem |
> | addsservices | Nem |Nem |
> | ügynökök | Nem |Nem |
> | anonymousapiusers | Nem |Nem |
> | konfiguráció | Nem |Nem |
> | logs | Nem |Nem |
> | jelentések | Nem |Nem |
> | servicehealthmetrics | Nem |Nem |
> | services | Nem |Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | konfigurációk | Nem |Nem |
> | generateRecommendations | Nem |Nem |
> | metaadatok | Nem |Nem |
> | javaslatok | Nem |Nem |
> | fóliakondenzát | Nem |Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | actionRules | Igen |Igen |
> | riasztások | Nem |Nem |
> | alertsList | Nem |Nem |
> | alertsMetaData | Nem |Nem |
> | alertsSummary | Nem |Nem |
> | alertsSummaryList | Nem |Nem |
> | Visszajelzés | Nem |Nem |
> | smartDetectorAlertRules | Igen |Igen |
> | smartDetectorRuntimeEnvironments | Nem |Nem |
> | smartGroups | Nem |Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kiszolgáló | Igen |Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nem |Nem |
> | szolgáltatás | Igen |Igen |
> | validateServiceName | Nem |Nem |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | configurationStores | Igen |Igen |
> | configurationStores/eventGridFilters | Nem |Nem |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Tavasz | Igen |Igen |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nem |Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nem |Nem |
> | dataAliases | Nem |Nem |
> | denyAssignments | Nem |Nem |
> | elevateAccess | Nem |Nem |
> | zárak | Nem |Nem |
> | engedélyek | Nem |Nem |
> | policyAssignments | Nem |Nem |
> | policyDefinitions | Nem |Nem |
> | policySetDefinitions | Nem |Nem |
> | providerOperations | Nem |Nem |
> | roleAssignments | Nem |Nem |
> | roleDefinitions | Nem |Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Igen |Igen |
> | automationAccounts/konfigurációk | Igen |Igen |
> | automationAccounts/feladatok | Nem |Nem |
> | automationAccounts/runbooks | Igen |Igen |
> | automationAccounts/softwareUpdateConfigurations | Nem |Nem |
> | automationAccounts/webhooks | Nem |Nem |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | configurationStores | Igen |Igen |
> | configurationStores/eventGridFilters | Nem |Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | környezetben | Nem |Nem |
> | környezetek/fiókok | Nem |Nem |
> | környezetek/fiókok/névterek | Nem |Nem |
> | környezetek/fiókok/névterek/konfigurációk | Nem |Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Igen |Nem |
> | b2ctenants | Nem |Nem |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Igen |Igen |
> | postgresInstances | Igen |Igen |
> | sqlBigDataClusters | Igen |Igen |
> | sqlInstances | Igen |Igen |
> | sqlServerRegistrations | Igen |Igen |
> | sqlServerRegistrations/sqlServers | Nem |Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | regisztrációk | Igen |Igen |
> | registrations/customerSubscriptions | Nem |Nem |
> | regisztrációk/termékek | Nem |Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Igen |Igen |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nem |Nem |
> | billingAccounts/szerződések | Nem |Nem |
> | billingAccounts/billingPermissions | Nem |Nem |
> | billingAccounts/billingProfiles | Nem |Nem |
> | billingAccounts/billingProfiles/billingPermissions | Nem |Nem |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nem |Nem |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nem |Nem |
> | billingAccounts/billingProfiles/billingSubscriptions | Nem |Nem |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nem |Nem |
> | billingAccounts/billingProfiles/ügyfelek | Nem |Nem |
> | billingAccounts/billingProfiles/invoices | Nem |Nem |
> | billingAccounts/billingProfiles/invoices/pricesheet | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek/átvitel | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek/updateAutoRenew | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/tranzakciók | Nem |Nem |
> | billingAccounts/billingProfiles/invoiceSections/Transfers | Nem |Nem |
> | billingAccounts/BillingProfiles/patchOperations | Nem |Nem |
> | billingAccounts/billingProfiles/paymentMethods | Nem |Nem |
> | billingAccounts/billingProfiles/házirendek | Nem |Nem |
> | billingAccounts/billingProfiles/pricesheet | Nem |Nem |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nem |Nem |
> | billingAccounts/billingProfiles/products | Nem |Nem |
> | billingAccounts/billingProfiles/transactions | Nem |Nem |
> | billingAccounts/billingRoleAssignments | Nem |Nem |
> | billingAccounts/billingRoleDefinitions | Nem |Nem |
> | billingAccounts/billingSubscriptions | Nem |Nem |
> | billingAccounts/createBillingRoleAssignment | Nem |Nem |
> | billingAccounts/createInvoiceSectionOperations | Nem |Nem |
> | billingAccounts/ügyfelek | Nem |Nem |
> | billingAccounts/ügyfelek/billingSubscriptions | Nem |Nem |
> | billingAccounts/ügyfelek/initiateTransfer | Nem |Nem |
> | billingAccounts/ügyfelek/termékek | Nem |Nem |
> | billingAccounts/ügyfelek/tranzakciók | Nem |Nem |
> | billingAccounts/ügyfelek/átvitelek | Nem |Nem |
> | billingAccounts/részlegek | Nem |Nem |
> | billingAccounts/enrollmentAccounts | Nem |Nem |
> | billingAccounts/számlák | Nem |Nem |
> | billingAccounts/invoiceSections | Nem |Nem |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nem |Nem |
> | billingAccounts/invoiceSections/billingSubscriptions | Nem |Nem |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Nem |Nem |
> | billingAccounts/invoiceSections/Jogosultságszint-emelés | Nem |Nem |
> | billingAccounts/invoiceSections/initiateTransfer | Nem |Nem |
> | billingAccounts/invoiceSections/patchOperations | Nem |Nem |
> | billingAccounts/invoiceSections/productMoveOperations | Nem |Nem |
> | billingAccounts/invoiceSections/products | Nem |Nem |
> | billingAccounts/invoiceSections/termékek/átvitel | Nem |Nem |
> | billingAccounts/invoiceSections/termékek/updateAutoRenew | Nem |Nem |
> | billingAccounts/invoiceSections/tranzakciók | Nem |Nem |
> | billingAccounts/invoiceSections/transfers | Nem |Nem |
> | billingAccounts/lineOfCredit | Nem |Nem |
> | billingAccounts/patchOperations | Nem |Nem |
> | billingAccounts/paymentMethods | Nem |Nem |
> | billingAccounts/termékek | Nem |Nem |
> | billingAccounts/transactions | Nem |Nem |
> | billingPeriods | Nem |Nem |
> | BillingPermissions | Nem |Nem |
> | billingProperty | Nem |Nem |
> | BillingRoleAssignments | Nem |Nem |
> | BillingRoleDefinitions | Nem |Nem |
> | CreateBillingRoleAssignment | Nem |Nem |
> | részlegek | Nem |Nem |
> | enrollmentAccounts | Nem |Nem |
> | számlák | Nem |Nem |
> | transzferek | Nem |Nem |
> | átvitelek/acceptTransfer | Nem |Nem |
> | átvitelek/declineTransfer | Nem |Nem |
> | transfers/operationStatus | Nem |Nem |
> | átvitelek/validateTransfer | Nem |Nem |
> | validateAddress | Nem |Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | mapApis | Igen |Igen |
> | updateCommunicationPreference | Nem |Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | BizTalk | Igen |Igen |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Igen |Igen |
> | néző | Igen |Igen |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nem |Nem |
> | blueprintAssignments/assignmentOperations | Nem |Nem |
> | blueprintAssignments/műveletek | Nem |Nem |
> | tervrajzok | Nem |Nem |
> | tervrajzok/összetevők | Nem |Nem |
> | tervezetek/verziók | Nem |Nem |
> | tervrajzok/verziók/összetevők | Nem |Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | botServices | Igen |Igen |
> | botServices/csatornák | Nem |Nem |
> | botServices/kapcsolatok | Nem |Nem |
> | Nyelvek | Nem |Nem |
> | sablonok | Nem |Nem |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Redis | Igen |Igen |
> | RedisConfigDefinition | Nem |Nem |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nem |Nem |
> | calculateExchange | Nem |Nem |
> | calculatePrice | Nem |Nem |
> | calculatePurchasePrice | Nem |Nem |
> | katalógusok | Nem |Nem |
> | commercialReservationOrders | Nem |Nem |
> | Exchange | Nem |Nem |
> | placePurchaseOrder | Nem |Nem |
> | reservationOrders | Nem |Nem |
> | reservationOrders/calculateRefund | Nem |Nem |
> | reservationOrders/egyesítés | Nem |Nem |
> | reservationOrders/foglalások | Nem |Nem |
> | reservationOrders/foglalások/változatok | Nem |Nem |
> | reservationOrders/Return | Nem |Nem |
> | reservationOrders/felosztás | Nem |Nem |
> | reservationOrders/swap | Nem |Nem |
> | foglalások | Nem |Nem |
> | erőforrások | Nem |Nem |
> | validateReservationOrder | Nem |Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem |Nem |
> | CdnWebApplicationFirewallPolicies | Igen |Igen |
> | edgenodes | Nem |Nem |
> | profiles | Igen |Igen |
> | profilok/végpontok | Igen |Igen |
> | profiles/endpoints/customdomains | Nem |Nem |
> | profilok/végpontok/eredetek | Nem |Nem |
> | validateProbe | Nem |Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Tanúsítványrendelések | Igen |Igen |
> | Tanúsítványrendelések/tanúsítványok | Nem |Nem |
> | validateCertificateRegistrationInformation | Nem |Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem |Nem |
> | Tartománynevek | Igen |Igen |
> | domainNames/capabilities | Nem |Nem |
> | domainNames/internalLoadBalancers | Nem |Nem |
> | domainNames/serviceCertificates | Nem |Nem |
> | Tartománynevek/tárolóhelyek | Nem |Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök | Nem |Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök/metricDefinitions | Nem |Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök/mérőszámok | Nem |Nem |
> | moveSubscriptionResources | Nem |Nem |
> | operatingSystemFamilies | Nem |Nem |
> | operatingSystems | Nem |Nem |
> | kvóták | Nem |Nem |
> | resourceTypes | Nem |Nem |
> | validateSubscriptionMoveAvailability | Nem |Nem |
> | VirtualMachines | Igen |Igen |
> | virtualMachines/diagnosticSettings | Nem |Nem |
> | virtualMachines/metricDefinitions | Nem |Nem |
> | virtualMachines/mérőszámok | Nem |Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nem |Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem |Nem |
> | expressRouteCrossConnections | Nem |Nem |
> | expressRouteCrossConnections/peerings | Nem |Nem |
> | gatewaySupportedDevices | Nem |Nem |
> | networkSecurityGroups | Igen |Igen |
> | kvóták | Nem |Nem |
> | reservedIps | Igen |Igen |
> | virtualNetworks | Igen |Igen |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem |Nem |
> | virtualNetworks/virtualNetworkPeerings | Nem |Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | képességek | Nem |Nem |
> | lemezek | Nem |Nem |
> | rendszerképek | Nem |Nem |
> | osImages | Nem |Nem |
> | osPlatformImages | Nem |Nem |
> | publicImages | Nem |Nem |
> | kvóták | Nem |Nem |
> | storageAccounts | Igen |Igen |
> | storageAccounts/blobServices | Nem |Nem |
> | storageAccounts/fileServices | Nem |Nem |
> | storageAccounts/metricDefinitions | Nem |Nem |
> | storageAccounts/mérőszámok | Nem |Nem |
> | storageAccounts/queueServices | Nem |Nem |
> | storageAccounts/szolgáltatások | Nem |Nem |
> | storageAccounts/szolgáltatások/diagnosticSettings | Nem |Nem |
> | storageAccounts/szolgáltatások/metricDefinitions | Nem |Nem |
> | storageAccounts/szolgáltatások/mérőszámok | Nem |Nem |
> | storageAccounts/tableServices | Nem |Nem |
> | storageAccounts/vmImages | Nem |Nem |
> | Lemezképet | Nem |Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | RateCard | Nem |Nem |
> | UsageAggregates | Nem |Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Igen |Igen |
> | diskEncryptionSets | Igen |Igen |
> | lemezek | Igen |Igen |
> | katalógusok | Igen |Igen |
> | galériák/alkalmazások | Igen |Igen |
> | galériák/alkalmazások/verziók | Igen |Igen |
> | galériák/lemezképek | Igen |Igen |
> | galériák/lemezképek/verziók | Igen |Igen |
> | hostGroups | Igen |Igen |
> | hostGroups/gazdagépek | Igen |Igen |
> | rendszerképek | Igen |Igen |
> | proximityPlacementGroups | Igen |Igen |
> | restorePointCollections | Igen |Igen |
> | restorePointCollections/restorePoints | Nem |Nem |
> | sharedVMImages | Igen |Igen |
> | sharedVMImages/verziók | Igen |Igen |
> | pillanatképek | Igen |Igen |
> | VirtualMachines | Igen |Igen |
> | virtualMachines/bővítmények | Igen |Igen |
> | virtualMachines/metricDefinitions | Nem |Nem |
> | virtualMachines/scriptJobs | Nem |Nem |
> | virtualMachines/softwareUpdateDeployments | Nem |Nem |
> | virtualMachineScaleSets | Igen |Igen |
> | virtualMachineScaleSets/extensions | Nem |Nem |
> | virtualMachineScaleSets/networkInterfaces | Nem |Nem |
> | virtualMachineScaleSets/publicIPAddresses | Nem |Nem |
> | virtualMachineScaleSets/virtualMachines | Nem |Nem |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem |Nem |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nem |Nem |
> | Egyenlegek | Nem |Nem |
> | Költségvetések | Nem |Nem |
> | Díjak | Nem |Nem |
> | CostTags | Nem |Nem |
> | hitelek | Nem |Nem |
> | események | Nem |Nem |
> | Előrejelzések | Nem |Nem |
> | számos | Nem |Nem |
> | Piacterek | Nem |Nem |
> | Pricesheets | Nem |Nem |
> | termék | Nem |Nem |
> | ReservationDetails | Nem |Nem |
> | ReservationRecommendations | Nem |Nem |
> | ReservationSummaries | Nem |Nem |
> | ReservationTransactions | Nem |Nem |
> | Tags | Nem |Nem |
> | bérlők | Nem |Nem |
> | Feltételek | Nem |Nem |
> | UsageDetails | Nem |Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | containerGroups | Igen |Igen |
> | serviceAssociationLinks | Nem |Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kibocsátásiegység | Igen |Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildek | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/Mégse | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/getLogLink | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks | Igen |Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések | Nem |Nem |
> | registries/eventGridFilters | Nem |Nem |
> | registries/getBuildSourceUploadUrl | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/GetCredentials | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/importImage | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/queueBuild | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredential | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredentials | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen |Igen |
> | kibocsátásiegység-forgalmi jegyzékek/futtatások | Nem |Nem |
> | nyilvántartások/futtatások/megszakítás | Nem |Nem |
> | registries/scheduleRun | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen |Igen |
> | registries/updatePolicies | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen |Igen |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/getCallbackConfig | Nem |Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/ping | Nem |Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | containerServices | Igen |Igen |
> | managedClusters | Igen |Igen |
> | openShiftManagedClusters | Igen |Igen |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen |Igen |
> | updateCommunicationPreference | Nem |Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Riasztások | Nem |Nem |
> | billingAccounts | Nem |Nem |
> | Költségvetések | Nem |Nem |
> | CloudConnectors | Nem |Nem |
> | Összekötők | Igen |Igen |
> | Részlegek | Nem |Nem |
> | Dimenziók | Nem |Nem |
> | enrollmentAccounts | Nem |Nem |
> | Exportálások | Nem |Nem |
> | ExternalBillingAccounts | Nem |Nem |
> | ExternalBillingAccounts/riasztások | Nem |Nem |
> | ExternalBillingAccounts/méretek | Nem |Nem |
> | ExternalBillingAccounts/előrejelzés | Nem |Nem |
> | ExternalBillingAccounts/lekérdezés | Nem |Nem |
> | ExternalSubscriptions | Nem |Nem |
> | ExternalSubscriptions/riasztások | Nem |Nem |
> | ExternalSubscriptions/méretek | Nem |Nem |
> | ExternalSubscriptions/előrejelzés | Nem |Nem |
> | ExternalSubscriptions/lekérdezés | Nem |Nem |
> | Előrejelzés | Nem |Nem |
> | Lekérdezés | Nem |Nem |
> | Regisztráció | Nem |Nem |
> | Reportconfigs | Nem |Nem |
> | Jelentések | Nem |Nem |
> | Beállítások | Nem |Nem |
> | showbackRules | Nem |Nem |
> | Nézetek | Nem |Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kérelmek | Nem |Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | szövetségek | Nem |Nem |
> | resourceProviders | Igen |Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | feladatok | Igen |Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Igen |Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen |Nem |
> | munkaterületek/virtualNetworkPeerings | Nem |Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | katalógusok | Igen |Igen |
> | datacatalogs | Igen |Igen |
> | datacatalogs/adatforrások | Nem |Nem |
> | datacatalogs/adatforrások/vizsgálatok | Nem |Nem |
> | datacatalogs/adatforrások/vizsgálatok/adatkészletek | Nem |Nem |
> | datacatalogs/adatforrások/vizsgálatok/triggerek | Nem |Nem |
> | datacatalogs/scantargets | Nem |Nem |
> | datacatalogs/scantargets/adatkészletek | Nem |Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dataFactories | Igen |Nem |
> | dataFactories/diagnosticSettings | Nem |Nem |
> | dataFactories/metricDefinitions | Nem |Nem |
> | dataFactorySchema | Nem |Nem |
> | előállítók | Igen |Nem |
> | gyárak/integrationRuntimes | Nem |Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |
> | accounts/dataLakeStoreAccounts | Nem |Nem |
> | fiókok/storageAccounts | Nem |Nem |
> | fiókok/storageAccounts/tárolók | Nem |Nem |
> | fiókok/transferAnalyticsUnits | Nem |Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |
> | fiókok/eventGridFilters | Nem |Nem |
> | fiókok/firewallRules | Nem |Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | services | Nem |Nem |
> | szolgáltatások/projektek | Nem |Nem |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |
> | fiókok/megosztások | Nem |Nem |
> | fiókok/megosztások/adatkészletek | Nem |Nem |
> | fiókok/megosztások/meghívók | Nem |Nem |
> | fiókok/megosztások/providersharesubscriptions | Nem |Nem |
> | fiókok/megosztások/synchronizationSettings | Nem |Nem |
> | fiókok/sharesubscriptions | Nem |Nem |
> | fiókok/sharesubscriptions/consumerSourceDataSets | Nem |Nem |
> | fiókok/sharesubscriptions/datasetmappings | Nem |Nem |
> | fiókok/sharesubscriptions/eseményindítók | Nem |Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kiszolgáló | Igen |Igen |
> | kiszolgálók/tanácsadók | Nem |Nem |
> | kiszolgálók/queryTexts | Nem |Nem |
> | kiszolgálók/recoverableServers | Nem |Nem |
> | kiszolgálók/topQueryStatistics | Nem |Nem |
> | servers/virtualNetworkRules | Nem |Nem |
> | kiszolgálók/waitStatistics | Nem |Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kiszolgáló | Igen |Igen |
> | kiszolgálók/tanácsadók | Nem |Nem |
> | kiszolgálók/queryTexts | Nem |Nem |
> | kiszolgálók/recoverableServers | Nem |Nem |
> | kiszolgálók/topQueryStatistics | Nem |Nem |
> | servers/virtualNetworkRules | Nem |Nem |
> | kiszolgálók/waitStatistics | Nem |Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | serverGroups | Igen |Igen |
> | kiszolgáló | Igen |Igen |
> | kiszolgálók/tanácsadók | Nem |Nem |
> | kiszolgálók/queryTexts | Nem |Nem |
> | kiszolgálók/recoverableServers | Nem |Nem |
> | kiszolgálók/topQueryStatistics | Nem |Nem |
> | servers/virtualNetworkRules | Nem |Nem |
> | kiszolgálók/waitStatistics | Nem |Nem |
> | serversv2 | Igen |Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | artifactSources | Igen |Igen |
> | kibocsátások | Igen |Igen |
> | serviceTopologies | Igen |Igen |
> | serviceTopologies/szolgáltatások | Igen |Igen |
> | serviceTopologies/szolgáltatások/serviceUnits | Igen |Igen |
> | lépések | Igen |Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Igen |Igen |
> | applicationgroups/alkalmazások | Nem |Nem |
> | applicationgroups/startmenuitems | Nem |Nem |
> | hostpools | Igen |Igen |
> | hostpools/sessionhosts | Nem |Nem |
> | hostpools/sessionhosts/usersessions | Nem |Nem |
> | hostpools/usersessions | Nem |Nem |
> | munkaterületek | Igen |Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | elasticPools | Igen |Igen |
> | ElasticPools/IotHubTenants | Igen |Igen |
> | IotHubs | Igen |Igen |
> | IotHubs/eventGridFilters | Nem |Nem |
> | ProvisioningServices | Igen |Igen |
> | használat | Nem |Nem |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | adatcsatornák | Igen |Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tartományvezérlők | Igen |Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | labcenters | Igen |Igen |
> | Labs | Igen |Igen |
> | Labs/környezetek | Igen |Igen |
> | labs/serviceRunners | Igen |Igen |
> | Labor/virtualMachines | Igen |Igen |
> | menetrend | Igen |Igen |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nem |Nem |
> | databaseAccounts | Igen |Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tartományok | Igen |Igen |
> | tartományok/domainOwnershipIdentifiers | Nem |Nem |
> | generateSsoRequest | Nem |Nem |
> | topLevelDomains | Nem |Nem |
> | validateDomainRegistrationInformation | Nem |Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nem |Nem |
> | lcsprojects/clouddeployments | Nem |Nem |
> | lcsprojects/összekötők | Nem |Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen |Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | tartományok | Igen |Igen |
> | tartományok/témakörök | Nem |Nem |
> | eventSubscriptions | Nem |Nem |
> | extensionTopics | Nem |Nem |
> | témakörök | Igen |Igen |
> | topicTypes | Nem |Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen |Igen |
> | névterek | Igen |Igen |
> | névterek/engedélyezési szabályok | Nem |Nem |
> | névterek/disasterrecoveryconfigs | Nem |Nem |
> | névterek/eventhubs | Nem |Nem |
> | névterek/eventhubs/engedélyezési szabályok | Nem |Nem |
> | névterek/eventhubs/consumergroups | Nem |Nem |
> | névterek/networkrulesets | Nem |Nem |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | funkciókkal | Nem |Nem |
> | Szolgáltatók | Nem |Nem |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | beiratkozik | Nem |Nem |
> | galleryitems | Nem |Nem |
> | generateartifactaccessuri | Nem |Nem |
> | myareas | Nem |Nem |
> | myareas/területek | Nem |Nem |
> | myareas/területek/területek | Nem |Nem |
> | myareas/területek/területek/galleryitems | Nem |Nem |
> | myareas/területek/galleryitems | Nem |Nem |
> | myareas/galleryitems | Nem |Nem |
> | Regisztráció | Nem |Nem |
> | erőforrások | Nem |Nem |
> | retrieveresourcesbyid | Nem |Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | autoManagedVmConfigurationProfiles | Igen |Igen |
> | guestConfigurationAssignments | Nem |Nem |
> | Szoftver | Nem |Nem |
> | softwareUpdateProfile | Nem |Nem |
> | softwareUpdates | Nem |Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Igen |Igen |
> | sapMonitors | Igen |Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Igen |Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen |Igen |
> | fürtök/alkalmazások | Nem |Nem |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen |Igen |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | gép | Igen |Igen |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dataManagers | Igen |Igen |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Összetevők | Igen |Igen |
> | networkScopes | Igen |Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | feladatok | Igen |Igen |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem |Nem |
> | diagnosticSettingsCategories | Nem |Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nem |Nem |
> | IoTApps | Igen |Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Graph | Igen |Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nem |Nem |
> | hsmPools | Igen |Igen |
> | boltívek | Igen |Igen |
> | tárolók/accessPolicies | Nem |Nem |
> | tárolók/eventGridFilters | Nem |Nem |
> | tárolók/titkok | Nem |Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen |Igen |
> | fürtök/attacheddatabaseconfigurations | Nem |Nem |
> | fürtök/adatbázisok | Nem |Nem |
> | fürtök/adatbázisok/dataconnections | Nem |Nem |
> | fürtök/adatbázisok/eventhubconnections | Nem |Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | labaccounts | Igen |Igen |
> | Felhasználók | Nem |Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Igen |Igen |
> | integrationAccounts | Igen |Igen |
> | integrationServiceEnvironments | Igen |Igen |
> | integrationServiceEnvironments/król | Igen |Igen |
> | isolatedEnvironments | Igen |Igen |
> | munkafolyamatok | Igen |Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Igen |Igen |
> | webServices | Igen |Igen |
> | Munkaterületek | Igen |Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen |Igen |
> | munkaterületek/számítások | Nem |Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Identitások | Nem |Nem |
> | userAssignedIdentities | Igen |Igen |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nem |Nem |
> | registrationAssignments | Nem |Nem |
> | registrationDefinitions | Nem |Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | getEntities | Nem |Nem |
> | managementGroups | Nem |Nem |
> | erőforrások | Nem |Nem |
> | startTenantBackfill | Nem |Nem |
> | tenantBackfillStatus | Nem |Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen |Igen |
> | fiókok/eventGridFilters | Nem |Nem |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kínál | Nem |Nem |
> | offerTypes | Nem |Nem |
> | offerTypes/közzétevők | Nem |Nem |
> | offerTypes/kiadók/ajánlatok | Nem |Nem |
> | offerTypes/kiadók/ajánlatok/csomagok | Nem |Nem |
> | offerTypes/kiadók/ajánlatok/csomagok/szerződések | Nem |Nem |
> | offerTypes/kiadók/ajánlatok/csomagok/konfigurációk | Nem |Nem |
> | offerTypes/publishers/offers/plans/configs/importImage | Nem |Nem |
> | privategalleryitems | Nem |Nem |
> | termék | Nem |Nem |
> | közzétevők | Nem |Nem |
> | kiadók/ajánlatok | Nem |Nem |
> | közzétevők/ajánlatok/módosítások | Nem |Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Igen |Igen |
> | updateCommunicationPreference | Nem |Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | megállapodások | Nem |Nem |
> | offertypes | Nem |Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Mediaservices | Igen |Igen |
> | Mediaservices/accountFilters | Nem |Nem |
> | Mediaservices/-eszközök | Nem |Nem |
> | Mediaservices/eszközök/assetFilters | Nem |Nem |
> | mediaservices/contentKeyPolicies | Nem |Nem |
> | Mediaservices/eventGridFilters | Nem |Nem |
> | Mediaservices/liveEventOperations | Nem |Nem |
> | Mediaservices/liveEvents | Igen |Igen |
> | Mediaservices/liveEvents/liveOutputs | Nem |Nem |
> | Mediaservices/liveOutputOperations | Nem |Nem |
> | Mediaservices/streamingEndpointOperations | Nem |Nem |
> | Mediaservices/streamingEndpoints | Igen |Igen |
> | Mediaservices/streamingLocators | Nem |Nem |
> | Mediaservices/streamingPolicies | Nem |Nem |
> | Mediaservices/átalakítások | Nem |Nem |
> | Mediaservices/átalakítások/feladatok | Nem |Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | appClusters | Igen |Igen |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Igen |Igen |
> | migrateprojects | Igen |Igen |
> | projektek | Igen |Igen |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Igen |Igen |
> | objectUnderstandingAccounts | Igen |Igen |
> | remoteRenderingAccounts | Igen |Igen |
> | spatialAnchorsAccounts | Igen |Igen |
> | surfaceReconstructionAccounts | Igen |Igen |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Igen |Igen |
> | netAppAccounts/capacityPools | Igen |Igen |
> | netAppAccounts/capacityPools/kötetek | Igen |Igen |
> | netAppAccounts/capacityPools/kötetek/mountTargets | Igen |Igen |
> | netAppAccounts/capacityPools/kötetek/Pillanatképek | Igen |Igen |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Igen |Igen |
> | applicationGatewayWebApplicationFirewallPolicies | Igen |Igen |
> | applicationSecurityGroups | Igen |Igen |
> | azureFirewallFqdnTags | Nem |Nem |
> | azureFirewalls | Igen |Igen |
> | bastionHosts | Igen |Igen |
> | bgpServiceCommunities | Nem |Nem |
> | kapcsolatok | Igen |Igen |
> | ddosCustomPolicies | Igen |Igen |
> | ddosProtectionPlans | Igen |Igen |
> | dnsOperationStatuses | Nem |Nem |
> | dnszones | Igen |Igen |
> | dnszones/A | Nem |Nem |
> | dnszones/AAAA | Nem |Nem |
> | dnszones/mind | Nem |Nem |
> | dnszones/CAA | Nem |Nem |
> | dnszones/CNAME | Nem |Nem |
> | dnszones/MX | Nem |Nem |
> | dnszones/NS | Nem |Nem |
> | dnszones/PTR | Nem |Nem |
> | dnszones/recordsets | Nem |Nem |
> | dnszones/SOA | Nem |Nem |
> | dnszones/SRV | Nem |Nem |
> | dnszones/TXT | Nem |Nem |
> | expressRouteCircuits | Igen |Igen |
> | expressRouteCrossConnections | Igen |Igen |
> | expressRouteGateways | Igen |Igen |
> | expressRoutePorts | Igen |Igen |
> | expressRouteServiceProviders | Nem |Nem |
> | firewallPolicies | Igen |Igen |
> | frontdoorok | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) |Igen |
> | frontdoorWebApplicationFirewallManagedRuleSets | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) |Nem |
> | frontdoorWebApplicationFirewallPolicies | Igen, de korlátozott (lásd az [alábbi megjegyzést](#frontdoor)) |Igen |
> | getDnsResourceReference | Nem |Nem |
> | internalNotify | Nem |Nem |
> | loadBalancers | Igen |Nem |
> | localNetworkGateways | Igen |Igen |
> | natGateways | Igen |Igen |
> | networkIntentPolicies | Igen |Igen |
> | networkInterfaces | Igen |Igen |
> | networkProfiles | Igen |Igen |
> | networkSecurityGroups | Igen |Igen |
> | networkWatchers | Igen |Nem |
> | networkWatchers/connectionMonitors | Igen |Nem |
> | networkWatchers/objektívek | Igen |Nem |
> | networkWatchers/pingMeshes | Igen |Nem |
> | p2sVpnGateways | Igen |Igen |
> | privateDnsOperationStatuses | Nem |Nem |
> | privateDnsZones | Igen |Igen |
> | privateDnsZones/A | Nem |Nem |
> | privateDnsZones/AAAA | Nem |Nem |
> | privateDnsZones/mind | Nem |Nem |
> | privateDnsZones/CNAME | Nem |Nem |
> | privateDnsZones/MX | Nem |Nem |
> | privateDnsZones/PTR | Nem |Nem |
> | privateDnsZones/SOA | Nem |Nem |
> | privateDnsZones/SRV | Nem |Nem |
> | privateDnsZones/TXT | Nem |Nem |
> | privateDnsZones/virtualNetworkLinks | Igen |Igen |
> | privateEndpoints | Igen |Igen |
> | privateLinkServices | Igen |Igen |
> | publicIPAddresses | Igen |Igen |
> | publicIPPrefixes | Igen |Igen |
> | routeFilters | Igen |Igen |
> | routeTables | Igen |Igen |
> | secureGateways | Igen |Igen |
> | serviceEndpointPolicies | Igen |Igen |
> | trafficManagerGeographicHierarchies | Nem |Nem |
> | trafficmanagerprofiles | Igen |Igen |
> | trafficmanagerprofiles/heatMaps | Nem |Nem |
> | trafficManagerUserMetricsKeys | Nem |Nem |
> | virtualHubs | Igen |Igen |
> | virtualNetworkGateways | Igen |Igen |
> | virtualNetworks | Igen |Igen |
> | virtualNetworkTaps | Igen |Igen |
> | virtualWans | Igen |Igen |
> | vpnGateways | Igen |Nem |
> | vpnSites | Igen |Igen |
> | webApplicationFirewallPolicies | Igen |Igen |

<a id="frontdoor" />

> [!NOTE]
> Az Azure bejárati szolgáltatásához címkéket alkalmazhat az erőforrás létrehozásakor, de a címkék frissítése vagy hozzáadása jelenleg nem támogatott.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen |Nem |
> | névterek/notificationHubs | Igen |Nem |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Igen |Igen |
> | ImportSites | Igen |Igen |
> | ServerSites | Igen |Igen |
> | VMwareSites | Igen |Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fürtök | Igen |Igen |
> | eszközök | Nem |Nem |
> | linkTargets | Nem |Nem |
> | storageInsightConfigs | Nem |Nem |
> | munkaterületek | Igen |Igen |
> | munkaterületek/adatforrások | Nem |Nem |
> | workspaces/linkedServices | Nem |Nem |
> | munkaterületek/lekérdezés | Nem |Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nem |Nem |
> | managementconfigurations | Igen |Igen |
> | megoldások | Igen |Igen |
> | megtekintés | Igen |Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nem |Nem |
> | peerAsns | Nem |Nem |
> | társviszonyok | Igen |Igen |
> | peeringServiceProviders | Nem |Nem |
> | peeringServices | Igen |Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nem |Nem |
> | policyStates | Nem |Nem |
> | policyTrackedResources | Nem |Nem |
> | szervizelések | Nem |Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | konzolok | Nem |Nem |
> | Irányítópultok | Igen |Igen |
> | userSettings | Nem |Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Igen |Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kapacitások | Igen |Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nem |Nem |
> | boltívek | Igen |Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen |Igen |
> | névterek/engedélyezési szabályok | Nem |Nem |
> | névterek/hybridconnections | Nem |Nem |
> | névterek/hybridconnections/engedélyezési szabályok | Nem |Nem |
> | névterek/wcfrelays | Nem |Nem |
> | névterek/wcfrelays/engedélyezési szabályok | Nem |Nem |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Nem |Nem |
> | Gyűjtemények | Igen |Igen |
> | gyűjtemények/alkalmazások | Nem |Nem |
> | gyűjtemények/securityprincipals | Nem |Nem |
> | templateImages | Nem |Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | lekérdezés | Igen |Igen |
> | resourceChangeDetails | Nem |Nem |
> | resourceChanges | Nem |Nem |
> | erőforrások | Nem |Nem |
> | resourcesHistory | Nem |Nem |
> | subscriptionsStatus | Nem |Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nem |Nem |
> | childAvailabilityStatuses | Nem |Nem |
> | childResources | Nem |Nem |
> | események | Nem |Nem |
> | impactedResources | Nem |Nem |
> | metaadatok | Nem |Nem |
> | értesítések | Nem |Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | üzemelő példányok | Igen |Nem |
> | üzembe helyezések/műveletek | Nem |Nem |
> | Linkek | Nem |Nem |
> | notifyResourceJobs | Nem |Nem |
> | Szolgáltatók | Nem |Nem |
> | resourceGroups | Igen |Nem |
> | erőforrások | Nem |Nem |
> | előfizetés | Nem |Nem |
> | előfizetések/szolgáltatók | Nem |Nem |
> | előfizetések/erőforrások | Nem |Nem |
> | előfizetések/tagNames | Nem |Nem |
> | előfizetések/tagNames/tagValues | Nem |Nem |
> | bérlők | Nem |Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen |Igen |
> | saasresources | Nem |Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | feladatgyűjtemények | Igen |Igen |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nem |Nem |
> | searchServices | Igen |Igen |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nem |Nem |
> | advancedThreatProtectionSettings | Nem |Nem |
> | riasztások | Nem |Nem |
> | allowedConnections | Nem |Nem |
> | applicationWhitelistings | Nem |Nem |
> | assessmentMetadata | Nem |Nem |
> | értékelések | Nem |Nem |
> | AutoProvisioningSettings | Nem |Nem |
> | Felelésről | Nem |Nem |
> | dataCollectionAgents | Nem |Nem |
> | deviceSecurityGroups | Nem |Nem |
> | discoveredSecuritySolutions | Nem |Nem |
> | externalSecuritySolutions | Nem |Nem |
> | InformationProtectionPolicies | Nem |Nem |
> | iotSecuritySolutions | Igen |Igen |
> | iotSecuritySolutions/analyticsModels | Nem |Nem |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nem |Nem |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nem |Nem |
> | jitNetworkAccessPolicies | Nem |Nem |
> | networkData | Nem |Nem |
> | playbookConfigurations | Igen |Igen |
> | házirendek | Nem |Nem |
> | pricings | Nem |Nem |
> | regulatoryComplianceStandards | Nem |Nem |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nem |Nem |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nem |Nem |
> | securityContacts | Nem |Nem |
> | securitySolutions | Nem |Nem |
> | securitySolutionsReferenceData | Nem |Nem |
> | securityStatuses | Nem |Nem |
> | securityStatusesSummaries | Nem |Nem |
> | serverVulnerabilityAssessments | Nem |Nem |
> | beállítások | Nem |Nem |
> | feladatok | Nem |Nem |
> | topológiák | Nem |Nem |
> | workspaceSettings | Nem |Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem |Nem |
> | diagnosticSettingsCategories | Nem |Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | összesítések | Nem |Nem |
> | Riasztási szabályok | Nem |Nem |
> | alertRuleTemplates | Nem |Nem |
> | Könyvjelzők | Nem |Nem |
> | esetekben | Nem |Nem |
> | dataConnectors | Nem |Nem |
> | entitás | Nem |Nem |
> | entityQueries | Nem |Nem |
> | officeConsents | Nem |Nem |
> | beállítások | Nem |Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | névterek | Igen |Nem |
> | névterek/engedélyezési szabályok | Nem |Nem |
> | névterek/disasterrecoveryconfigs | Nem |Nem |
> | névterek/eventgridfilters | Nem |Nem |
> | névterek/networkrulesets | Nem |Nem |
> | névterek/várólisták | Nem |Nem |
> | névterek/várólisták/engedélyezési szabályok | Nem |Nem |
> | névterek/témakörök | Nem |Nem |
> | névterek/témakörök/engedélyezési szabályok | Nem |Nem |
> | névterek/témakörök/előfizetések | Nem |Nem |
> | névterek/témakörök/előfizetések/szabályok | Nem |Nem |
> | premiumMessagingRegions | Nem |Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen |Igen |
> | fürtök | Igen |Igen |
> | fürtök/alkalmazások | Nem |Nem |
> | containerGroups | Igen |Igen |
> | containerGroupSets | Igen |Igen |
> | edgeclusters | Igen |Igen |
> | edgeclusters/alkalmazások | Nem |Nem |
> | hálózatok | Igen |Igen |
> | secretstores | Igen |Igen |
> | secretstores/tanúsítványok | Nem |Nem |
> | secretstores/titkok | Nem |Nem |
> | kötetek | Igen |Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen |Igen |
> | containerGroups | Igen |Igen |
> | átjárók | Igen |Igen |
> | hálózatok | Igen |Igen |
> | titkos kódok | Igen |Igen |
> | kötetek | Igen |Igen |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nem |Nem |
> | providerRegistrations/resourceTypeRegistrations | Nem |Nem |
> | kibocsátások | Igen |Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | SignalR | Igen |Igen |
> | Jelző/eventGridFilters | Nem |Nem |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Igen |Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nem |Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Igen |Igen |
> | alkalmazások | Igen |Igen |
> | jitRequests | Igen |Igen |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | managedInstances | Igen | Igen |
> | managedInstances/adatbázisok | Igen (lásd az [alábbi megjegyzést](#sqlnote)) | Igen |
> | managedInstances/databases/backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Nem | Nem |
> | managedInstances/databases/vulnerabilityAssessments | Nem | Nem |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Nem | Nem |
> | managedInstances/encryptionProtector | Nem | Nem |
> | managedInstances/keys | Nem | Nem |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances/vulnerabilityAssessments | Nem | Nem |
> | kiszolgáló | Igen | Igen |
> | kiszolgálók/rendszergazdák | Nem | Nem |
> | kiszolgálók/communicationLinks | Nem | Nem |
> | kiszolgálók/adatbázisok | Igen (lásd az [alábbi megjegyzést](#sqlnote)) | Igen |
> | servers/encryptionProtector | Nem | Nem |
> | kiszolgálók/firewallRules | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | servers/restorableDroppedDatabases | Nem | Nem |
> | kiszolgálók/serviceobjectives | Nem | Nem |
> | kiszolgálók/tdeCertificates | Nem | Nem |
> | virtualClusters | Nem | Nem |

<a id="sqlnote" />

> [!NOTE]
> A főadatbázis nem támogatja a címkéket, de más adatbázisokat is, beleértve a Azure SQL Data Warehouse-adatbázisokat, a támogatási címkéket. Azure SQL Data Warehouse adatbázisoknak aktív (nem szüneteltetett) állapotban kell lenniük.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Igen |Igen |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Nem |Nem |
> | SqlVirtualMachines | Igen |Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Igen |Igen |
> | storageAccounts/blobServices | Nem |Nem |
> | storageAccounts/fileServices | Nem |Nem |
> | storageAccounts/queueServices | Nem |Nem |
> | storageAccounts/szolgáltatások | Nem |Nem |
> | storageAccounts/szolgáltatások/metricDefinitions | Nem |Nem |
> | storageAccounts/tableServices | Nem |Nem |
> | használat | Nem |Nem |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | gyorsítótárak | Igen |Igen |
> | gyorsítótárak/storageTargets | Nem |Nem |
> | usageModels | Nem |Nem |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nem |Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen |Igen |
> | storageSyncServices/registeredServers | Nem |Nem |
> | storageSyncServices/syncGroups | Nem |Nem |
> | storageSyncServices/syncGroups/cloudEndpoints | Nem |Nem |
> | storageSyncServices/syncGroups/serverEndpoints | Nem |Nem |
> | storageSyncServices/munkafolyamatok | Nem |Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen |Igen |
> | storageSyncServices/registeredServers | Nem |Nem |
> | storageSyncServices/syncGroups | Nem |Nem |
> | storageSyncServices/syncGroups/cloudEndpoints | Nem |Nem |
> | storageSyncServices/syncGroups/serverEndpoints | Nem |Nem |
> | storageSyncServices/munkafolyamatok | Nem |Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen |Igen |
> | storageSyncServices/registeredServers | Nem |Nem |
> | storageSyncServices/syncGroups | Nem |Nem |
> | storageSyncServices/syncGroups/cloudEndpoints | Nem |Nem |
> | storageSyncServices/syncGroups/serverEndpoints | Nem |Nem |
> | storageSyncServices/munkafolyamatok | Nem |Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | kezelők | Igen |Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Igen (lásd az alábbi megjegyzést) |Igen |

> [!NOTE]
> Nem adhat hozzá címkét, ha a streamingjobs fut. Egy címke hozzáadásához állítsa le az erőforrást.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | mégse | Nem |Nem |
> | CreateSubscription | Nem |Nem |
> | engedélyezése | Nem |Nem |
> | átnevezés | Nem |Nem |
> | SubscriptionDefinitions | Nem |Nem |
> | SubscriptionOperations | Nem |Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | környezetben | Igen |Nem |
> | környezetek/accessPolicies | Nem |Nem |
> | környezetek/eventsources | Igen |Nem |
> | környezetek/referenceDataSets | Igen |Nem |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Igen |Igen |
> | dedicatedCloudServices | Igen |Igen |
> | VirtualMachines | Igen |Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nem |Nem |
> | apiManagementAccounts/apiAcls | Nem |Nem |
> | apiManagementAccounts/apis | Nem |Nem |
> | apiManagementAccounts/apis/apiAcls | Nem |Nem |
> | apiManagementAccounts/apis/connectionAcls | Nem |Nem |
> | apiManagementAccounts/apis/connections | Nem |Nem |
> | apiManagementAccounts/apis/connections/connectionAcls | Nem |Nem |
> | apiManagementAccounts/apis/localizedDefinitions | Nem |Nem |
> | apiManagementAccounts/connectionAcls | Nem |Nem |
> | apiManagementAccounts/connections | Nem |Nem |
> | billingMeters | Nem |Nem |
> | tanúsítványok | Igen |Igen |
> | connectionGateways | Igen |Igen |
> | kapcsolatok | Igen |Igen |
> | customApis | Igen |Igen |
> | deletedSites | Nem |Nem |
> | függvény | Nem |Nem |
> | hostingEnvironments | Igen |Igen |
> | hostingEnvironments/multiRolePools | Nem |Nem |
> | hostingEnvironments/workerPools | Nem |Nem |
> | publishingUsers | Nem |Nem |
> | javaslatok | Nem |Nem |
> | resourceHealthMetadata | Nem |Nem |
> | Runtimes | Nem |Nem |
> | Kiszolgálófarmok | Igen |Igen |
> | Kiszolgálófarmok/eventGridFilters | Nem |Nem |
> | webhelyek | Igen |Igen |
> | helyek/konfiguráció  | Nem | Nem |
> | helyek/eventGridFilters | Nem |Nem |
> | sites/hostNameBindings | Nem |Nem |
> | helyek/networkConfig | Nem |Nem |
> | helyek/premieraddons | Igen |Igen |
> | helyek/bővítőhelyek | Igen |Igen |
> | helyek/bővítőhelyek/eventGridFilters | Nem |Nem |
> | sites/slots/hostNameBindings | Nem |Nem |
> | helyek/bővítőhelyek/networkConfig | Nem |Nem |
> | sourceControls | Nem |Nem |
> | érvényesít | Nem |Nem |
> | verifyHostingEnvironmentVnet | Nem |Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem |Nem |
> | diagnosticSettingsCategories | Nem |Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Igen |Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Címkék támogatása | Címke a Cost jelentésben |
> | ------------- | ----------- | ----------- |
> | Összetevők | Nem |Nem |
> | componentsSummary | Nem |Nem |
> | monitorInstances | Nem |Nem |
> | monitorInstancesSummary | Nem |Nem |
> | figyeli | Nem |Nem |
> | notificationSettings | Nem |Nem |

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan alkalmazhat címkéket az erőforrásokra, tekintse meg [a címkék használata az Azure-erőforrások rendszerezéséhez](resource-group-using-tags.md)című témakört.
