---
title: Azure Resource Manager teljes üzemmódú törlés
description: Azt mutatja, hogy az erőforrástípusok hogyan kezelik a teljes módú törlést Azure Resource Manager-sablonokban.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: tomfitz
ms.openlocfilehash: de1b5080e72f79626ca0c749efe4122721f14922
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528583"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Azure-erőforrások törlése a teljes módú üzembe helyezésekhez

Ez a cikk azt ismerteti, hogyan kezeli az erőforrástípusok a törlést, ha nem a teljes módban telepített sablonban vannak.

Az **Igen** értékkel jelölt erőforrástípusok törlődnek, ha a típus nincs a teljes móddal üzembe helyezett sablonban.

A **nem** a (z) jelölésű erőforrástípusok nem törlődnek automatikusan, ha nem a sablonban; azonban törlődnek, ha a szülő erőforrás törölve lett. A viselkedés teljes leírását lásd: [Azure Resource Manager telepítési módok](deployment-modes.md).

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft. HRE](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. igazolás](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Genf](#microsoftazuregeneva)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. számítás](#microsoftcompute)
> - [Microsoft. felhasználás](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. Tárolószolgáltatás](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. Data Lake Store](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kulcstartó](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. előfizetés](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DomainServices | Igen |
> | DomainServices/oucontainer | Nem |
> | DomainServices/ReplicaSets | Igen |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | supportProviders | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | aadsupportcases | Nem |
> | addsservices | Nem |
> | ügynökök | Nem |
> | anonymousapiusers | Nem |
> | konfiguráció | Nem |
> | naplók | Nem |
> | Jelentések | Nem |
> | servicehealthmetrics | Nem |
> | services | Nem |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Konfigurációk | Nem |
> | generateRecommendations | Nem |
> | metaadatok | Nem |
> | Javaslatok | Nem |
> | fóliakondenzát | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | actionRules | Igen |
> | riasztások | Nem |
> | alertsList | Nem |
> | alertsMetaData | Nem |
> | alertsSummary | Nem |
> | alertsSummaryList | Nem |
> | Visszajelzés | Nem |
> | smartDetectorAlertRules | Igen |
> | smartDetectorRuntimeEnvironments | Nem |
> | smartGroups | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | reportFeedback | Nem |
> | szolgáltatás | Igen |
> | validateServiceName | Nem |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores | Igen |
> | configurationStores/eventGridFilters | Nem |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Spring | Igen |

## <a name="microsoftattestation"></a>Microsoft. igazolás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | attestationProviders | Nem |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicAdministrators | Nem |
> | dataAliases | Nem |
> | denyAssignments | Nem |
> | elevateAccess | Nem |
> | zárak | Nem |
> | Engedélyek | Nem |
> | policyAssignments | Nem |
> | policyDefinitions | Nem |
> | policySetDefinitions | Nem |
> | providerOperations | Nem |
> | roleAssignments | Nem |
> | roleDefinitions | Nem |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | automationAccounts | Igen |
> | automationAccounts/konfigurációk | Igen |
> | automationAccounts/feladatok | Nem |
> | automationAccounts/runbookok | Igen |
> | automationAccounts/softwareUpdateConfigurations | Nem |
> | automationAccounts/webhookok | Nem |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores | Igen |
> | configurationStores/eventGridFilters | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Genf

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | környezetben | Nem |
> | környezetek/fiókok | Nem |
> | környezetek/fiókok/névterek | Nem |
> | környezetek/fiókok/névterek/konfigurációk | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | b2cDirectories | Igen |
> | b2ctenants | Nem |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hybridDataManagers | Igen |
> | postgresInstances | Igen |
> | sqlBigDataClusters | Igen |
> | sqlInstances | Igen |
> | sqlServerRegistrations | Igen |
> | sqlServerRegistrations/sqlServers | Nem |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | regisztrációk | Igen |
> | regisztrációk/customerSubscriptions | Nem |
> | regisztrációk/termékek | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | batchAccounts | Igen |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | billingAccounts | Nem |
> | billingAccounts/szerződések | Nem |
> | billingAccounts/billingPermissions | Nem |
> | billingAccounts/billingProfiles | Nem |
> | billingAccounts/billingProfiles/billingPermissions | Nem |
> | billingAccounts/billingProfiles/billingRoleAssignments | Nem |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Nem |
> | billingAccounts/billingProfiles/billingSubscriptions | Nem |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Nem |
> | billingAccounts/billingProfiles/ügyfelek | Nem |
> | billingAccounts/billingProfiles/számlák | Nem |
> | billingAccounts/billingProfiles/számlák/árlista | Nem |
> | billingAccounts/billingProfiles/invoiceSections | Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Nem |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Nem |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Nem |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek | Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek/átvitel | Nem |
> | billingAccounts/billingProfiles/invoiceSections/termékek/updateAutoRenew | Nem |
> | billingAccounts/billingProfiles/invoiceSections/tranzakciók | Nem |
> | billingAccounts/billingProfiles/invoiceSections/Transfers | Nem |
> | billingAccounts/BillingProfiles/patchOperations | Nem |
> | billingAccounts/billingProfiles/paymentMethods | Nem |
> | billingAccounts/billingProfiles/házirendek | Nem |
> | billingAccounts/billingProfiles/árlista | Nem |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Nem |
> | billingAccounts/billingProfiles/termékek | Nem |
> | billingAccounts/billingProfiles/tranzakciók | Nem |
> | billingAccounts/billingRoleAssignments | Nem |
> | billingAccounts/billingRoleDefinitions | Nem |
> | billingAccounts/billingSubscriptions | Nem |
> | billingAccounts/createBillingRoleAssignment | Nem |
> | billingAccounts/createInvoiceSectionOperations | Nem |
> | billingAccounts/ügyfelek | Nem |
> | billingAccounts/ügyfelek/billingSubscriptions | Nem |
> | billingAccounts/ügyfelek/initiateTransfer | Nem |
> | billingAccounts/ügyfelek/szabályzatok | Nem |
> | billingAccounts/ügyfelek/termékek | Nem |
> | billingAccounts/ügyfelek/tranzakciók | Nem |
> | billingAccounts/ügyfelek/átvitelek | Nem |
> | billingAccounts/részlegek | Nem |
> | billingAccounts/enrollmentAccounts | Nem |
> | billingAccounts/számlák | Nem |
> | billingAccounts/invoiceSections | Nem |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Nem |
> | billingAccounts/invoiceSections/billingSubscriptions | Nem |
> | billingAccounts/invoiceSections/billingSubscriptions/átvitel | Nem |
> | billingAccounts/invoiceSections/Jogosultságszint-emelés | Nem |
> | billingAccounts/invoiceSections/initiateTransfer | Nem |
> | billingAccounts/invoiceSections/patchOperations | Nem |
> | billingAccounts/invoiceSections/productMoveOperations | Nem |
> | billingAccounts/invoiceSections/termékek | Nem |
> | billingAccounts/invoiceSections/termékek/átvitel | Nem |
> | billingAccounts/invoiceSections/termékek/updateAutoRenew | Nem |
> | billingAccounts/invoiceSections/tranzakciók | Nem |
> | billingAccounts/invoiceSections/átvitel | Nem |
> | billingAccounts/lineOfCredit | Nem |
> | billingAccounts/patchOperations | Nem |
> | billingAccounts/paymentMethods | Nem |
> | billingAccounts/termékek | Nem |
> | billingAccounts/tranzakciók | Nem |
> | billingPeriods | Nem |
> | billingPermissions | Nem |
> | billingProperty | Nem |
> | billingRoleAssignments | Nem |
> | billingRoleDefinitions | Nem |
> | createBillingRoleAssignment | Nem |
> | részlegek | Nem |
> | enrollmentAccounts | Nem |
> | számlák | Nem |
> | transzferek | Nem |
> | átvitelek/acceptTransfer | Nem |
> | átvitelek/declineTransfer | Nem |
> | átvitelek/operationStatus | Nem |
> | átvitelek/validateTransfer | Nem |
> | validateAddress | Nem |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mapApis | Igen |
> | updateCommunicationPreference | Nem |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blockchainMembers | Igen |
> | néző | Igen |

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
> | Nyelvek | Nem |
> | sablonok | Nem |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Redis | Igen |
> | RedisConfigDefinition | Nem |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appliedReservations | Nem |
> | calculateExchange | Nem |
> | calculatePrice | Nem |
> | calculatePurchasePrice | Nem |
> | katalógusok | Nem |
> | commercialReservationOrders | Nem |
> | Exchange | Nem |
> | placePurchaseOrder | Nem |
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

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem |
> | CdnWebApplicationFirewallPolicies | Igen |
> | edgenodes | Nem |
> | profilok | Igen |
> | profilok/végpontok | Igen |
> | profilok/végpontok/customdomains | Nem |
> | profilok/végpontok/eredetek | Nem |
> | validateProbe | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

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
> | Képességek | Nem |
> | Tartománynevek | Igen |
> | Tartománynevek/képességek | Nem |
> | Tartománynevek/internalLoadBalancers | Nem |
> | Tartománynevek/serviceCertificates | Nem |
> | Tartománynevek/tárolóhelyek | Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök | Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök/metricDefinitions | Nem |
> | Tartománynevek/bővítőhelyek/szerepkörök/mérőszámok | Nem |
> | moveSubscriptionResources | Nem |
> | operatingSystemFamilies | Nem |
> | operatingSystems | Nem |
> | kvóták | Nem |
> | resourceTypes | Nem |
> | validateSubscriptionMoveAvailability | Nem |
> | VirtualMachines | Igen |
> | virtualMachines/diagnosticSettings | Nem |
> | virtualMachines/metricDefinitions | Nem |
> | virtualMachines/mérőszámok | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Képességek | Nem |
> | expressRouteCrossConnections | Nem |
> | expressRouteCrossConnections/társak | Nem |
> | gatewaySupportedDevices | Nem |
> | networkSecurityGroups | Igen |
> | kvóták | Nem |
> | reservedIps | Igen |
> | virtualNetworks | Igen |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nem |
> | virtualNetworks/virtualNetworkPeerings | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Képességek | Nem |
> | lemezek | Nem |
> | images | Nem |
> | osImages | Nem |
> | osPlatformImages | Nem |
> | publicImages | Nem |
> | kvóták | Nem |
> | storageAccounts | Igen |
> | storageAccounts/blobServices | Nem |
> | storageAccounts/fileServices | Nem |
> | storageAccounts/metricDefinitions | Nem |
> | storageAccounts/mérőszámok | Nem |
> | storageAccounts/queueServices | Nem |
> | storageAccounts/szolgáltatások | Nem |
> | storageAccounts/szolgáltatások/diagnosticSettings | Nem |
> | storageAccounts/szolgáltatások/metricDefinitions | Nem |
> | storageAccounts/szolgáltatások/mérőszámok | Nem |
> | storageAccounts/tableServices | Nem |
> | storageAccounts/lemezképet | Nem |
> | Lemezképet | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

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
> | diskEncryptionSets | Igen |
> | lemezek | Igen |
> | katalógusok | Igen |
> | galériák/alkalmazások | Nem |
> | galériák/alkalmazások/verziók | Nem |
> | galériák/lemezképek | Nem |
> | galériák/lemezképek/verziók | Nem |
> | hostGroups | Igen |
> | hostGroups/gazdagépek | Igen |
> | images | Igen |
> | proximityPlacementGroups | Igen |
> | restorePointCollections | Igen |
> | restorePointCollections/restorePoints | Nem |
> | sharedVMImages | Igen |
> | sharedVMImages/verziók | Nem |
> | pillanatképek | Igen |
> | VirtualMachines | Igen |
> | virtualMachines/bővítmények | Igen |
> | virtualMachines/metricDefinitions | Nem |
> | virtualMachineScaleSets | Igen |
> | virtualMachineScaleSets/bővítmények | Nem |
> | virtualMachineScaleSets/networkInterfaces | Nem |
> | virtualMachineScaleSets/nyilvános IP | Nem |
> | virtualMachineScaleSets/virtualMachines | Nem |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Nem |

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | AggregatedCost | Nem |
> | Egyenlegek | Nem |
> | Költségkeret | Nem |
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
> | Címkék | Nem |
> | Bérlők | Nem |
> | Fogalmak | Nem |
> | UsageDetails | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerGroups | Igen |
> | serviceAssociationLinks | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kibocsátásiegység | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildek | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/Mégse | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/getLogLink | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/eventGridFilters | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/getBuildSourceUploadUrl | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/GetCredentials | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/importImage | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/queueBuild | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredential | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regenerateCredentials | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/futtatások | Nem |
> | nyilvántartások/futtatások/megszakítás | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/scheduleRun | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/updatePolicies | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/getCallbackConfig | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/ping | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerServices | Igen |
> | managedClusters | Igen |
> | openShiftManagedClusters | Igen |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | updateCommunicationPreference | Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Értesítések | Nem |
> | billingAccounts | Nem |
> | Költségkeret | Nem |
> | CloudConnectors | Nem |
> | Összekötők | Igen |
> | részlegek | Nem |
> | Dimenziók | Nem |
> | enrollmentAccounts | Nem |
> | Export | Nem |
> | ExternalBillingAccounts | Nem |
> | ExternalBillingAccounts/riasztások | Nem |
> | ExternalBillingAccounts/méretek | Nem |
> | ExternalBillingAccounts/előrejelzés | Nem |
> | ExternalBillingAccounts/lekérdezés | Nem |
> | ExternalSubscriptions | Nem |
> | ExternalSubscriptions/riasztások | Nem |
> | ExternalSubscriptions/méretek | Nem |
> | ExternalSubscriptions/előrejelzés | Nem |
> | ExternalSubscriptions/lekérdezés | Nem |
> | Időjárás | Nem |
> | Lekérdezés | Nem |
> | Regisztráció | Nem |
> | Reportconfigs | Nem |
> | Jelentések | Nem |
> | Beállítások | Nem |
> | showbackRules | Nem |
> | Nézetek | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kérelmek | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | szövetségek | Nem |
> | resourceProviders | Igen |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Feladatok | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Igen |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | munkaterületek | Igen |
> | munkaterületek/virtualNetworkPeerings | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | katalógusok | Igen |
> | datacatalogs | Igen |
> | datacatalogs/adatforrások | Nem |
> | datacatalogs/adatforrások/vizsgálatok | Nem |
> | datacatalogs/adatforrások/vizsgálatok/adatkészletek | Nem |
> | datacatalogs/adatforrások/vizsgálatok/triggerek | Nem |
> | datacatalogs/scantargets | Nem |
> | datacatalogs/scantargets/adatkészletek | Nem |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataFactories | Igen |
> | dataFactories/diagnosticSettings | Nem |
> | dataFactories/metricDefinitions | Nem |
> | dataFactorySchema | Nem |
> | előállítók | Igen |
> | gyárak/integrationRuntimes | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | fiókok/dataLakeStoreAccounts | Nem |
> | fiókok/storageAccounts | Nem |
> | fiókok/storageAccounts/tárolók | Nem |
> | fiókok/transferAnalyticsUnits | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | fiókok/eventGridFilters | Nem |
> | fiókok/firewallRules | Nem |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |
> | szolgáltatások/projektek | Igen |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | fiókok/megosztások | Nem |
> | fiókok/megosztások/adatkészletek | Nem |
> | fiókok/megosztások/meghívók | Nem |
> | fiókok/megosztások/providersharesubscriptions | Nem |
> | fiókok/megosztások/synchronizationSettings | Nem |
> | fiókok/sharesubscriptions | Nem |
> | fiókok/sharesubscriptions/consumerSourceDataSets | Nem |
> | fiókok/sharesubscriptions/datasetmappings | Nem |
> | fiókok/sharesubscriptions/eseményindítók | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen |
> | kiszolgálók/tanácsadók | Nem |
> | kiszolgálók/queryTexts | Nem |
> | kiszolgálók/recoverableServers | Nem |
> | kiszolgálók/topQueryStatistics | Nem |
> | kiszolgálók/virtualNetworkRules | Nem |
> | kiszolgálók/waitStatistics | Nem |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen |
> | kiszolgálók/tanácsadók | Nem |
> | kiszolgálók/queryTexts | Nem |
> | kiszolgálók/recoverableServers | Nem |
> | kiszolgálók/topQueryStatistics | Nem |
> | kiszolgálók/virtualNetworkRules | Nem |
> | kiszolgálók/waitStatistics | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | serverGroups | Igen |
> | Kiszolgálók | Igen |
> | kiszolgálók/tanácsadók | Nem |
> | kiszolgálók/privateEndpointConnectionProxies | Nem |
> | kiszolgálók/privateEndpointConnections | Nem |
> | kiszolgálók/privateLinkResources | Nem |
> | kiszolgálók/queryTexts | Nem |
> | kiszolgálók/recoverableServers | Nem |
> | kiszolgálók/topQueryStatistics | Nem |
> | kiszolgálók/virtualNetworkRules | Nem |
> | kiszolgálók/waitStatistics | Nem |
> | serversv2 | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | artifactSources | Igen |
> | kibocsátások | Igen |
> | serviceTopologies | Igen |
> | serviceTopologies/szolgáltatások | Igen |
> | serviceTopologies/szolgáltatások/serviceUnits | Igen |
> | lépések | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationgroups | Igen |
> | applicationgroups/alkalmazások | Nem |
> | applicationgroups/startmenuitems | Nem |
> | hostpools | Igen |
> | hostpools/sessionhosts | Nem |
> | hostpools/sessionhosts/usersessions | Nem |
> | hostpools/usersessions | Nem |
> | munkaterületek | Igen |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | ElasticPools | Igen |
> | ElasticPools/IotHubTenants | Igen |
> | IotHubs | Igen |
> | IotHubs/eventGridFilters | Nem |
> | ProvisioningServices | Igen |
> | használat | Nem |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Csővezetékek | Igen |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományvezérlők | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labcenters | Igen |
> | Labs | Igen |
> | Labs/környezetek | Igen |
> | Labor/serviceRunners | Igen |
> | Labor/virtualMachines | Igen |
> | menetrend | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | databaseAccountNames | Nem |
> | databaseAccounts | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | tartományok | Igen |
> | tartományok/domainOwnershipIdentifiers | Nem |
> | generateSsoRequest | Nem |
> | topLevelDomains | Nem |
> | validateDomainRegistrationInformation | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lcsprojects | Nem |
> | lcsprojects/clouddeployments | Nem |
> | lcsprojects/összekötők | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | tartományok | Igen |
> | tartományok/témakörök | Nem |
> | eventSubscriptions | Nem |
> | extensionTopics | Nem |
> | témakörök | Igen |
> | topicTypes | Nem |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen |
> | Névterek | Igen |
> | névterek/engedélyezési szabályok | Nem |
> | névterek/disasterrecoveryconfigs | Nem |
> | névterek/eventhubs | Nem |
> | névterek/eventhubs/engedélyezési szabályok | Nem |
> | névterek/eventhubs/consumergroups | Nem |
> | névterek/networkrulesets | Nem |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | funkciókkal | Nem |
> | szolgáltatók | Nem |

## <a name="microsoftgallery"></a>Microsoft. Gallery

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

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | autoManagedVmConfigurationProfiles | Igen |
> | guestConfigurationAssignments | Nem |
> | szoftver | Nem |
> | softwareUpdateProfile | Nem |
> | softwareUpdates | Nem |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hanaInstances | Igen |
> | sapMonitors | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedicatedHSMs | Igen |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen |
> | fürtök/alkalmazások | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | gépek | Igen |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataManagers | Igen |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összetevők | Igen |
> | networkScopes | Igen |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Feladatok | Igen |

## <a name="microsoftintune"></a>Microsoft. Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem |
> | diagnosticSettingsCategories | Nem |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appTemplates | Nem |
> | IoTApps | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Gráf | Igen |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deletedVaults | Nem |
> | hsmPools | Igen |
> | boltívek | Igen |
> | tárolók/accessPolicies | Nem |
> | tárolók/eventGridFilters | Nem |
> | tárolók/titkok | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen |
> | fürtök/attacheddatabaseconfigurations | Nem |
> | fürtök/adatbázisok | Nem |
> | fürtök/adatbázisok/dataconnections | Nem |
> | fürtök/adatbázisok/eventhubconnections | Nem |
> | fürtök/sharedidentities | Nem |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labaccounts | Igen |
> | felhasználó | Nem |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hostingEnvironments | Igen |
> | integrationAccounts | Igen |
> | integrationServiceEnvironments | Igen |
> | integrationServiceEnvironments/król | Igen |
> | isolatedEnvironments | Igen |
> | munkafolyamatok | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | commitmentPlans | Igen |
> | webServices | Igen |
> | Munkaterületek | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | munkaterületek | Igen |
> | munkaterületek/számítások | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Identitások | Nem |
> | userAssignedIdentities | Igen |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nem |
> | registrationAssignments | Nem |
> | registrationDefinitions | Nem |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | getEntities | Nem |
> | managementGroups | Nem |
> | Erőforrások | Nem |
> | startTenantBackfill | Nem |
> | tenantBackfillStatus | Nem |

## <a name="microsoftmaps"></a>Microsoft. Maps

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
> | offerTypes/kiadók/ajánlatok/csomagok/konfigurációk/importImage | Nem |
> | privategalleryitems | Nem |
> | termékek | Nem |
> | közzétevők | Nem |
> | kiadók/ajánlatok | Nem |
> | közzétevők/ajánlatok/módosítások | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicDevServices | Igen |
> | updateCommunicationPreference | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | megállapodások | Nem |
> | offertypes | Nem |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Mediaservices | Igen |
> | Mediaservices/accountFilters | Nem |
> | Mediaservices/-eszközök | Nem |
> | Mediaservices/eszközök/assetFilters | Nem |
> | Mediaservices/contentKeyPolicies | Nem |
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

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appClusters | Igen |

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | assessmentProjects | Igen |
> | migrateprojects | Igen |
> | projektek | Igen |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Igen |
> | objectUnderstandingAccounts | Igen |
> | remoteRenderingAccounts | Igen |
> | spatialAnchorsAccounts | Igen |
> | surfaceReconstructionAccounts | Igen |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | netAppAccounts | Igen |
> | netAppAccounts/backupPolicies | Igen |
> | netAppAccounts/capacityPools | Igen |
> | netAppAccounts/capacityPools/kötetek | Igen |
> | netAppAccounts/capacityPools/kötetek/biztonsági másolatok | Nem |
> | netAppAccounts/capacityPools/kötetek/mountTargets | Igen |
> | netAppAccounts/capacityPools/kötetek/Pillanatképek | Igen |
> | netAppAccounts/-tárolók | Nem |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationGateways | Igen |
> | applicationGatewayWebApplicationFirewallPolicies | Igen |
> | applicationSecurityGroups | Igen |
> | azureFirewallFqdnTags | Nem |
> | azureFirewalls | Igen |
> | bastionHosts | Igen |
> | bgpServiceCommunities | Nem |
> | Kapcsolatok | Igen |
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
> | dnszones/rekordhalmazok | Nem |
> | dnszones/SOA | Nem |
> | dnszones/SRV | Nem |
> | dnszones/TXT | Nem |
> | expressRouteCircuits | Igen |
> | expressRouteCrossConnections | Igen |
> | expressRouteGateways | Igen |
> | expressRoutePorts | Igen |
> | expressRouteServiceProviders | Nem |
> | firewallPolicies | Igen |
> | frontdoors | Igen |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nem |
> | frontdoorWebApplicationFirewallPolicies | Igen |
> | getDnsResourceReference | Nem |
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
> | p2sVpnGateways | Igen |
> | privateDnsOperationStatuses | Nem |
> | privateDnsZones | Igen |
> | privateDnsZones/A | Nem |
> | privateDnsZones/AAAA | Nem |
> | privateDnsZones/mind | Nem |
> | privateDnsZones/CNAME | Nem |
> | privateDnsZones/MX | Nem |
> | privateDnsZones/PTR | Nem |
> | privateDnsZones/SOA | Nem |
> | privateDnsZones/SRV | Nem |
> | privateDnsZones/TXT | Nem |
> | privateDnsZones/virtualNetworkLinks | Igen |
> | privateEndpoints | Igen |
> | privateLinkServices | Igen |
> | Nyilvános IP | Igen |
> | publicIPPrefixes | Igen |
> | routeFilters | Igen |
> | routeTables | Igen |
> | secureGateways | Igen |
> | serviceEndpointPolicies | Igen |
> | trafficManagerGeographicHierarchies | Nem |
> | trafficmanagerprofiles | Igen |
> | trafficmanagerprofiles/Intenzitástérképei | Nem |
> | trafficManagerUserMetricsKeys | Nem |
> | virtualHubs | Igen |
> | virtualNetworkGateways | Igen |
> | virtualNetworks | Igen |
> | virtualNetworkTaps | Igen |
> | virtualWans | Igen |
> | vpnGateways | Igen |
> | vpnSites | Igen |
> | webApplicationFirewallPolicies | Igen |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Igen |
> | névterek/notificationHubs | Igen |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | osNamespaces | Igen |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | HyperVSites | Igen |
> | ImportSites | Igen |
> | ServerSites | Igen |
> | VMwareSites | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fürtök | Igen |
> | eszközök | Nem |
> | linkTargets | Nem |
> | storageInsightConfigs | Nem |
> | munkaterületek | Igen |
> | munkaterületek/adatforrások | Nem |
> | munkaterületek/linkedServices | Nem |
> | munkaterületek/lekérdezés | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managementassociations | Nem |
> | managementconfigurations | Igen |
> | megoldások | Igen |
> | Kilátással | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | legacyPeerings | Nem |
> | peerAsns | Nem |
> | társviszonyok | Igen |
> | peeringServiceProviders | Nem |
> | peeringServices | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | policyEvents | Nem |
> | policyStates | Nem |
> | policyTrackedResources | Nem |
> | szervizelések | Nem |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | konzolok | Nem |
> | Irányítópultok | Igen |
> | userSettings | Nem |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | workspaceCollections | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kapacitások | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | backupProtectedItems | Nem |
> | boltívek | Igen |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Igen |
> | névterek/engedélyezési szabályok | Nem |
> | névterek/hybridconnections | Nem |
> | névterek/hybridconnections/engedélyezési szabályok | Nem |
> | névterek/wcfrelays | Nem |
> | névterek/wcfrelays/engedélyezési szabályok | Nem |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Nem |
> | Gyűjtemények | Igen |
> | gyűjtemények/alkalmazások | Nem |
> | gyűjtemények/securityprincipals | Nem |
> | templateImages | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lekérdezés | Igen |
> | resourceChangeDetails | Nem |
> | resourceChanges | Nem |
> | Erőforrások | Nem |
> | resourcesHistory | Nem |
> | subscriptionsStatus | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilityStatuses | Nem |
> | childAvailabilityStatuses | Nem |
> | childResources | Nem |
> | események | Nem |
> | impactedResources | Nem |
> | metaadatok | Nem |
> | Értesítések | Nem |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Központi telepítések | Nem |
> | üzembe helyezések/műveletek | Nem |
> | linkek | Nem |
> | notifyResourceJobs | Nem |
> | szolgáltatók | Nem |
> | resourceGroups | Nem |
> | Erőforrások | Nem |
> | előfizetések | Nem |
> | előfizetések/szolgáltatók | Nem |
> | előfizetések/resourceGroups | Nem |
> | előfizetések/resourcegroups/erőforrások | Nem |
> | előfizetések/erőforrások | Nem |
> | előfizetések/tagNames | Nem |
> | előfizetések/tagNames/tagValues | Nem |
> | Bérlők | Nem |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | saasresources | Nem |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | feladatgyűjtemények | Igen |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nem |
> | searchServices | Igen |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nem |
> | advancedThreatProtectionSettings | Nem |
> | riasztások | Nem |
> | allowedConnections | Nem |
> | applicationWhitelistings | Nem |
> | assessmentMetadata | Nem |
> | értékelések | Nem |
> | AutoProvisioningSettings | Nem |
> | Felelésről | Nem |
> | dataCollectionAgents | Nem |
> | deviceSecurityGroups | Nem |
> | discoveredSecuritySolutions | Nem |
> | externalSecuritySolutions | Nem |
> | InformationProtectionPolicies | Nem |
> | iotSecuritySolutions | Igen |
> | iotSecuritySolutions/analyticsModels | Nem |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Nem |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Nem |
> | jitNetworkAccessPolicies | Nem |
> | networkData | Nem |
> | playbookConfigurations | Igen |
> | Szabályzatok | Nem |
> | pricings | Nem |
> | regulatoryComplianceStandards | Nem |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Nem |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Nem |
> | securityContacts | Nem |
> | securitySolutions | Nem |
> | securitySolutionsReferenceData | Nem |
> | securityStatuses | Nem |
> | securityStatusesSummaries | Nem |
> | serverVulnerabilityAssessments | Nem |
> | beállítások | Nem |
> | alértékelések | Nem |
> | feladatok | Nem |
> | Topológiák | Nem |
> | workspaceSettings | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem |
> | diagnosticSettingsCategories | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | összesítések | Nem |
> | alertRules | Nem |
> | alertRuleTemplates | Nem |
> | könyvjelzők | Nem |
> | esetekben | Nem |
> | dataConnectors | Nem |
> | szervezetek | Nem |
> | entityQueries | Nem |
> | officeConsents | Nem |
> | beállítások | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Igen |
> | névterek/engedélyezési szabályok | Nem |
> | névterek/disasterrecoveryconfigs | Nem |
> | névterek/eventgridfilters | Nem |
> | névterek/networkrulesets | Nem |
> | névterek/várólisták | Nem |
> | névterek/várólisták/engedélyezési szabályok | Nem |
> | névterek/témakörök | Nem |
> | névterek/témakörök/engedélyezési szabályok | Nem |
> | névterek/témakörök/előfizetések | Nem |
> | névterek/témakörök/előfizetések/szabályok | Nem |
> | premiumMessagingRegions | Nem |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | fürtök | Igen |
> | fürtök/alkalmazások | Nem |
> | containerGroups | Igen |
> | containerGroupSets | Igen |
> | edgeclusters | Igen |
> | edgeclusters/alkalmazások | Nem |
> | hálózatok | Igen |
> | secretstores | Igen |
> | secretstores/tanúsítványok | Nem |
> | secretstores/titkok | Nem |
> | volumes | Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | containerGroups | Igen |
> | átjárók | Igen |
> | hálózatok | Igen |
> | titkok | Igen |
> | volumes | Igen |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | providerRegistrations | Nem |
> | providerRegistrations/resourceTypeRegistrations | Nem |
> | kibocsátások | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SignalR | Igen |
> | Jelző/eventGridFilters | Nem |

## <a name="microsoftsiterecovery"></a>Microsoft. SiteRecovery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SiteRecoveryVault | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hybridUseBenefits | Nem |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationDefinitions | Igen |
> | alkalmazás | Igen |
> | jitRequests | Igen |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managedInstances | Igen |
> | managedInstances/adatbázisok | Igen |
> | managedInstances/adatbázisok/backupShortTermRetentionPolicies | Nem |
> | managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels | Nem |
> | managedInstances/adatbázisok/vulnerabilityAssessments | Nem |
> | managedInstances/adatbázisok/vulnerabilityAssessments/szabályok/alaptervek | Nem |
> | managedInstances/encryptionProtector | Nem |
> | managedInstances/kulcsok | Nem |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Nem |
> | managedInstances/vulnerabilityAssessments | Nem |
> | Kiszolgálók | Igen |
> | kiszolgálók/rendszergazdák | Nem |
> | kiszolgálók/communicationLinks | Nem |
> | kiszolgálók/adatbázisok | Igen |
> | kiszolgálók/encryptionProtector | Nem |
> | kiszolgálók/firewallRules | Nem |
> | kiszolgálók/kulcsok | Nem |
> | kiszolgálók/restorableDroppedDatabases | Nem |
> | kiszolgálók/serviceobjectives | Nem |
> | kiszolgálók/tdeCertificates | Nem |
> | virtualClusters | Nem |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

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
> | storageAccounts/szolgáltatások/metricDefinitions | Nem |
> | storageAccounts/tableServices | Nem |
> | használat | Nem |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | gyorsítótárak | Igen |
> | gyorsítótárak/storageTargets | Nem |
> | usageModels | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | replicationGroups | Nem |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices/registeredServers | Nem |
> | storageSyncServices/syncGroups | Nem |
> | storageSyncServices/syncGroups/cloudEndpoints | Nem |
> | storageSyncServices/syncGroups/serverEndpoints | Nem |
> | storageSyncServices/munkafolyamatok | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices/registeredServers | Nem |
> | storageSyncServices/syncGroups | Nem |
> | storageSyncServices/syncGroups/cloudEndpoints | Nem |
> | storageSyncServices/syncGroups/serverEndpoints | Nem |
> | storageSyncServices/munkafolyamatok | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices/registeredServers | Nem |
> | storageSyncServices/syncGroups | Nem |
> | storageSyncServices/syncGroups/cloudEndpoints | Nem |
> | storageSyncServices/syncGroups/serverEndpoints | Nem |
> | storageSyncServices/munkafolyamatok | Nem |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kezelők | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | streamingjobs | Igen |

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Mégse | Nem |
> | CreateSubscription | Nem |
> | Engedélyezése | Nem |
> | Átnevezése | Nem |
> | SubscriptionDefinitions | Nem |
> | SubscriptionOperations | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | környezetben | Igen |
> | környezetek/accessPolicies | Nem |
> | környezetek/eventsources | Igen |
> | környezetek/referenceDataSets | Igen |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Igen |
> | dedicatedCloudServices | Igen |
> | VirtualMachines | Igen |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | apiManagementAccounts | Nem |
> | apiManagementAccounts/apiAcls | Nem |
> | apiManagementAccounts/API-k | Nem |
> | apiManagementAccounts/API-k/apiAcls | Nem |
> | apiManagementAccounts/API-k/connectionAcls | Nem |
> | apiManagementAccounts/API-k/kapcsolatok | Nem |
> | apiManagementAccounts/API-k/kapcsolatok/connectionAcls | Nem |
> | apiManagementAccounts/API-k/localizedDefinitions | Nem |
> | apiManagementAccounts/connectionAcls | Nem |
> | apiManagementAccounts/kapcsolatok | Nem |
> | billingMeters | Nem |
> | Tanúsítványok | Igen |
> | connectionGateways | Igen |
> | Kapcsolatok | Igen |
> | customApis | Igen |
> | deletedSites | Nem |
> | functions | Nem |
> | hostingEnvironments | Igen |
> | hostingEnvironments/multiRolePools | Nem |
> | hostingEnvironments/workerPools | Nem |
> | publishingUsers | Nem |
> | Javaslatok | Nem |
> | resourceHealthMetadata | Nem |
> | Runtimes | Nem |
> | Kiszolgálófarmok | Igen |
> | Kiszolgálófarmok/eventGridFilters | Nem |
> | helyek | Igen |
> | helyek/konfiguráció  | Nem |
> | helyek/eventGridFilters | Nem |
> | helyek/hostNameBindings | Nem |
> | helyek/networkConfig | Nem |
> | helyek/premieraddons | Igen |
> | helyek/bővítőhelyek | Igen |
> | helyek/bővítőhelyek/eventGridFilters | Nem |
> | helyek/bővítőhelyek/hostNameBindings | Nem |
> | helyek/bővítőhelyek/networkConfig | Nem |
> | sourceControls | Nem |
> | érvényesít | Nem |
> | verifyHostingEnvironmentVnet | Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem |
> | diagnosticSettingsCategories | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DeviceServices | Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összetevők | Nem |
> | componentsSummary | Nem |
> | monitorInstances | Nem |
> | monitorInstancesSummary | Nem |
> | figyeli | Nem |
> | notificationSettings | Nem |

## <a name="next-steps"></a>Következő lépések

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [Complete-Mode-Deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)fájlt.
