---
title: Törlés Teljes mód esetén
description: Bemutatja, hogy az erőforrástípusok hogyan kezelik a teljes módú törlést az Azure Resource Manager-sablonokban.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 5f797974212636460306c6a17869d6b8380545ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664406"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Az Azure-erőforrások törlése a teljes módú telepítésekhez

Ez a cikk azt ismerteti, hogy az erőforrástípusok hogyan kezelik a törlést, ha nincs teljes módban üzembe helyezett sablonban.

Az **Igen** jellel megjelölt erőforrástípusok törlődnek, ha a típus nincs a teljes módban telepített sablonban.

A **Nem** jellel jelölt erőforrástípusok nem törlődnek automatikusan, ha nem a sablonban vannak; azonban törlődnek, ha a fölérendelt erőforrás törlődik. A viselkedés teljes leírását az [Azure Resource Manager telepítési módjai című témakörben talál.](deployment-modes.md)

Ha egy [sablonban egynél több erőforráscsoportra](cross-resource-group-deployment.md)telepíti a központi telepítésben megadott erőforrásokat, törölhetők. A másodlagos erőforráscsoportokban lévő erőforrások nem törlődnek.

Ugrás erőforrásszolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Genf](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Számlázás](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.Tanúsítványregisztráció](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Fogyasztás](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Szolgáltatások](#microsoftfeatures)
> - [Microsoft.Galéria](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestKonfiguráció](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Piactér](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceRendelés](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Tavasz](#microsoftmicroservices4spring)
> - [Microsoft.Áttelepítés](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Társviszony-létesítés](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Keresés](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Megoldások](#microsoftsolutions)
> - [Microsoft.SpoolService](#microsoftspoolservice)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadFigyelő](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományi szolgáltatások | Igen |
> | DomainServices / oucontainer | Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | supportProviders | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | aadsupportcases (ad-támogató esetek) | Nem |
> | hozzátesziszolgáltatások | Nem |
> | Ügynökök | Nem |
> | anonymousapiusers | Nem |
> | konfiguráció | Nem |
> | naplók | Nem |
> | jelentések | Nem |
> | servicehealthmetrics | Nem |
> | services | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Konfigurációk | Nem |
> | javaslatok létrehozása | Nem |
> | metaadatok | Nem |
> | Ajánlások | Nem |
> | elnyomások | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | actionRules (műveletszabály) | Igen |
> | riasztások | Nem |
> | figyelmeztetésekListája | Nem |
> | riasztásokMetaData | Nem |
> | figyelmeztetésekÖsszegzés | Nem |
> | figyelmeztetésekÖsszefoglalólista | Nem |
> | visszajelzés | Nem |
> | smartDetectorAlertRules | Igen |
> | smartDetectorRuntimeKörnyezetek | Nem |
> | intelligens csoportok | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Szerverek | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | reportVisszajelzés | Nem |
> | szolgáltatás | Igen |
> | validateServiceName | Nem |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores (konfigurációRaktárak) | Igen |
> | configurationStores / eventGridFilters | Nem |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Spring | Igen |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | attestationProviders | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | klasszikusRendszergazdák | Nem |
> | dataAliases | Nem |
> | denyAssignments | Nem |
> | magasabb szintű hozzáférés | Nem |
> | findOrphanRoleAssignments | Nem |
> | Zárak | Nem |
> | Engedélyek | Nem |
> | házirend-hozzárendelések | Nem |
> | policyDefinitions (policyDefinitions) | Nem |
> | policySetDefinitions (policySetDefinitions) | Nem |
> | szolgáltatóÜzemeltetés | Nem |
> | szerepkör-hozzárendelések | Nem |
> | roleAssignmentsUsageMetrics | Nem |
> | roleDefinitions (szerepkördefiníciók) | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | automationAccounts | Igen |
> | automationAccounts / konfigurációk | Igen |
> | automationAccounts / munkahelyek | Nem |
> | automationAccounts / privateEndpointConnectionProxies | Nem |
> | automationAccounts / privateEndpointConnections | Nem |
> | automationAccounts / privateLinkResources | Nem |
> | automationAccounts / runbookok | Igen |
> | automationAccounts / softwareUpdateConfigurations | Nem |
> | automationAccounts / webhooks | Nem |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores (konfigurációRaktárak) | Igen |
> | configurationStores / eventGridFilters | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genf

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Környezetben | Nem |
> | környezetek / fiókok | Nem |
> | környezetek / fiókok / névterek | Nem |
> | környezetek / fiókok / névterek / konfigurációk | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | b2cIgazgatók | Igen |
> | b2cbérlők | Nem |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hibridDataManagers | Igen |
> | postgresInstances | Igen |
> | sqlBigDataClusters | Igen |
> | sqlInstances | Igen |
> | sqlServerRegistrations | Igen |
> | sqlServerRegistrations / sqlServers | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Regisztrációk | Igen |
> | regisztrációk / customerSubscriptions | Nem |
> | regisztrációk / termékek | Nem |
> | ellenőrző billentyűk | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | batchAccounts | Igen |

## <a name="microsoftbilling"></a>Microsoft.Számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | számlázási számlák | Nem |
> | billingAccounts / megállapodások | Nem |
> | billingAccounts / billingPermissions | Nem |
> | billingAccounts / billingProfiles | Nem |
> | billingAccounts / billingProfiles / billingPermissions | Nem |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nem |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nem |
> | billingAccounts / billingProfiles / billingSubscriptions | Nem |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nem |
> | billingAccounts / billingProfiles / ügyfelek | Nem |
> | billingAccounts / billingProfiles / utasítások | Nem |
> | billingAccounts / billingProfiles / számlák | Nem |
> | billingAccounts / billingProfiles / számlák / árlap | Nem |
> | billingAccounts / billingProfiles / számlák / tranzakciók | Nem |
> | billingAccounts / billingProfiles / invoiceSections | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nem |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nem |
> | billingAccounts / billingProfiles / invoiceSzakaszok / termékek | Nem |
> | billingAccounts / billingProfiles / számlaSzakaszok / termékek / átutalások | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nem |
> | billingAccounts / billingProfiles / invoiceSections / tranzakciók | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nem |
> | billingAccounts / BillingProfiles / patchOperations | Nem |
> | billingAccounts / billingProfiles / paymentMethods | Nem |
> | billingAccounts / billingProfiles / politikák | Nem |
> | billingAccounts / billingProfiles / árlap | Nem |
> | billingAccounts / billingProfiles / áradatletöltésDownloadOperations | Nem |
> | billingAccounts / billingProfiles / termékek | Nem |
> | billingAccounts / billingProfiles / tranzakciók | Nem |
> | billingAccounts / billingRoleAssignments | Nem |
> | billingAccounts / billingRoleDefinitions | Nem |
> | billingAccounts / billingSubscriptions | Nem |
> | billingAccounts / billingSubscriptions / számlák | Nem |
> | billingAccounts / createBillingRoleAssignment | Nem |
> | billingAccounts / createInvoiceSectionOperations | Nem |
> | billingAccounts / ügyfelek | Nem |
> | billingAccounts / ügyfelek / billingPermissions | Nem |
> | billingAccounts / ügyfelek / billingSubscriptions | Nem |
> | billingAccounts / ügyfelek / kezdeményező | Nem |
> | billingAccounts / ügyfelek / politikák | Nem |
> | billingAccounts / ügyfelek / termékek | Nem |
> | billingAccounts / ügyfelek / tranzakciók | Nem |
> | billingAccounts / ügyfelek / átutalások | Nem |
> | billingAccounts / osztályok | Nem |
> | billingAccounts / enrollmentAccounts | Nem |
> | billingAccounts / számlák | Nem |
> | billingAccounts / számlaSzakaszok | Nem |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nem |
> | billingAccounts / számlaSzakaszok / billingSubscriptions | Nem |
> | billingAccounts / számlaSzakaszok / billingSubscriptions / átvitel | Nem |
> | billingAccounts / számlaSzakaszok / emelik | Nem |
> | billingAccounts / invoiceSections / initiateTransfer | Nem |
> | billingAccounts / számlaSzakaszok / patchOperations | Nem |
> | billingAccounts / számlaSzakaszok / productMoveOperations | Nem |
> | billingAccounts / számlaSzakaszok / termékek | Nem |
> | billingAccounts / számlaSzakaszok / termékek / átutalás | Nem |
> | billingAccounts / számlaSzakaszok / termékek / updateAutoRenew | Nem |
> | billingAccounts / számlaSzakaszok / tranzakciók | Nem |
> | billingAccounts / számlaSzakaszok / átutalások | Nem |
> | billingAccounts / lineOfCredit | Nem |
> | billingAccounts / patchOperations | Nem |
> | billingAccounts / fizetési módszerek | Nem |
> | billingAccounts / termékek | Nem |
> | billingAccounts / tranzakciók | Nem |
> | billingIdőszakok | Nem |
> | számlázásI engedélyek | Nem |
> | billingProperty (számlázási tulajdonság) | Nem |
> | billingRoleAssignments | Nem |
> | billingRoleDefinitions | Nem |
> | createBillingRoleAssignment | Nem |
> | Szervezeti egységek | Nem |
> | enrollmentAccounts fiókok | Nem |
> | Számlák | Nem |
> | Transzferek | Nem |
> | átutalások / acceptTransfer | Nem |
> | átutalások / hanyatlásTransfer | Nem |
> | átvitelek / operationStatus | Nem |
> | átvitelek / validateTransfer | Nem |
> | validateAddress | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | térképApik | Igen |
> | updateCommunicationPreference | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blockchainMembers | Igen |
> | cordaMembers | Igen |
> | Watchers | Igen |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blueprintAssignments | Nem |
> | blueprintAssignments / assignmentOperations | Nem |
> | blueprintAssignments / műveletek | Nem |
> | Tervrajz | Nem |
> | tervrajzok / műtárgyak | Nem |
> | tervrajzok / változatok | Nem |
> | tervrajzok / verziók / műtárgyak | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | botServices (botServices) | Igen |
> | botServices / csatornák | Nem |
> | botServices / kapcsolatok | Nem |
> | nyelv | Nem |
> | sablonok | Nem |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Redis | Igen |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appliedReservations | Nem |
> | autoQuotaNövelje | Nem |
> | calculateExchange | Nem |
> | ár számítása | Nem |
> | beszerzési ár számítása | Nem |
> | Katalógusok | Nem |
> | kereskedelmiReservationOrders | Nem |
> | Exchange | Nem |
> | placePurchaseOrder | Nem |
> | reservationOrders (foglalás) | Nem |
> | reservationOrders / calculateRefund | Nem |
> | reservationOrders / egyesítés | Nem |
> | reservationOrders / foglalások | Nem |
> | reservationOrders / fenntartások / revíziók | Nem |
> | reservationOrders / visszaküldés | Nem |
> | reservationOrders / split | Nem |
> | reservationOrders / csere | Nem |
> | Foglalás | Nem |
> | erőforrás-szolgáltatók | Nem |
> | resources | Nem |
> | validateReservationOrder | Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem |
> | CdnWebApplicationFirewallPolicies | Igen |
> | edgenodes | Nem |
> | Profilok | Igen |
> | profilok / végpontok | Igen |
> | profilok / végpontok / customdomains | Nem |
> | profilok / végpontok / eredet | Nem |
> | validateProbe | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.Tanúsítványregisztráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | certificateOrders (tanúsítványRendelések) | Igen |
> | certificateOrders / tanúsítványok | Nem |
> | validateCertificateRegistrationInformation | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Képességek | Nem |
> | tartománynevek | Igen |
> | domainNames / képességek | Nem |
> | domainNames / internalLoadBalancers | Nem |
> | domainNames / serviceCertificates | Nem |
> | domainNames / bővítőhely | Nem |
> | domainNames / bővítőhely / szerepek | Nem |
> | domainNames / bővítőhely / szerepek / metricDefinitions | Nem |
> | domainNames / bővítőhely / szerepek / metrikák | Nem |
> | moveSubscriptionResources | Nem |
> | operatingSystemFamilies (operációs rendszerCsaládok) | Nem |
> | operációs rendszerek | Nem |
> | kvóták | Nem |
> | resourceTypes | Nem |
> | validateSubscriptionMoveAvailability | Nem |
> | virtualMachines | Igen |
> | virtualMachines / diagnosticSettings | Nem |
> | virtualMachines / metricDefinitions | Nem |
> | virtualMachines / mérőszámok | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Képességek | Nem |
> | expressRouteCrossConnections | Nem |
> | expressRouteCrossConnections / társviszony-létesítések | Nem |
> | gatewaySupportedDevices | Nem |
> | networkSecurityGroups (hálózatibiztonsági csoportok) | Igen |
> | kvóták | Nem |
> | reservedIps | Igen |
> | virtualNetworks | Igen |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nem |
> | virtualNetworks / virtualNetworkPeerings | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Képességek | Nem |
> | Lemezek | Nem |
> | images | Nem |
> | osImages | Nem |
> | osPlatformImages | Nem |
> | nyilvános képek | Nem |
> | kvóták | Nem |
> | storageAccounts | Igen |
> | storageAccounts / blobServices | Nem |
> | storageAccounts / fileServices | Nem |
> | storageAccounts / metricDefinitions | Nem |
> | storageAccounts / metrikák | Nem |
> | storageAccounts / queueServices | Nem |
> | storageAccounts / szolgáltatások | Nem |
> | storageAccounts / szolgáltatások / diagnosztikaiBeállítások | Nem |
> | storageAccounts / szolgáltatások / metricDefinitions | Nem |
> | storageAccounts / szolgáltatások / metrikák | Nem |
> | storageAccounts / tableServices | Nem |
> | storageAccounts / vmImages | Nem |
> | vmImages | Nem |

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
> | Használatösszesítések | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilitySets | Igen |
> | diskEncryptionSets | Igen |
> | Lemezek | Igen |
> | Galériák | Igen |
> | galériák / alkalmazások | Nem |
> | galériák / alkalmazások / verziók | Nem |
> | galériák / képek | Nem |
> | galériák / képek / verziók | Nem |
> | hostCsoportok | Igen |
> | hostCsoportok / állomások | Igen |
> | images | Igen |
> | proximityPlacementGroups (proximityPlacementGroups) | Igen |
> | restorePointCollections | Igen |
> | restorePointCollections / restorePoints | Nem |
> | sharedVMImages | Igen |
> | sharedVMImages / verziók | Nem |
> | pillanatképek | Igen |
> | virtualMachines | Igen |
> | virtualMachines / kiterjesztések | Igen |
> | virtualMachines / metricDefinitions | Nem |
> | virtualMachineScaleSets | Igen |
> | virtualMachineScaleSets / kiterjesztések | Nem |
> | virtualMachineScaleSets / hálózati interfészek | Nem |
> | virtualMachineScaleSets / publicIPAddresses | Nem |
> | virtualMachineScaleSets / virtualMachines | Nem |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nem |

## <a name="microsoftconsumption"></a>Microsoft.Fogyasztás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összesített költség | Nem |
> | Egyenlegek | Nem |
> | Költségvetések | Nem |
> | Díjak | Nem |
> | Költségcímkék | Nem |
> | Hitelek | Nem |
> | események | Nem |
> | Előrejelzések | Nem |
> | Sok | Nem |
> | Piacokon | Nem |
> | Árlisták | Nem |
> | Termékek | Nem |
> | ReservationDetails (Foglalás részletei) | Nem |
> | Foglalási javaslatok | Nem |
> | ReservationSummaries (Foglalásösszegzések) | Nem |
> | ReservationTransactions (Foglalási tranzakciók) | Nem |
> | Címkék | Nem |
> | Bérlők | Nem |
> | Fogalmak | Nem |
> | UsageDetails (Használatrészletei) | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerGroups (tárolócsoportok) | Igen |
> | szolgáltatásAssociationLinks | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Nyilvántartások | Igen |
> | nyilvántartások / buildek | Nem |
> | nyilvántartások / buildek / mégse | Nem |
> | nyilvántartások / buildek / getLogLink | Nem |
> | nyilvántartások / buildTasks | Igen |
> | nyilvántartások / buildTasks / lépések | Nem |
> | regisztika / eventGridFilters | Nem |
> | nyilvántartások / generateCredentials | Nem |
> | regisztika / getBuildSourceUploadUrl | Nem |
> | nyilvántartások / GetCredentials | Nem |
> | nyilvántartások / importImage | Nem |
> | nyilvántartások / privateEndpointConnectionProxies | Nem |
> | regisztika / privateEndpointConnectionProxies / érvényesítés | Nem |
> | nyilvántartások / privateEndpointConnections | Nem |
> | nyilvántartások / privateLinkResources | Nem |
> | regisztika / queueBuild | Nem |
> | regisztika / regenerateCredential | Nem |
> | regisztika / regenerateCredentials | Nem |
> | nyilvántartások / replikációk | Igen |
> | nyilvántartások / futtatások | Nem |
> | nyilvántartások / fut / mégse | Nem |
> | nyilvántartások / scheduleRun | Nem |
> | nyilvántartások / hatókörTérképek | Nem |
> | nyilvántartások / taskRuns | Igen |
> | nyilvántartások / feladatok | Igen |
> | nyilvántartások / tokenek | Nem |
> | nyilvántartások / updatePolicies | Nem |
> | nyilvántartások / webhookok | Igen |
> | nyilvántartások / webhooks / getCallbackConfig | Nem |
> | nyilvántartások / webhooks / ping | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerServices | Igen |
> | managedClusters (kezelt fürtök) | Igen |
> | openShiftManagedClusters | Igen |

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
> | Számlázási számlák | Nem |
> | Költségvetések | Nem |
> | CloudConnectors | Nem |
> | Összekötők | Igen |
> | Részlegek | Nem |
> | Dimenziók | Nem |
> | EnrollmentAccounts fiókok | Nem |
> | Export | Nem |
> | Külsőszámlázásfiókok | Nem |
> | ExternalBillingAccounts / Riasztások | Nem |
> | ExternalBillingAccounts / Dimenziók | Nem |
> | ExternalBillingAccounts / Előrejelzés | Nem |
> | ExternalBillingAccounts / Lekérdezés | Nem |
> | Külső előfizetések | Nem |
> | ExternalSubscriptions / Riasztások | Nem |
> | ExternalSubscriptions / Dimenziók | Nem |
> | ExternalSubscriptions / Előrejelzés | Nem |
> | ExternalSubscriptions / Lekérdezés | Nem |
> | Előrejelzés | Nem |
> | Lekérdezés | Nem |
> | Regisztráció | Nem |
> | Jelentési konfigurációk | Nem |
> | Jelentések | Nem |
> | Beállítások | Nem |
> | showbackRules | Nem |
> | Nézetek | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kérelmek | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Egyesületek | Nem |
> | erőforrás-szolgáltatók | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkahelyek | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | munkaterületek | Igen |
> | munkaterületek / dbWorkspaces | Nem |
> | munkaterületek / tárolásTitkosítás | Nem |
> | munkaterületek / virtualNetworkPeerings | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Katalógusok | Igen |
> | adatkatalógusok | Igen |
> | datacatalogs / adatforrások | Nem |
> | datacatalogs / adatforrások / vizsgál | Nem |
> | datacatalogs / adatforrások / vizsgál / adatkészletek | Nem |
> | datacatalogs / adatforrások / vizsgál / triggerek | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataGyárak | Igen |
> | dataFactories / diagnosticSettings | Nem |
> | dataFactories / metricDefinitions | Nem |
> | dataFactorySchema | Nem |
> | Gyárak | Igen |
> | gyárak / integrációRuntimes | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | fiókok / dataLakeStoreAccounts | Nem |
> | fiókok / storageAccounts | Nem |
> | számlák / storageAccounts / konténerek | Nem |
> | számlák / transferAnalyticsUnits | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | számlák / eventGridFilters | Nem |
> | számlák / firewallRules | Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |
> | szolgáltatások / projektek | Igen |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | számlák / részvények | Nem |
> | számlák / részvények / adatkészletek | Nem |
> | számlák / részvények / meghívók | Nem |
> | számlák / részvények / providersharesubscriptions | Nem |
> | számlák / megosztások / szinkronizálásBeállítások | Nem |
> | számlák / sharesubscriptions | Nem |
> | számlák / sharesubscriptions / consumerSourceDataSets | Nem |
> | számlák / sharesubscriptions / datasetmappings | Nem |
> | számlák / sharesubscriptions / triggerek | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Szerverek | Igen |
> | szerverek / tanácsadók | Nem |
> | szerverek / kulcsok | Nem |
> | kiszolgálók / privateEndpointConnectionProxies | Nem |
> | kiszolgálók / privateEndpointConnections | Nem |
> | szerverek / privateLinkResources | Nem |
> | szerverek / queryTexts | Nem |
> | szerverek / recoverableServers | Nem |
> | szerverek / topQueryStatistics | Nem |
> | szerverek / virtualNetworkRules | Nem |
> | szerverek / waitStatistics | Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Szerverek | Igen |
> | szerverek / tanácsadók | Nem |
> | szerverek / kulcsok | Nem |
> | kiszolgálók / privateEndpointConnectionProxies | Nem |
> | kiszolgálók / privateEndpointConnections | Nem |
> | szerverek / privateLinkResources | Nem |
> | szerverek / queryTexts | Nem |
> | szerverek / recoverableServers | Nem |
> | szerverek / topQueryStatistics | Nem |
> | szerverek / virtualNetworkRules | Nem |
> | szerverek / waitStatistics | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kiszolgálócsoportok | Igen |
> | Szerverek | Igen |
> | szerverek / tanácsadók | Nem |
> | szerverek / kulcsok | Nem |
> | kiszolgálók / privateEndpointConnectionProxies | Nem |
> | kiszolgálók / privateEndpointConnections | Nem |
> | szerverek / privateLinkResources | Nem |
> | szerverek / queryTexts | Nem |
> | szerverek / recoverableServers | Nem |
> | szerverek / topQueryStatistics | Nem |
> | szerverek / virtualNetworkRules | Nem |
> | szerverek / waitStatistics | Nem |
> | kiszolgálókv2 | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | artifactSources | Igen |
> | bevezetés | Igen |
> | serviceTopologies | Igen |
> | serviceTopologies / szolgáltatások | Igen |
> | serviceTopologies / szolgáltatások / serviceUnits | Igen |
> | lépések | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazáscsoportok | Igen |
> | alkalmazáscsoportok / alkalmazások | Nem |
> | alkalmazáscsoportok / asztali számítógépek | Nem |
> | alkalmazáscsoportok / startmenuitems | Nem |
> | hostpools | Igen |
> | hostpools / sessionhosts | Nem |
> | hostpools / sessionhosts / usersessions | Nem |
> | hostpools / usersessions | Nem |
> | munkaterületek | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | ElasticPools (Rugalmasmedencék) | Igen |
> | ElasticPools / IotHubTenants | Igen |
> | ElasticPools / IotHubTenants / securitySettings | Nem |
> | IotHubok | Igen |
> | IotHubs / eventGridFilters | Nem |
> | IotHubs / securityBeállítások | Nem |
> | ProvisioningServices (Szolgáltatások kiépítése) | Igen |
> | Használat | Nem |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Csővezetékek | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Vezérlők | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | laborközpontok | Igen |
> | Labs | Igen |
> | laborok / környezetek | Igen |
> | laborok / szolgáltatásRunners | Igen |
> | laborok / virtualMachines | Igen |
> | Menetrend | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | databaseAccountNames | Nem |
> | adatbázis-fiókok | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományok | Igen |
> | domain / domainOwnershipIdentifiers | Nem |
> | generateSsoRequest | Nem |
> | topLevelDomains | Nem |
> | validateDomainRegistrationInformation | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lcsprojects | Nem |
> | lcsprojects / felhőtelepítés | Nem |
> | lcsprojects / csatlakozók | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Tartományok | Igen |
> | domainek / témák | Nem |
> | eventElőfizetések | Nem |
> | extensionTopics (Témakörök) | Nem |
> | partnerNamespaces | Igen |
> | partnerNamespaces / eventChannels | Nem |
> | partnerRegisztrációk | Igen |
> | partnerTémakörök | Igen |
> | partnerTémakörök / eventSubscriptions | Nem |
> | systemTopics | Igen |
> | systemTopics / eventSubscriptions | Nem |
> | Témák | Igen |
> | topicTypes | Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Igen |
> | Névterek | Igen |
> | névterek / engedélyezési szabályok | Nem |
> | névterek / disasterrecoveryconfigs | Nem |
> | névterek / eventhubs | Nem |
> | névterek / eventhubs / engedélyezési szabályok | Nem |
> | névterek / eventhubs / fogyasztói csoportok | Nem |
> | névterek / hálózati szabályok | Nem |

## <a name="microsoftfeatures"></a>Microsoft.Szolgáltatások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | funkciók | Nem |
> | Szolgáltatók | Nem |

## <a name="microsoftgallery"></a>Microsoft.Galéria

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Beiratkozik | Nem |
> | galériaelemek | Nem |
> | generateartifactaccessuri | Nem |
> | myareas (barátaima) | Nem |
> | myareas / területek | Nem |
> | myareas / területek / területek | Nem |
> | myareas / területek / területek / galériaelemek | Nem |
> | myareas / területek / galériaelemek | Nem |
> | myareas / galériaelemek | Nem |
> | Regisztráció | Nem |
> | resources | Nem |
> | beolvasás, resourcesbyid | Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestKonfiguráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | automatikusfelügyelt fiókok | Igen |
> | autoManagedVmConfigurationProfiles | Igen |
> | configurationProfileAssignments (configurationProfileAssignments) | Nem |
> | guestConfigurationAssignments | Nem |
> | Szoftver | Nem |
> | softwareUpdateProfile | Nem |
> | szoftverfrissítések | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hanaInstances (hanaInstances) | Igen |
> | sapMonitorok | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedikált HSM-ek | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Igen |
> | fürtök / alkalmazások | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Gépek | Igen |
> | gépek / kiterjesztések | Igen |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataManagers (adatkezelők) | Igen |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összetevők | Igen |
> | hálózati hatókörök | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Munkahelyek | Igen |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosztikai beállítások | Nem |
> | diagnosticSettingsCategories (diagnosztikai beállításokkategóriák) | Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appTemplates | Nem |
> | IoTApps | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Graph | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | töröltVaults | Nem |
> | hsmPools | Igen |
> | Boltívek | Igen |
> | trezorok / accessPolicies | Nem |
> | boltívek / eventGridFilters | Nem |
> | boltívek / titkok | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Igen |
> | fürtök / csatoltadatbázis-konfigurációk | Nem |
> | fürtök / adatbázisok | Nem |
> | klaszterek / adatbázisok / adatkapcsolatok | Nem |
> | klaszterek / adatbázisok / eventhubconnections | Nem |
> | klaszterek / adatbázisok / megbízórendeltségek | Nem |
> | klaszterek / megbízófeladatok | Nem |
> | fürtök / megosztott identitások | Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | laborszámlák | Igen |
> | felhasználók | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hostingKörnyezet | Igen |
> | integrációS fiókok | Igen |
> | integrationServiceEnvironments környezetek | Igen |
> | integrationServiceEnvironments / managedApis | Igen |
> | elszigeteltKörnyezetek | Igen |
> | Munkafolyamatok | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | kötelezettségvállalásTervek | Igen |
> | webServices | Igen |
> | Munkaterületek | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | munkaterületek | Igen |
> | munkaterületek / számítások | Nem |
> | munkaterületek / eventGridFilters | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Identitások | Nem |
> | userAssignedIdentities | Igen |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | piactérRegistrationDefinitions | Nem |
> | regisztrációHozzárendelések | Nem |
> | registrationDefinitions | Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | getEntitások | Nem |
> | managementGroups (felügyeleti csoportok) | Nem |
> | managementGroups / beállítások | Nem |
> | resources | Nem |
> | startTenantBackfill | Nem |
> | tenantBackfillStatus | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | számlák / eventGridFilters | Nem |

## <a name="microsoftmarketplace"></a>Microsoft.Piactér

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kínál | Nem |
> | offerTypes | Nem |
> | offerTypes / kiadók | Nem |
> | offerTypes / kiadók / ajánlatok | Nem |
> | offerTypes / kiadók / ajánlatok / tervek | Nem |
> | offerTypes / kiadók / ajánlatok / tervek / megállapodások | Nem |
> | offerTypes / kiadók / ajánlatok / tervek / configs | Nem |
> | offerTypes / kiadók / ajánlatok / tervek / configs / importImage | Nem |
> | privát galériaelemek | Nem |
> | privateStoreClient | Nem |
> | Termékek | Nem |
> | Kiadók | Nem |
> | kiadók / ajánlatok | Nem |
> | kiadók / ajánlatok / módosítások | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicDevServices | Igen |
> | updateCommunicationPreference | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceRendelés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Megállapodások | Nem |
> | ajánlattípusok | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | médiaszolgáltatások | Igen |
> | mediaservices / accountFilters | Nem |
> | mediaservices / eszközök | Nem |
> | mediaservices / eszközök / assetFilters | Nem |
> | mediaservices / contentKeyPolicies | Nem |
> | mediaservices / eventGridFilters | Nem |
> | mediaservices / liveEventOperations | Nem |
> | mediaservices / liveEvents | Igen |
> | mediaservices / liveEvents / liveOutputs | Nem |
> | mediaservices / liveOutputOperations | Nem |
> | mediaservices / mediaGraphs | Nem |
> | mediaservices / streamingEndpointOperations | Nem |
> | mediaservices / streamingEndpoints | Igen |
> | mediaservices / streamingLocators | Nem |
> | mediaservices / streamingPolicies | Nem |
> | mediaservices / átalakítások | Nem |
> | mediaservices / átalakítja / munkahelyek | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Tavasz

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appClusters | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Áttelepítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | értékelésProjektek | Igen |
> | projektek áttelepítése | Igen |
> | moveCollections (gyűjtemények) | Igen |
> | Projektek | Igen |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Igen |
> | objectUnderstandingAccounts | Igen |
> | remoteRenderingAccounts fiókok | Igen |
> | spatialAnchorsAccounts | Igen |
> | surfaceReconstructionAccounts | Igen |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | netAppAccounts | Igen |
> | netAppAccounts / capacityPools | Igen |
> | netAppAccounts / capacityPools / kötetek | Igen |
> | netAppAccounts / capacityPools / kötetek / mountTargets | Igen |
> | netAppAccounts / capacityPools / kötetek / pillanatképek | Igen |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | NotebookProxies | Nem |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazásátjárók | Igen |
> | alkalmazásGatewayWebApplicationFirewallPolicies | Igen |
> | alkalmazásSecurityGroups | Igen |
> | azureFirewallFqdnTags | Nem |
> | azureTűzfalak | Igen |
> | bástyaHázigazdák | Igen |
> | bgpServiceKözösségek | Nem |
> | Kapcsolatok | Igen |
> | ddosCustomPolicies | Igen |
> | ddosProtectionPlans | Igen |
> | dnsOperationStatuses | Nem |
> | dnszones között | Igen |
> | dnszones / A | Nem |
> | dnszones / AAAA | Nem |
> | dnszones / minden | Nem |
> | dnszones / CAA | Nem |
> | dnszones / CNAME | Nem |
> | dnszones / MX | Nem |
> | dnszones / NS | Nem |
> | dnszones / PTR | Nem |
> | dnszones / rekordhalmazok | Nem |
> | dnszones / SOA | Nem |
> | dnszones / SRV | Nem |
> | dnszones / TXT | Nem |
> | expressRouteCircuits | Igen |
> | expressRouteCrossConnections | Igen |
> | expresszRouteÁtjárók | Igen |
> | expressRoutePorts | Igen |
> | expresszRouteServiceProviders | Nem |
> | firewallPolicies (tűzfalházirendek) | Igen |
> | elülső ajtók | Igen |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nem |
> | frontdoorWebApplicationFirewallPolicies | Igen |
> | getDnsResourceReference | Nem |
> | internalNotify | Nem |
> | terheléselosztók | Igen |
> | localNetworkGateways | Igen |
> | natGateways | Igen |
> | hálózatiIntentPolicies | Igen |
> | hálózati interfészek | Igen |
> | hálózati profilok | Igen |
> | networkSecurityGroups (hálózatibiztonsági csoportok) | Igen |
> | networkWatchers | Igen |
> | networkWatchers / connectionMonitors | Igen |
> | networkWatchers / objektívek | Igen |
> | networkWatchers / pingMeshes | Igen |
> | p2sVpnGateways | Igen |
> | magánDnsOperationStatuses | Nem |
> | privátDnsZones | Igen |
> | privateDnsZones / A | Nem |
> | privateDnsZones / AAAA | Nem |
> | privateDnsZones / minden | Nem |
> | privateDnsZones / CNAME | Nem |
> | privateDnsZones / MX | Nem |
> | privateDnsZones / PTR | Nem |
> | privateDnsZones / SOA | Nem |
> | privateDnsZones / SRV | Nem |
> | privateDnsZones / TXT | Nem |
> | privateDnsZones / virtualNetworkLinks | Igen |
> | privateEndpoints | Igen |
> | privateLinkServices | Igen |
> | nyilvánosIP-címek | Igen |
> | nyilvános IPPrefixes | Igen |
> | routeFilters | Igen |
> | routeTables (útvonaltáblák) | Igen |
> | serviceEndpointPolicies | Igen |
> | trafficManagerGeographicHierarchies | Nem |
> | trafficmanagerprofiles (trafficmanagerprofiles) | Igen |
> | trafficmanagerprofiles / heatMaps | Nem |
> | trafficManagerUserMetricsKeys | Nem |
> | virtualHubs | Igen |
> | virtualNetworkGateways | Igen |
> | virtualNetworks | Igen |
> | virtualNetworkTaps | Igen |
> | virtualWans | Igen |
> | vpnGateways | Igen |
> | vpnOldalak | Igen |
> | webApplicationFirewallPolicies | Igen |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Igen |
> | névterek / értesítésHubs | Igen |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | osNamespaces között | Igen |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Hiperv-oldalak | Igen |
> | Importwebhelyek | Igen |
> | Kiszolgálówebhelyek | Igen |
> | VMware Oldalak | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Klaszterek | Igen |
> | linkTargets | Nem |
> | storageInsightConfigs | Nem |
> | munkaterületek | Igen |
> | munkaterületek / dataExports | Nem |
> | munkaterületek / dataSources | Nem |
> | munkaterületek / linkedServices | Nem |
> | munkaterületek / privateEndpointConnectionProxies | Nem |
> | munkaterületek / privateEndpointConnections | Nem |
> | munkaterületek / privateLinkResources | Nem |
> | munkaterületek / lekérdezés | Nem |
> | munkaterületek / scopedPrivateLinkProxies | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managementegyesületek | Nem |
> | felügyeleti konfigurációk | Igen |
> | megoldások | Igen |
> | Kilátás nyílik | Igen |

## <a name="microsoftpeering"></a>Microsoft.Társviszony-létesítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | legacyPeerings | Nem |
> | peerAsns között | Nem |
> | társviszony | Igen |
> | peeringServiceOrszágok | Nem |
> | társviszony-létesítőszolgáltatók | Nem |
> | társviszony-létesítési szolgáltatások | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | policyEvents (házirendEsemények) | Nem |
> | policyMetadata | Nem |
> | policyStates (politikai államok) | Nem |
> | policyTrackedResources (házirendNyomonforrások) | Nem |
> | kármentesítések | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> |  -konzolok | Nem |
> | irányítópultok | Igen |
> | UserSettings | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | workspaceGyűjtemények | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kapacitások | Igen |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | backupProtectedItems | Nem |
> | Boltívek | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Igen |
> | névterek / engedélyezési szabályok | Nem |
> | névterek / hibrid kapcsolatok | Nem |
> | névterek / hibridkapcsolatok / engedélyezési szabályok | Nem |
> | névterek / wcfrelays | Nem |
> | névterek / wcfrelays / authorizationrules | Nem |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Nem |
> | Gyűjtemények | Igen |
> | gyűjtemények / alkalmazások | Nem |
> | gyűjtemények / biztonsági igazgatók | Nem |
> | sablonKépek | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Lekérdezések | Igen |
> | resourceChangeDetails | Nem |
> | erőforrás-változások | Nem |
> | resources | Nem |
> | resourcesHistory | Nem |
> | előfizetésekÁllapota | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilityStatuses | Nem |
> | childAvailabilityStatuses | Nem |
> | childResources (gyermekerőforrások) | Nem |
> | feltörekvő szövetek | Nem |
> | események | Nem |
> | érintett források | Nem |
> | metaadatok | Nem |
> | Értesítések | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Telepítések | Nem |
> | telepítések / műveletek | Nem |
> | központi parancsfájlok | Igen |
> | deploymentScripts / naplók | Nem |
> | Linkek | Nem |
> | notifyResourceJobs | Nem |
> | Szolgáltatók | Nem |
> | erőforráscsoportok | Nem |
> | Előfizetések | Nem |
> | Bérlők | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Igen |
> | saasresources között | Nem |

## <a name="microsoftsearch"></a>Microsoft.Keresés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nem |
> | searchServices | Igen |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | adaptiveNetworkEdzések | Nem |
> | advancedThreatProtectionSettings | Nem |
> | riasztások | Nem |
> | engedélyezettkapcsolatok | Nem |
> | alkalmazásWhitelistings | Nem |
> | értékelésMetadata | Nem |
> | értékelések | Nem |
> | autoDismissAlertsRules | Nem |
> | automatizálások | Igen |
> | AutoProvisioningBeállítások | Nem |
> | Megfelelőség | Nem |
> | dataCollectionAgents | Nem |
> | deviceSecurityGroups | Nem |
> | discoveredSecuritySolutions | Nem |
> | externalSecuritySolutions | Nem |
> | InformationProtectionPolicies (Információvédelmi irányelvek) | Nem |
> | iotSecuritySolutions | Igen |
> | iotSecuritySolutions / analyticsModellek | Nem |
> | iotSecuritySolutions / analyticsModellek / aggregatedAlerts | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nem |
> | jitNetworkAccessPolicies | Nem |
> | hálózati adatok | Nem |
> | policies | Nem |
> | árak | Nem |
> | szabályozásiMegfelelőségi szabványok | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nem |
> | biztonsági partnerek | Nem |
> | securitySolutions (biztonsági megoldások) | Nem |
> | securitySolutionsReferenceData | Nem |
> | securityStatuses | Nem |
> | securityStatusesSummaries | Nem |
> | kiszolgálóbiztonsági értékelések | Nem |
> | beállítások | Nem |
> | alértékelések | Nem |
> | feladatok | Nem |
> | Topológiák | Nem |
> | munkaterületBeállítások | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosztikai beállítások | Nem |
> | diagnosticSettingsCategories (diagnosztikai beállításokkategóriák) | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összesítései | Nem |
> | alertRules (figyelmeztetés) | Nem |
> | alertRuleTemplates (figyelmeztetésSzabálysablonok) | Nem |
> | Könyvjelzők | Nem |
> | Esetekben | Nem |
> | dataConnectors | Nem |
> | dataConnectorsCheckRequirements | Nem |
> | Entitások | Nem |
> | entityQueries | Nem |
> | Események | Nem |
> | officeConsents (hivatalbeleegyezések) | Nem |
> | beállítások | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Névterek | Igen |
> | névterek / engedélyezési szabályok | Nem |
> | névterek / disasterrecoveryconfigs | Nem |
> | névterek / eventgridfilters | Nem |
> | névterek / hálózati szabályok | Nem |
> | névterek / várólisták | Nem |
> | névterek / várólisták / engedélyezési szabályok | Nem |
> | névterek / témakörök | Nem |
> | névterek / témakörök / engedélyezési szabályok | Nem |
> | névterek / témák / előfizetések | Nem |
> | névterek / témák / előfizetések / szabályok | Nem |
> | premiumMessagingRegions (prémiumüzenetekRégiói) | Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Igen |
> | Klaszterek | Igen |
> | fürtök / alkalmazások | Nem |
> | containerGroups (tárolócsoportok) | Igen |
> | containerGroupSets | Igen |
> | élhalmazok | Igen |
> | edgeclusters / alkalmazások | Nem |
> | kezelt klaszterek | Igen |
> | managedclusters / nodetypes | Nem |
> | Hálózatok | Igen |
> | titkos üzletek | Igen |
> | titkos üzletek / tanúsítványok | Nem |
> | titkos üzletek / titkok | Nem |
> | volumes | Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazások | Igen |
> | containerGroups (tárolócsoportok) | Igen |
> | Átjárók | Igen |
> | Hálózatok | Igen |
> | Titkok | Igen |
> | volumes | Igen |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | providerRegistrations (Regisztrációk) | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem |
> | bevezetés | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SignalR | Igen |
> | SignalR / eventGridFilters | Nem |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SiteRecoveryVault | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hibridElőnyök | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Megoldások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazásdefiníciók | Igen |
> | alkalmazások | Igen |
> | jitRequests | Igen |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | registeredElőfizetések | Nem |
> | Orsók | Igen |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managedInstances (példányok) | Igen |
> | managedInstances / adatbázisok | Igen |
> | managedInstances / adatbázisok / backupShortTermRetentionPolicies | Nem |
> | managedInstances / adatbázisok / sémák / táblák / oszlopok / érzékenységCímkék | Nem |
> | managedInstances / adatbázisok / sebezhetőségÉrtékelések | Nem |
> | managedInstances / adatbázisok / sebezhetőségÉrtékelések / szabályok / alaptervek | Nem |
> | managedInstances / encryptionProtector | Nem |
> | managedInstances / kulcsok | Nem |
> | managedInstances / restoreableDroppedDatabases / backupShortTermRetentionPolicies | Nem |
> | managedInstances / vulnerabilityAssessments | Nem |
> | Szerverek | Igen |
> | szerverek / rendszergazdák | Nem |
> | szerverek / communicationLinks | Nem |
> | szerverek / adatbázisok | Igen |
> | szerverek / encryptionProtector | Nem |
> | szerverek / firewallRules | Nem |
> | szerverek / kulcsok | Nem |
> | szerverek / helyreállíthatóDroppedadatbázisok | Nem |
> | szerverek / szolgáltatáscélok | Nem |
> | kiszolgálók / tdeCertificates | Nem |
> | virtualClusters | Nem |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Igen |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nem |
> | SqlVirtualMachines | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageAccounts | Igen |
> | storageAccounts / blobServices | Nem |
> | storageAccounts / fileServices | Nem |
> | storageAccounts / queueServices | Nem |
> | storageAccounts / szolgáltatások | Nem |
> | storageAccounts / szolgáltatások / metricDefinitions | Nem |
> | storageAccounts / tableServices | Nem |
> | Használat | Nem |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Elrejt | Igen |
> | gyorsítótárak / tárolókCélok | Nem |
> | usageModellek | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | replikációscsoportok | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices / registeredServers | Nem |
> | storageSyncServices / syncGroups | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem |
> | storageSyncServices / munkafolyamatok | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices / registeredServers | Nem |
> | storageSyncServices / syncGroups | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem |
> | storageSyncServices / munkafolyamatok | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices / registeredServers | Nem |
> | storageSyncServices / syncGroups | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem |
> | storageSyncServices / munkafolyamatok | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Vezetők | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | streamingjobs | Igen |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mégse | Nem |
> | CreateSubscription (Előfizetés létrehozása) | Nem |
> | Engedélyezi | Nem |
> | átnevezés | Nem |
> | Előfizetésdefiníciók | Nem |
> | Előfizetési műveletek | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Környezetben | Igen |
> | környezetek / accessPolicies | Nem |
> | környezetek / eseményforrások | Igen |
> | környezetek / referenceDataSets | Igen |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedikáltCloudNodes | Igen |
> | dedikáltCloudServices | Igen |
> | virtualMachines | Igen |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | eszközök | Igen |
> | registeredElőfizetések | Nem |
> | Szállítók | Nem |
> | eladók / skus | Nem |
> | gyártók / vnfs | Nem |
> | vnfs | Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | apiManagementAccounts | Nem |
> | apiManagementAccounts / apiAcls | Nem |
> | apiManagementAccounts / apis | Nem |
> | apiManagementAccounts / apis / apiAcls | Nem |
> | apiManagementAccounts / apis / connectionAcls | Nem |
> | apiManagementAccounts / apis / kapcsolatok | Nem |
> | apiManagementAccounts / apis / kapcsolatok / connectionAcls | Nem |
> | apiManagementAccounts / apis / localizedDefinitions | Nem |
> | apiManagementAccounts / connectionAcls | Nem |
> | apiManagementAccounts / kapcsolatok | Nem |
> | billingMéterek | Nem |
> | tanúsítványok | Igen |
> | connectionGateways (átjárók) | Igen |
> | Kapcsolatok | Igen |
> | egyéniApis | Igen |
> | törölt webhelyek | Nem |
> | hostingKörnyezet | Igen |
> | hostingEnvironments / eventGridFilters | Nem |
> | hostingEnvironments / multiRolePools | Nem |
> | hostingEnvironments / workerPools | Nem |
> | közzétételFelhasználók | Nem |
> | Ajánlások | Nem |
> | resourceHealthMetadata | Nem |
> | futási idők | Nem |
> | szerverFarms | Igen |
> | serverFarms / eventGridFilters | Nem |
> | Helyek | Igen |
> | oldalak/config  | Nem |
> | oldalak / eventGridFilters | Nem |
> | oldalak / hostNameBindings | Nem |
> | oldalak / networkConfig | Nem |
> | oldalak / premieraddons | Igen |
> | oldalak / bővítőhely | Igen |
> | oldalak / bővítőhely / eventGridFilters | Nem |
> | oldalak / bővítőhely / hostNameBindings | Nem |
> | oldalak / bővítőhely / networkConfig | Nem |
> | sourceControls | Nem |
> | statikus webhelyek | Igen |
> | Érvényesít | Nem |
> | verifyHostingEnvironmentVnet | Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosztikai beállítások | Nem |
> | diagnosticSettingsCategories (diagnosztikai beállításokkategóriák) | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DeviceServices | Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadFigyelő

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Összetevők | Nem |
> | componentsSummary | Nem |
> | monitorInstances | Nem |
> | monitorInstancesSummary | Nem |
> | Monitorok | Nem |
> | értesítésBeállítások | Nem |

## <a name="next-steps"></a>További lépések

Ha ugyanazokat az adatokat szeretné beszerezni, mint a vesszővel tagolt értékek fájlját, töltse le a [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)fájlt.
