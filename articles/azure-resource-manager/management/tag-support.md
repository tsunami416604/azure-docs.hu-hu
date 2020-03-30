---
title: Az erőforrások címkézési támogatása
description: Megmutatja, hogy mely Azure-erőforrástípusok támogatják a címkéket. Az összes Azure-szolgáltatás részleteit tartalmazza.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 6100c667c7df0b3e1740777565d260af9fa818a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657573"
---
# <a name="tag-support-for-azure-resources"></a>Az Azure-erőforrások címkézési támogatása
Ez a cikk azt ismerteti, hogy egy erőforrástípus támogatja-e a [címkéket.](tag-resources.md) A Támogatja a **címkéket** feliratú oszlop azt jelzi, hogy az erőforrástípus rendelkezik-e tulajdonsággal a címkéhez. A **Költségjelentésben címke** feliratú oszlop jelzi, hogy az adott erőforrástípus átadja-e a címkét a költségjelentésnek. A költségeket címkék szerint tekintheti meg a [Költségkezelési költségelemzésben,](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) az [Azure számlázási számláján és a napi használati adatokban.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Ha ugyanazokat az adatokat szeretné beszerezni, mint a vesszővel tagded értékek fájlját, töltse le a [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)fájlt.

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
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
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
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Tartományi szolgáltatások | Igen | Igen |
> | DomainServices / oucontainer | Nem | Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nem | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | aadsupportcases (ad-támogató esetek) | Nem | Nem |
> | hozzátesziszolgáltatások | Nem | Nem |
> | Ügynökök | Nem | Nem |
> | anonymousapiusers | Nem | Nem |
> | konfiguráció | Nem | Nem |
> | naplók | Nem | Nem |
> | jelentések | Nem | Nem |
> | servicehealthmetrics | Nem | Nem |
> | services | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Konfigurációk | Nem | Nem |
> | javaslatok létrehozása | Nem | Nem |
> | metaadatok | Nem | Nem |
> | Ajánlások | Nem | Nem |
> | elnyomások | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | actionRules (műveletszabály) | Igen | Igen |
> | riasztások | Nem | Nem |
> | figyelmeztetésekListája | Nem | Nem |
> | riasztásokMetaData | Nem | Nem |
> | figyelmeztetésekÖsszegzés | Nem | Nem |
> | figyelmeztetésekÖsszefoglalólista | Nem | Nem |
> | visszajelzés | Nem | Nem |
> | smartDetectorAlertRules | Igen | Igen |
> | smartDetectorRuntimeKörnyezetek | Nem | Nem |
> | intelligens csoportok | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Szerverek | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | reportVisszajelzés | Nem | Nem |
> | szolgáltatás | Igen | Igen |
> | validateServiceName | Nem | Nem |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | configurationStores (konfigurációRaktárak) | Igen | Igen |
> | configurationStores / eventGridFilters | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Spring | Igen | Igen |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nem | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | klasszikusRendszergazdák | Nem | Nem |
> | dataAliases | Nem | Nem |
> | denyAssignments | Nem | Nem |
> | magasabb szintű hozzáférés | Nem | Nem |
> | findOrphanRoleAssignments | Nem | Nem |
> | Zárak | Nem | Nem |
> | Engedélyek | Nem | Nem |
> | házirend-hozzárendelések | Nem | Nem |
> | policyDefinitions (policyDefinitions) | Nem | Nem |
> | policySetDefinitions (policySetDefinitions) | Nem | Nem |
> | szolgáltatóÜzemeltetés | Nem | Nem |
> | szerepkör-hozzárendelések | Nem | Nem |
> | roleAssignmentsUsageMetrics | Nem | Nem |
> | roleDefinitions (szerepkördefiníciók) | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Igen | Igen |
> | automationAccounts / konfigurációk | Igen | Igen |
> | automationAccounts / munkahelyek | Nem | Nem |
> | automationAccounts / privateEndpointConnectionProxies | Nem | Nem |
> | automationAccounts / privateEndpointConnections | Nem | Nem |
> | automationAccounts / privateLinkResources | Nem | Nem |
> | automationAccounts / runbookok | Igen | Igen |
> | automationAccounts / softwareUpdateConfigurations | Nem | Nem |
> | automationAccounts / webhooks | Nem | Nem |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | configurationStores (konfigurációRaktárak) | Igen | Igen |
> | configurationStores / eventGridFilters | Nem | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Genf

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Környezetben | Nem | Nem |
> | környezetek / fiókok | Nem | Nem |
> | környezetek / fiókok / névterek | Nem | Nem |
> | környezetek / fiókok / névterek / konfigurációk | Nem | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | b2cIgazgatók | Igen | Nem |
> | b2cbérlők | Nem | Nem |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hibridDataManagers | Igen | Igen |
> | postgresInstances | Igen | Igen |
> | sqlBigDataClusters | Igen | Igen |
> | sqlInstances | Igen | Igen |
> | sqlServerRegistrations | Igen | Igen |
> | sqlServerRegistrations / sqlServers | Nem | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Regisztrációk | Igen | Igen |
> | regisztrációk / customerSubscriptions | Nem | Nem |
> | regisztrációk / termékek | Nem | Nem |
> | ellenőrző billentyűk | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Igen | Igen |

## <a name="microsoftbilling"></a>Microsoft.Számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | számlázási számlák | Nem | Nem |
> | billingAccounts / megállapodások | Nem | Nem |
> | billingAccounts / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles | Nem | Nem |
> | billingAccounts / billingProfiles / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / ügyfelek | Nem | Nem |
> | billingAccounts / billingProfiles / utasítások | Nem | Nem |
> | billingAccounts / billingProfiles / számlák | Nem | Nem |
> | billingAccounts / billingProfiles / számlák / árlap | Nem | Nem |
> | billingAccounts / billingProfiles / számlák / tranzakciók | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSzakaszok / termékek | Nem | Nem |
> | billingAccounts / billingProfiles / számlaSzakaszok / termékek / átutalások | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / tranzakciók | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nem | Nem |
> | billingAccounts / BillingProfiles / patchOperations | Nem | Nem |
> | billingAccounts / billingProfiles / paymentMethods | Nem | Nem |
> | billingAccounts / billingProfiles / politikák | Nem | Nem |
> | billingAccounts / billingProfiles / árlap | Nem | Nem |
> | billingAccounts / billingProfiles / áradatletöltésDownloadOperations | Nem | Nem |
> | billingAccounts / billingProfiles / termékek | Nem | Nem |
> | billingAccounts / billingProfiles / tranzakciók | Nem | Nem |
> | billingAccounts / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingSubscriptions / számlák | Nem | Nem |
> | billingAccounts / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / createInvoiceSectionOperations | Nem | Nem |
> | billingAccounts / ügyfelek | Nem | Nem |
> | billingAccounts / ügyfelek / billingPermissions | Nem | Nem |
> | billingAccounts / ügyfelek / billingSubscriptions | Nem | Nem |
> | billingAccounts / ügyfelek / kezdeményező | Nem | Nem |
> | billingAccounts / ügyfelek / politikák | Nem | Nem |
> | billingAccounts / ügyfelek / termékek | Nem | Nem |
> | billingAccounts / ügyfelek / tranzakciók | Nem | Nem |
> | billingAccounts / ügyfelek / átutalások | Nem | Nem |
> | billingAccounts / osztályok | Nem | Nem |
> | billingAccounts / enrollmentAccounts | Nem | Nem |
> | billingAccounts / számlák | Nem | Nem |
> | billingAccounts / számlaSzakaszok | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nem | Nem |
> | billingAccounts / számlaSzakaszok / billingSubscriptions | Nem | Nem |
> | billingAccounts / számlaSzakaszok / billingSubscriptions / átvitel | Nem | Nem |
> | billingAccounts / számlaSzakaszok / emelik | Nem | Nem |
> | billingAccounts / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / számlaSzakaszok / patchOperations | Nem | Nem |
> | billingAccounts / számlaSzakaszok / productMoveOperations | Nem | Nem |
> | billingAccounts / számlaSzakaszok / termékek | Nem | Nem |
> | billingAccounts / számlaSzakaszok / termékek / átutalás | Nem | Nem |
> | billingAccounts / számlaSzakaszok / termékek / updateAutoRenew | Nem | Nem |
> | billingAccounts / számlaSzakaszok / tranzakciók | Nem | Nem |
> | billingAccounts / számlaSzakaszok / átutalások | Nem | Nem |
> | billingAccounts / lineOfCredit | Nem | Nem |
> | billingAccounts / patchOperations | Nem | Nem |
> | billingAccounts / fizetési módszerek | Nem | Nem |
> | billingAccounts / termékek | Nem | Nem |
> | billingAccounts / tranzakciók | Nem | Nem |
> | billingIdőszakok | Nem | Nem |
> | számlázásI engedélyek | Nem | Nem |
> | billingProperty (számlázási tulajdonság) | Nem | Nem |
> | billingRoleAssignments | Nem | Nem |
> | billingRoleDefinitions | Nem | Nem |
> | createBillingRoleAssignment | Nem | Nem |
> | Szervezeti egységek | Nem | Nem |
> | enrollmentAccounts fiókok | Nem | Nem |
> | Számlák | Nem | Nem |
> | Transzferek | Nem | Nem |
> | átutalások / acceptTransfer | Nem | Nem |
> | átutalások / hanyatlásTransfer | Nem | Nem |
> | átvitelek / operationStatus | Nem | Nem |
> | átvitelek / validateTransfer | Nem | Nem |
> | validateAddress | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | térképApik | Igen | Igen |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Igen | Igen |
> | cordaMembers | Igen | Igen |
> | Watchers | Igen | Igen |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nem | Nem |
> | blueprintAssignments / assignmentOperations | Nem | Nem |
> | blueprintAssignments / műveletek | Nem | Nem |
> | Tervrajz | Nem | Nem |
> | tervrajzok / műtárgyak | Nem | Nem |
> | tervrajzok / változatok | Nem | Nem |
> | tervrajzok / verziók / műtárgyak | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | botServices (botServices) | Igen | Igen |
> | botServices / csatornák | Nem | Nem |
> | botServices / kapcsolatok | Nem | Nem |
> | nyelv | Nem | Nem |
> | sablonok | Nem | Nem |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Redis | Igen | Igen |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nem | Nem |
> | autoQuotaNövelje | Nem | Nem |
> | calculateExchange | Nem | Nem |
> | ár számítása | Nem | Nem |
> | beszerzési ár számítása | Nem | Nem |
> | Katalógusok | Nem | Nem |
> | kereskedelmiReservationOrders | Nem | Nem |
> | Exchange | Nem | Nem |
> | placePurchaseOrder | Nem | Nem |
> | reservationOrders (foglalás) | Nem | Nem |
> | reservationOrders / calculateRefund | Nem | Nem |
> | reservationOrders / egyesítés | Nem | Nem |
> | reservationOrders / foglalások | Nem | Nem |
> | reservationOrders / fenntartások / revíziók | Nem | Nem |
> | reservationOrders / visszaküldés | Nem | Nem |
> | reservationOrders / split | Nem | Nem |
> | reservationOrders / csere | Nem | Nem |
> | Foglalás | Nem | Nem |
> | erőforrás-szolgáltatók | Nem | Nem |
> | resources | Nem | Nem |
> | validateReservationOrder | Nem | Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem | Nem |
> | CdnWebApplicationFirewallPolicies | Igen | Igen |
> | edgenodes | Nem | Nem |
> | Profilok | Igen | Igen |
> | profilok / végpontok | Igen | Igen |
> | profilok / végpontok / customdomains | Nem | Nem |
> | profilok / végpontok / eredet | Nem | Nem |
> | validateProbe | Nem | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.Tanúsítványregisztráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | certificateOrders (tanúsítványRendelések) | Igen | Igen |
> | certificateOrders / tanúsítványok | Nem | Nem |
> | validateCertificateRegistrationInformation | Nem | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Képességek | Nem | Nem |
> | tartománynevek | Nem | Nem |
> | domainNames / képességek | Nem | Nem |
> | domainNames / internalLoadBalancers | Nem | Nem |
> | domainNames / serviceCertificates | Nem | Nem |
> | domainNames / bővítőhely | Nem | Nem |
> | domainNames / bővítőhely / szerepek | Nem | Nem |
> | domainNames / bővítőhely / szerepek / metricDefinitions | Nem | Nem |
> | domainNames / bővítőhely / szerepek / metrikák | Nem | Nem |
> | moveSubscriptionResources | Nem | Nem |
> | operatingSystemFamilies (operációs rendszerCsaládok) | Nem | Nem |
> | operációs rendszerek | Nem | Nem |
> | kvóták | Nem | Nem |
> | resourceTypes | Nem | Nem |
> | validateSubscriptionMoveAvailability | Nem | Nem |
> | virtualMachines | Nem | Nem |
> | virtualMachines / diagnosticSettings | Nem | Nem |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachines / mérőszámok | Nem | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nem | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Képességek | Nem | Nem |
> | expressRouteCrossConnections | Nem | Nem |
> | expressRouteCrossConnections / társviszony-létesítések | Nem | Nem |
> | gatewaySupportedDevices | Nem | Nem |
> | networkSecurityGroups (hálózatibiztonsági csoportok) | Nem | Nem |
> | kvóták | Nem | Nem |
> | reservedIps | Nem | Nem |
> | virtualNetworks | Nem | Nem |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nem | Nem |
> | virtualNetworks / virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Képességek | Nem | Nem |
> | Lemezek | Nem | Nem |
> | images | Nem | Nem |
> | osImages | Nem | Nem |
> | osPlatformImages | Nem | Nem |
> | nyilvános képek | Nem | Nem |
> | kvóták | Nem | Nem |
> | storageAccounts | Nem | Nem |
> | storageAccounts / blobServices | Nem | Nem |
> | storageAccounts / fileServices | Nem | Nem |
> | storageAccounts / metricDefinitions | Nem | Nem |
> | storageAccounts / metrikák | Nem | Nem |
> | storageAccounts / queueServices | Nem | Nem |
> | storageAccounts / szolgáltatások | Nem | Nem |
> | storageAccounts / szolgáltatások / diagnosztikaiBeállítások | Nem | Nem |
> | storageAccounts / szolgáltatások / metricDefinitions | Nem | Nem |
> | storageAccounts / szolgáltatások / metrikák | Nem | Nem |
> | storageAccounts / tableServices | Nem | Nem |
> | storageAccounts / vmImages | Nem | Nem |
> | vmImages | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | RateCard | Nem | Nem |
> | Használatösszesítések | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Igen | Igen |
> | diskEncryptionSets | Igen | Igen |
> | Lemezek | Igen | Igen |
> | Galériák | Igen | Igen |
> | galériák / alkalmazások | Nem | Nem |
> | galériák / alkalmazások / verziók | Nem | Nem |
> | galériák / képek | Nem | Nem |
> | galériák / képek / verziók | Nem | Nem |
> | hostCsoportok | Igen | Igen |
> | hostCsoportok / állomások | Igen | Igen |
> | images | Igen | Igen |
> | proximityPlacementGroups (proximityPlacementGroups) | Igen | Igen |
> | restorePointCollections | Igen | Igen |
> | restorePointCollections / restorePoints | Nem | Nem |
> | sharedVMImages | Igen | Igen |
> | sharedVMImages / verziók | Nem | Nem |
> | pillanatképek | Igen | Igen |
> | virtualMachines | Igen | Igen |
> | virtualMachines / kiterjesztések | Igen | Igen |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachineScaleSets | Igen | Igen |
> | virtualMachineScaleSets / kiterjesztések | Nem | Nem |
> | virtualMachineScaleSets / hálózati interfészek | Nem | Nem |
> | virtualMachineScaleSets / publicIPAddresses | Nem | Nem |
> | virtualMachineScaleSets / virtualMachines | Nem | Nem |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nem | Nem |

## <a name="microsoftconsumption"></a>Microsoft.Fogyasztás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Összesített költség | Nem | Nem |
> | Egyenlegek | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | Díjak | Nem | Nem |
> | Költségcímkék | Nem | Nem |
> | Hitelek | Nem | Nem |
> | események | Nem | Nem |
> | Előrejelzések | Nem | Nem |
> | Sok | Nem | Nem |
> | Piacokon | Nem | Nem |
> | Árlisták | Nem | Nem |
> | Termékek | Nem | Nem |
> | ReservationDetails (Foglalás részletei) | Nem | Nem |
> | Foglalási javaslatok | Nem | Nem |
> | ReservationSummaries (Foglalásösszegzések) | Nem | Nem |
> | ReservationTransactions (Foglalási tranzakciók) | Nem | Nem |
> | Címkék | Nem | Nem |
> | Bérlők | Nem | Nem |
> | Fogalmak | Nem | Nem |
> | UsageDetails (Használatrészletei) | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | containerGroups (tárolócsoportok) | Igen | Igen |
> | szolgáltatásAssociationLinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Nyilvántartások | Igen | Igen |
> | nyilvántartások / buildek | Nem | Nem |
> | nyilvántartások / buildek / mégse | Nem | Nem |
> | nyilvántartások / buildek / getLogLink | Nem | Nem |
> | nyilvántartások / buildTasks | Igen | Igen |
> | nyilvántartások / buildTasks / lépések | Nem | Nem |
> | regisztika / eventGridFilters | Nem | Nem |
> | nyilvántartások / generateCredentials | Nem | Nem |
> | regisztika / getBuildSourceUploadUrl | Nem | Nem |
> | nyilvántartások / GetCredentials | Nem | Nem |
> | nyilvántartások / importImage | Nem | Nem |
> | nyilvántartások / privateEndpointConnectionProxies | Nem | Nem |
> | regisztika / privateEndpointConnectionProxies / érvényesítés | Nem | Nem |
> | nyilvántartások / privateEndpointConnections | Nem | Nem |
> | nyilvántartások / privateLinkResources | Nem | Nem |
> | regisztika / queueBuild | Nem | Nem |
> | regisztika / regenerateCredential | Nem | Nem |
> | regisztika / regenerateCredentials | Nem | Nem |
> | nyilvántartások / replikációk | Igen | Igen |
> | nyilvántartások / futtatások | Nem | Nem |
> | nyilvántartások / fut / mégse | Nem | Nem |
> | nyilvántartások / scheduleRun | Nem | Nem |
> | nyilvántartások / hatókörTérképek | Nem | Nem |
> | nyilvántartások / taskRuns | Igen | Igen |
> | nyilvántartások / feladatok | Igen | Igen |
> | nyilvántartások / tokenek | Nem | Nem |
> | nyilvántartások / updatePolicies | Nem | Nem |
> | nyilvántartások / webhookok | Igen | Igen |
> | nyilvántartások / webhooks / getCallbackConfig | Nem | Nem |
> | nyilvántartások / webhooks / ping | Nem | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | containerServices | Igen | Igen |
> | managedClusters (kezelt fürtök) | Igen | Igen |
> | openShiftManagedClusters | Igen | Igen |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Riasztások | Nem | Nem |
> | Számlázási számlák | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | CloudConnectors | Nem | Nem |
> | Összekötők | Igen | Igen |
> | Részlegek | Nem | Nem |
> | Dimenziók | Nem | Nem |
> | EnrollmentAccounts fiókok | Nem | Nem |
> | Export | Nem | Nem |
> | Külsőszámlázásfiókok | Nem | Nem |
> | ExternalBillingAccounts / Riasztások | Nem | Nem |
> | ExternalBillingAccounts / Dimenziók | Nem | Nem |
> | ExternalBillingAccounts / Előrejelzés | Nem | Nem |
> | ExternalBillingAccounts / Lekérdezés | Nem | Nem |
> | Külső előfizetések | Nem | Nem |
> | ExternalSubscriptions / Riasztások | Nem | Nem |
> | ExternalSubscriptions / Dimenziók | Nem | Nem |
> | ExternalSubscriptions / Előrejelzés | Nem | Nem |
> | ExternalSubscriptions / Lekérdezés | Nem | Nem |
> | Előrejelzés | Nem | Nem |
> | Lekérdezés | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | Jelentési konfigurációk | Nem | Nem |
> | Jelentések | Nem | Nem |
> | Beállítások | Nem | Nem |
> | showbackRules | Nem | Nem |
> | Nézetek | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Kérelmek | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Egyesületek | Nem | Nem |
> | erőforrás-szolgáltatók | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Munkahelyek | Igen | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Igen | Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen | Nem |
> | munkaterületek / dbWorkspaces | Nem | Nem |
> | munkaterületek / tárolásTitkosítás | Nem | Nem |
> | munkaterületek / virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Katalógusok | Igen | Igen |
> | adatkatalógusok | Igen | Igen |
> | datacatalogs / adatforrások | Nem | Nem |
> | datacatalogs / adatforrások / vizsgál | Nem | Nem |
> | datacatalogs / adatforrások / vizsgál / adatkészletek | Nem | Nem |
> | datacatalogs / adatforrások / vizsgál / triggerek | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dataGyárak | Igen | Nem |
> | dataFactories / diagnosticSettings | Nem | Nem |
> | dataFactories / metricDefinitions | Nem | Nem |
> | dataFactorySchema | Nem | Nem |
> | Gyárak | Igen | Nem |
> | gyárak / integrációRuntimes | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | fiókok / dataLakeStoreAccounts | Nem | Nem |
> | fiókok / storageAccounts | Nem | Nem |
> | számlák / storageAccounts / konténerek | Nem | Nem |
> | számlák / transferAnalyticsUnits | Nem | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | számlák / eventGridFilters | Nem | Nem |
> | számlák / firewallRules | Nem | Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | services | Nem | Nem |
> | szolgáltatások / projektek | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | számlák / részvények | Nem | Nem |
> | számlák / részvények / adatkészletek | Nem | Nem |
> | számlák / részvények / meghívók | Nem | Nem |
> | számlák / részvények / providersharesubscriptions | Nem | Nem |
> | számlák / megosztások / szinkronizálásBeállítások | Nem | Nem |
> | számlák / sharesubscriptions | Nem | Nem |
> | számlák / sharesubscriptions / consumerSourceDataSets | Nem | Nem |
> | számlák / sharesubscriptions / datasetmappings | Nem | Nem |
> | számlák / sharesubscriptions / triggerek | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Szerverek | Igen | Igen |
> | szerverek / tanácsadók | Nem | Nem |
> | szerverek / kulcsok | Nem | Nem |
> | kiszolgálók / privateEndpointConnectionProxies | Nem | Nem |
> | kiszolgálók / privateEndpointConnections | Nem | Nem |
> | szerverek / privateLinkResources | Nem | Nem |
> | szerverek / queryTexts | Nem | Nem |
> | szerverek / recoverableServers | Nem | Nem |
> | szerverek / topQueryStatistics | Nem | Nem |
> | szerverek / virtualNetworkRules | Nem | Nem |
> | szerverek / waitStatistics | Nem | Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Szerverek | Igen | Igen |
> | szerverek / tanácsadók | Nem | Nem |
> | szerverek / kulcsok | Nem | Nem |
> | kiszolgálók / privateEndpointConnectionProxies | Nem | Nem |
> | kiszolgálók / privateEndpointConnections | Nem | Nem |
> | szerverek / privateLinkResources | Nem | Nem |
> | szerverek / queryTexts | Nem | Nem |
> | szerverek / recoverableServers | Nem | Nem |
> | szerverek / topQueryStatistics | Nem | Nem |
> | szerverek / virtualNetworkRules | Nem | Nem |
> | szerverek / waitStatistics | Nem | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | kiszolgálócsoportok | Igen | Igen |
> | Szerverek | Igen | Igen |
> | szerverek / tanácsadók | Nem | Nem |
> | szerverek / kulcsok | Nem | Nem |
> | kiszolgálók / privateEndpointConnectionProxies | Nem | Nem |
> | kiszolgálók / privateEndpointConnections | Nem | Nem |
> | szerverek / privateLinkResources | Nem | Nem |
> | szerverek / queryTexts | Nem | Nem |
> | szerverek / recoverableServers | Nem | Nem |
> | szerverek / topQueryStatistics | Nem | Nem |
> | szerverek / virtualNetworkRules | Nem | Nem |
> | szerverek / waitStatistics | Nem | Nem |
> | kiszolgálókv2 | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | artifactSources | Igen | Igen |
> | bevezetés | Igen | Igen |
> | serviceTopologies | Igen | Igen |
> | serviceTopologies / szolgáltatások | Igen | Igen |
> | serviceTopologies / szolgáltatások / serviceUnits | Igen | Igen |
> | lépések | Igen | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazáscsoportok | Igen | Igen |
> | alkalmazáscsoportok / alkalmazások | Nem | Nem |
> | alkalmazáscsoportok / asztali számítógépek | Nem | Nem |
> | alkalmazáscsoportok / startmenuitems | Nem | Nem |
> | hostpools | Igen | Igen |
> | hostpools / sessionhosts | Nem | Nem |
> | hostpools / sessionhosts / usersessions | Nem | Nem |
> | hostpools / usersessions | Nem | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | ElasticPools (Rugalmasmedencék) | Igen | Igen |
> | ElasticPools / IotHubTenants | Igen | Igen |
> | ElasticPools / IotHubTenants / securitySettings | Nem | Nem |
> | IotHubok | Igen | Igen |
> | IotHubs / eventGridFilters | Nem | Nem |
> | IotHubs / securityBeállítások | Nem | Nem |
> | ProvisioningServices (Szolgáltatások kiépítése) | Igen | Igen |
> | Használat | Nem | Nem |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Csővezetékek | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Vezérlők | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | laborközpontok | Igen | Igen |
> | Labs | Igen | Igen |
> | laborok / környezetek | Igen | Igen |
> | laborok / szolgáltatásRunners | Igen | Igen |
> | laborok / virtualMachines | Igen | Igen |
> | Menetrend | Igen | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nem | Nem |
> | adatbázis-fiókok | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Tartományok | Igen | Igen |
> | domain / domainOwnershipIdentifiers | Nem | Nem |
> | generateSsoRequest | Nem | Nem |
> | topLevelDomains | Nem | Nem |
> | validateDomainRegistrationInformation | Nem | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nem | Nem |
> | lcsprojects / felhőtelepítés | Nem | Nem |
> | lcsprojects / csatlakozók | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Tartományok | Igen | Igen |
> | domainek / témák | Nem | Nem |
> | eventElőfizetések | Nem | Nem |
> | extensionTopics (Témakörök) | Nem | Nem |
> | partnerNamespaces | Igen | Igen |
> | partnerNamespaces / eventChannels | Nem | Nem |
> | partnerRegisztrációk | Igen | Igen |
> | partnerTémakörök | Igen | Igen |
> | partnerTémakörök / eventSubscriptions | Nem | Nem |
> | systemTopics | Igen | Igen |
> | systemTopics / eventSubscriptions | Nem | Nem |
> | Témák | Igen | Igen |
> | topicTypes | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Igen |
> | Névterek | Igen | Igen |
> | névterek / engedélyezési szabályok | Nem | Nem |
> | névterek / disasterrecoveryconfigs | Nem | Nem |
> | névterek / eventhubs | Nem | Nem |
> | névterek / eventhubs / engedélyezési szabályok | Nem | Nem |
> | névterek / eventhubs / fogyasztói csoportok | Nem | Nem |
> | névterek / hálózati szabályok | Nem | Nem |

## <a name="microsoftfeatures"></a>Microsoft.Szolgáltatások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | funkciók | Nem | Nem |
> | Szolgáltatók | Nem | Nem |

## <a name="microsoftgallery"></a>Microsoft.Galéria

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Beiratkozik | Nem | Nem |
> | galériaelemek | Nem | Nem |
> | generateartifactaccessuri | Nem | Nem |
> | myareas (barátaima) | Nem | Nem |
> | myareas / területek | Nem | Nem |
> | myareas / területek / területek | Nem | Nem |
> | myareas / területek / területek / galériaelemek | Nem | Nem |
> | myareas / területek / galériaelemek | Nem | Nem |
> | myareas / galériaelemek | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | resources | Nem | Nem |
> | beolvasás, resourcesbyid | Nem | Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestKonfiguráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | automatikusfelügyelt fiókok | Igen | Igen |
> | autoManagedVmConfigurationProfiles | Igen | Igen |
> | configurationProfileAssignments (configurationProfileAssignments) | Nem | Nem |
> | guestConfigurationAssignments | Nem | Nem |
> | Szoftver | Nem | Nem |
> | softwareUpdateProfile | Nem | Nem |
> | szoftverfrissítések | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hanaInstances (hanaInstances) | Igen | Igen |
> | sapMonitorok | Igen | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dedikált HSM-ek | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Igen |
> | fürtök / alkalmazások | Nem | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Gépek | Igen | Igen |
> | gépek / kiterjesztések | Igen | Igen |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dataManagers (adatkezelők) | Igen | Igen |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Összetevők | Igen | Igen |
> | hálózati hatókörök | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Munkahelyek | Igen | Igen |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosztikai beállítások | Nem | Nem |
> | diagnosticSettingsCategories (diagnosztikai beállításokkategóriák) | Nem | Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nem | Nem |
> | IoTApps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | töröltVaults | Nem | Nem |
> | hsmPools | Igen | Igen |
> | Boltívek | Igen | Igen |
> | trezorok / accessPolicies | Nem | Nem |
> | boltívek / eventGridFilters | Nem | Nem |
> | boltívek / titkok | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Igen |
> | fürtök / csatoltadatbázis-konfigurációk | Nem | Nem |
> | fürtök / adatbázisok | Nem | Nem |
> | klaszterek / adatbázisok / adatkapcsolatok | Nem | Nem |
> | klaszterek / adatbázisok / eventhubconnections | Nem | Nem |
> | klaszterek / adatbázisok / megbízórendeltségek | Nem | Nem |
> | klaszterek / megbízófeladatok | Nem | Nem |
> | fürtök / megosztott identitások | Nem | Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | laborszámlák | Igen | Igen |
> | felhasználók | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hostingKörnyezet | Igen | Igen |
> | integrációS fiókok | Igen | Igen |
> | integrationServiceEnvironments környezetek | Igen | Igen |
> | integrationServiceEnvironments / managedApis | Igen | Igen |
> | elszigeteltKörnyezetek | Igen | Igen |
> | Munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | kötelezettségvállalásTervek | Igen | Igen |
> | webServices | Igen | Igen |
> | Munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | munkaterületek | Igen | Igen |
> | munkaterületek / számítások | Nem | Nem |
> | munkaterületek / eventGridFilters | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Identitások | Nem | Nem |
> | userAssignedIdentities | Igen | Igen |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | piactérRegistrationDefinitions | Nem | Nem |
> | regisztrációHozzárendelések | Nem | Nem |
> | registrationDefinitions | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | getEntitások | Nem | Nem |
> | managementGroups (felügyeleti csoportok) | Nem | Nem |
> | managementGroups / beállítások | Nem | Nem |
> | resources | Nem | Nem |
> | startTenantBackfill | Nem | Nem |
> | tenantBackfillStatus | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | számlák / eventGridFilters | Nem | Nem |

## <a name="microsoftmarketplace"></a>Microsoft.Piactér

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Kínál | Nem | Nem |
> | offerTypes | Nem | Nem |
> | offerTypes / kiadók | Nem | Nem |
> | offerTypes / kiadók / ajánlatok | Nem | Nem |
> | offerTypes / kiadók / ajánlatok / tervek | Nem | Nem |
> | offerTypes / kiadók / ajánlatok / tervek / megállapodások | Nem | Nem |
> | offerTypes / kiadók / ajánlatok / tervek / configs | Nem | Nem |
> | offerTypes / kiadók / ajánlatok / tervek / configs / importImage | Nem | Nem |
> | privát galériaelemek | Nem | Nem |
> | privateStoreClient | Nem | Nem |
> | Termékek | Nem | Nem |
> | Kiadók | Nem | Nem |
> | kiadók / ajánlatok | Nem | Nem |
> | kiadók / ajánlatok / módosítások | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Igen | Igen |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceRendelés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Megállapodások | Nem | Nem |
> | ajánlattípusok | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | médiaszolgáltatások | Igen | Igen |
> | mediaservices / accountFilters | Nem | Nem |
> | mediaservices / eszközök | Nem | Nem |
> | mediaservices / eszközök / assetFilters | Nem | Nem |
> | mediaservices / contentKeyPolicies | Nem | Nem |
> | mediaservices / eventGridFilters | Nem | Nem |
> | mediaservices / liveEventOperations | Nem | Nem |
> | mediaservices / liveEvents | Igen | Igen |
> | mediaservices / liveEvents / liveOutputs | Nem | Nem |
> | mediaservices / liveOutputOperations | Nem | Nem |
> | mediaservices / mediaGraphs | Nem | Nem |
> | mediaservices / streamingEndpointOperations | Nem | Nem |
> | mediaservices / streamingEndpoints | Igen | Igen |
> | mediaservices / streamingLocators | Nem | Nem |
> | mediaservices / streamingPolicies | Nem | Nem |
> | mediaservices / átalakítások | Nem | Nem |
> | mediaservices / átalakítja / munkahelyek | Nem | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Tavasz

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | appClusters | Igen | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Áttelepítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | értékelésProjektek | Igen | Igen |
> | projektek áttelepítése | Igen | Igen |
> | moveCollections (gyűjtemények) | Igen | Igen |
> | Projektek | Igen | Igen |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Igen | Igen |
> | objectUnderstandingAccounts | Igen | Igen |
> | remoteRenderingAccounts fiókok | Igen | Igen |
> | spatialAnchorsAccounts | Igen | Igen |
> | surfaceReconstructionAccounts | Igen | Igen |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Igen | Nem |
> | netAppAccounts / capacityPools | Igen | Nem |
> | netAppAccounts / capacityPools / kötetek | Igen | Nem |
> | netAppAccounts / capacityPools / kötetek / pillanatképek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazásátjárók | Igen | Igen |
> | alkalmazásGatewayWebApplicationFirewallPolicies | Igen | Igen |
> | alkalmazásSecurityGroups | Igen | Igen |
> | azureFirewallFqdnTags | Nem | Nem |
> | azureTűzfalak | Igen | Nem |
> | bástyaHázigazdák | Igen | Igen |
> | bgpServiceKözösségek | Nem | Nem |
> | Kapcsolatok | Igen | Igen |
> | ddosCustomPolicies | Igen | Igen |
> | ddosProtectionPlans | Igen | Igen |
> | dnsOperationStatuses | Nem | Nem |
> | dnszones között | Igen | Igen |
> | dnszones / A | Nem | Nem |
> | dnszones / AAAA | Nem | Nem |
> | dnszones / minden | Nem | Nem |
> | dnszones / CAA | Nem | Nem |
> | dnszones / CNAME | Nem | Nem |
> | dnszones / MX | Nem | Nem |
> | dnszones / NS | Nem | Nem |
> | dnszones / PTR | Nem | Nem |
> | dnszones / rekordhalmazok | Nem | Nem |
> | dnszones / SOA | Nem | Nem |
> | dnszones / SRV | Nem | Nem |
> | dnszones / TXT | Nem | Nem |
> | expressRouteCircuits | Igen | Igen |
> | expressRouteCrossConnections | Igen | Igen |
> | expresszRouteÁtjárók | Igen | Igen |
> | expressRoutePorts | Igen | Igen |
> | expresszRouteServiceProviders | Nem | Nem |
> | firewallPolicies (tűzfalházirendek) | Igen | Igen |
> | elülső ajtók | Igen, de korlátozott (lásd [az alábbi megjegyzést)](#frontdoor) | Igen |
> | frontdoorWebApplicationFirewallManagedRuleSets | Igen, de korlátozott (lásd [az alábbi megjegyzést)](#frontdoor) | Nem |
> | frontdoorWebApplicationFirewallPolicies | Igen, de korlátozott (lásd [az alábbi megjegyzést)](#frontdoor) | Igen |
> | getDnsResourceReference | Nem | Nem |
> | internalNotify | Nem | Nem |
> | terheléselosztók | Igen | Nem |
> | localNetworkGateways | Igen | Igen |
> | natGateways | Igen | Igen |
> | hálózatiIntentPolicies | Igen | Igen |
> | hálózati interfészek | Igen | Igen |
> | hálózati profilok | Igen | Igen |
> | networkSecurityGroups (hálózatibiztonsági csoportok) | Igen | Igen |
> | networkWatchers | Igen | Nem |
> | networkWatchers / connectionMonitors | Igen | Nem |
> | networkWatchers / objektívek | Igen | Nem |
> | networkWatchers / pingMeshes | Igen | Nem |
> | p2sVpnGateways | Igen | Igen |
> | magánDnsOperationStatuses | Nem | Nem |
> | privátDnsZones | Igen | Igen |
> | privateDnsZones / A | Nem | Nem |
> | privateDnsZones / AAAA | Nem | Nem |
> | privateDnsZones / minden | Nem | Nem |
> | privateDnsZones / CNAME | Nem | Nem |
> | privateDnsZones / MX | Nem | Nem |
> | privateDnsZones / PTR | Nem | Nem |
> | privateDnsZones / SOA | Nem | Nem |
> | privateDnsZones / SRV | Nem | Nem |
> | privateDnsZones / TXT | Nem | Nem |
> | privateDnsZones / virtualNetworkLinks | Igen | Igen |
> | privateEndpoints | Igen | Igen |
> | privateLinkServices | Igen | Igen |
> | nyilvánosIP-címek | Igen | Igen |
> | nyilvános IPPrefixes | Igen | Igen |
> | routeFilters | Igen | Igen |
> | routeTables (útvonaltáblák) | Igen | Igen |
> | serviceEndpointPolicies | Igen | Igen |
> | trafficManagerGeographicHierarchies | Nem | Nem |
> | trafficmanagerprofiles (trafficmanagerprofiles) | Igen | Igen |
> | trafficmanagerprofiles/heatMaps | Nem | Nem |
> | trafficManagerUserMetricsKeys | Nem | Nem |
> | virtualHubs | Igen | Igen |
> | virtualNetworkGateways | Igen | Igen |
> | virtualNetworks | Igen | Igen |
> | virtualNetworkTaps | Igen | Igen |
> | virtualWans | Igen | Igen |
> | vpnGateways | Igen | Nem |
> | vpnOldalak | Igen | Igen |
> | webApplicationFirewallPolicies | Igen | Igen |

<a id="frontdoor" />

> [!NOTE]
> Az Azure Bejárati ajtajának szolgáltatás esetén címkéket alkalmazhat az erőforrás létrehozásakor, de a címkék frissítése vagy hozzáadása jelenleg nem támogatott.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Nem | Nem |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Nem |
> | névterek / értesítésHubs | Igen | Nem |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | osNamespaces között | Igen | Igen |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Hiperv-oldalak | Igen | Igen |
> | Importwebhelyek | Igen | Igen |
> | Kiszolgálówebhelyek | Igen | Igen |
> | VMware Oldalak | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Klaszterek | Igen | Igen |
> | linkTargets | Nem | Nem |
> | storageInsightConfigs | Nem | Nem |
> | munkaterületek | Igen | Igen |
> | munkaterületek / dataExports | Nem | Nem |
> | munkaterületek / dataSources | Nem | Nem |
> | munkaterületek / linkedServices | Nem | Nem |
> | munkaterületek / privateEndpointConnectionProxies | Nem | Nem |
> | munkaterületek / privateEndpointConnections | Nem | Nem |
> | munkaterületek / privateLinkResources | Nem | Nem |
> | munkaterületek / lekérdezés | Nem | Nem |
> | munkaterületek / scopedPrivateLinkProxies | Nem | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | managementegyesületek | Nem | Nem |
> | felügyeleti konfigurációk | Igen | Igen |
> | megoldások | Igen | Igen |
> | Kilátás nyílik | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft.Társviszony-létesítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nem | Nem |
> | peerAsns között | Nem | Nem |
> | társviszony | Igen | Igen |
> | peeringServiceOrszágok | Nem | Nem |
> | társviszony-létesítőszolgáltatók | Nem | Nem |
> | társviszony-létesítési szolgáltatások | Igen | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | policyEvents (házirendEsemények) | Nem | Nem |
> | policyMetadata | Nem | Nem |
> | policyStates (politikai államok) | Nem | Nem |
> | policyTrackedResources (házirendNyomonforrások) | Nem | Nem |
> | kármentesítések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> |  -konzolok | Nem | Nem |
> | irányítópultok | Igen | Igen |
> | UserSettings | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | workspaceGyűjtemények | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Kapacitások | Igen | Igen |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nem | Nem |
> | Boltívek | Igen | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Igen |
> | névterek / engedélyezési szabályok | Nem | Nem |
> | névterek / hibrid kapcsolatok | Nem | Nem |
> | névterek / hibridkapcsolatok / engedélyezési szabályok | Nem | Nem |
> | névterek / wcfrelays | Nem | Nem |
> | névterek / wcfrelays / authorizationrules | Nem | Nem |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | fiókok | Nem | Nem |
> | Gyűjtemények | Igen | Igen |
> | gyűjtemények / alkalmazások | Nem | Nem |
> | gyűjtemények / biztonsági igazgatók | Nem | Nem |
> | sablonKépek | Nem | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Lekérdezések | Igen | Igen |
> | resourceChangeDetails | Nem | Nem |
> | erőforrás-változások | Nem | Nem |
> | resources | Nem | Nem |
> | resourcesHistory | Nem | Nem |
> | előfizetésekÁllapota | Nem | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nem | Nem |
> | childAvailabilityStatuses | Nem | Nem |
> | childResources (gyermekerőforrások) | Nem | Nem |
> | feltörekvő szövetek | Nem | Nem |
> | események | Nem | Nem |
> | érintett források | Nem | Nem |
> | metaadatok | Nem | Nem |
> | Értesítések | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Telepítések | Igen | Nem |
> | telepítések / műveletek | Nem | Nem |
> | központi parancsfájlok | Igen | Igen |
> | deploymentScripts / naplók | Nem | Nem |
> | Linkek | Nem | Nem |
> | notifyResourceJobs | Nem | Nem |
> | Szolgáltatók | Nem | Nem |
> | erőforráscsoportok | Igen | Nem |
> | Előfizetések | Igen | Nem |
> | Bérlők | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Igen |
> | saasresources között | Nem | Nem |

## <a name="microsoftsearch"></a>Microsoft.Keresés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nem | Nem |
> | searchServices | Igen | Igen |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkEdzések | Nem | Nem |
> | advancedThreatProtectionSettings | Nem | Nem |
> | riasztások | Nem | Nem |
> | engedélyezettkapcsolatok | Nem | Nem |
> | alkalmazásWhitelistings | Nem | Nem |
> | értékelésMetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | autoDismissAlertsRules | Nem | Nem |
> | automatizálások | Igen | Igen |
> | AutoProvisioningBeállítások | Nem | Nem |
> | Megfelelőség | Nem | Nem |
> | dataCollectionAgents | Nem | Nem |
> | deviceSecurityGroups | Nem | Nem |
> | discoveredSecuritySolutions | Nem | Nem |
> | externalSecuritySolutions | Nem | Nem |
> | InformationProtectionPolicies (Információvédelmi irányelvek) | Nem | Nem |
> | iotSecuritySolutions | Igen | Igen |
> | iotSecuritySolutions / analyticsModellek | Nem | Nem |
> | iotSecuritySolutions / analyticsModellek / aggregatedAlerts | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nem | Nem |
> | jitNetworkAccessPolicies | Nem | Nem |
> | hálózati adatok | Nem | Nem |
> | policies | Nem | Nem |
> | árak | Nem | Nem |
> | szabályozásiMegfelelőségi szabványok | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nem | Nem |
> | biztonsági partnerek | Nem | Nem |
> | securitySolutions (biztonsági megoldások) | Nem | Nem |
> | securitySolutionsReferenceData | Nem | Nem |
> | securityStatuses | Nem | Nem |
> | securityStatusesSummaries | Nem | Nem |
> | kiszolgálóbiztonsági értékelések | Nem | Nem |
> | beállítások | Nem | Nem |
> | alértékelések | Nem | Nem |
> | feladatok | Nem | Nem |
> | Topológiák | Nem | Nem |
> | munkaterületBeállítások | Nem | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosztikai beállítások | Nem | Nem |
> | diagnosticSettingsCategories (diagnosztikai beállításokkategóriák) | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Összesítései | Nem | Nem |
> | alertRules (figyelmeztetés) | Nem | Nem |
> | alertRuleTemplates (figyelmeztetésSzabálysablonok) | Nem | Nem |
> | Könyvjelzők | Nem | Nem |
> | Esetekben | Nem | Nem |
> | dataConnectors | Nem | Nem |
> | dataConnectorsCheckRequirements | Nem | Nem |
> | Entitások | Nem | Nem |
> | entityQueries | Nem | Nem |
> | Események | Nem | Nem |
> | officeConsents (hivatalbeleegyezések) | Nem | Nem |
> | beállítások | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Névterek | Igen | Nem |
> | névterek / engedélyezési szabályok | Nem | Nem |
> | névterek / disasterrecoveryconfigs | Nem | Nem |
> | névterek / eventgridfilters | Nem | Nem |
> | névterek / hálózati szabályok | Nem | Nem |
> | névterek / várólisták | Nem | Nem |
> | névterek / várólisták / engedélyezési szabályok | Nem | Nem |
> | névterek / témakörök | Nem | Nem |
> | névterek / témakörök / engedélyezési szabályok | Nem | Nem |
> | névterek / témák / előfizetések | Nem | Nem |
> | névterek / témák / előfizetések / szabályok | Nem | Nem |
> | premiumMessagingRegions (prémiumüzenetekRégiói) | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Igen |
> | Klaszterek | Igen | Igen |
> | fürtök / alkalmazások | Nem | Nem |
> | containerGroups (tárolócsoportok) | Igen | Igen |
> | containerGroupSets | Igen | Igen |
> | élhalmazok | Igen | Igen |
> | edgeclusters / alkalmazások | Nem | Nem |
> | kezelt klaszterek | Igen | Igen |
> | managedclusters / nodetypes | Nem | Nem |
> | Hálózatok | Igen | Igen |
> | titkos üzletek | Igen | Igen |
> | titkos üzletek / tanúsítványok | Nem | Nem |
> | titkos üzletek / titkok | Nem | Nem |
> | volumes | Igen | Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazások | Igen | Igen |
> | containerGroups (tárolócsoportok) | Igen | Igen |
> | Átjárók | Igen | Igen |
> | Hálózatok | Igen | Igen |
> | Titkok | Igen | Igen |
> | volumes | Igen | Igen |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | providerRegistrations (Regisztrációk) | Nem | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem | Nem |
> | bevezetés | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | SignalR | Igen | Igen |
> | SignalR / eventGridFilters | Nem | Nem |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Igen | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | hibridElőnyök | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Megoldások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | alkalmazásdefiníciók | Igen | Igen |
> | alkalmazások | Igen | Igen |
> | jitRequests | Igen | Igen |

## <a name="microsoftspoolservice"></a>Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | registeredElőfizetések | Nem | Nem |
> | Orsók | Igen | Igen |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | managedInstances (példányok) | Igen | Igen |
> | managedInstances / adatbázisok | Igen (lásd [az alábbi megjegyzést)](#sqlnote) | Igen |
> | managedInstances / adatbázisok / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / adatbázisok / sémák / táblák / oszlopok / érzékenységCímkék | Nem | Nem |
> | managedInstances / adatbázisok / sebezhetőségÉrtékelések | Nem | Nem |
> | managedInstances / adatbázisok / sebezhetőségÉrtékelések / szabályok / alaptervek | Nem | Nem |
> | managedInstances / encryptionProtector | Nem | Nem |
> | managedInstances / kulcsok | Nem | Nem |
> | managedInstances / restoreableDroppedDatabases / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / vulnerabilityAssessments | Nem | Nem |
> | Szerverek | Igen | Igen |
> | szerverek / rendszergazdák | Nem | Nem |
> | szerverek / communicationLinks | Nem | Nem |
> | szerverek / adatbázisok | Igen (lásd [az alábbi megjegyzést)](#sqlnote) | Igen |
> | szerverek / encryptionProtector | Nem | Nem |
> | szerverek / firewallRules | Nem | Nem |
> | szerverek / kulcsok | Nem | Nem |
> | szerverek / helyreállíthatóDroppedadatbázisok | Nem | Nem |
> | szerverek / szolgáltatáscélok | Nem | Nem |
> | kiszolgálók / tdeCertificates | Nem | Nem |
> | virtualClusters | Nem | Nem |

<a id="sqlnote" />

> [!NOTE]
> A Master adatbázis nem támogatja a címkéket, de más adatbázisok, beleértve az Azure SQL Data Warehouse-adatbázisokat, támogatják a címkéket. Az Azure SQL Data Warehouse-adatbázisoknak aktív (nem szüneteltetett) állapotban kell lenniük.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Igen | Igen |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nem | Nem |
> | SqlVirtualMachines | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Igen | Igen |
> | storageAccounts / blobServices | Nem | Nem |
> | storageAccounts / fileServices | Nem | Nem |
> | storageAccounts / queueServices | Nem | Nem |
> | storageAccounts / szolgáltatások | Nem | Nem |
> | storageAccounts / szolgáltatások / metricDefinitions | Nem | Nem |
> | storageAccounts / tableServices | Nem | Nem |
> | Használat | Nem | Nem |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Elrejt | Igen | Igen |
> | gyorsítótárak / tárolókCélok | Nem | Nem |
> | usageModellek | Nem | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | replikációscsoportok | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Igen |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Igen |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Igen |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / munkafolyamatok | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Vezetők | Igen | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Igen (lásd az alábbi megjegyzést) | Igen |

> [!NOTE]
> Nem adhat hozzá címkét, ha streamelési feladatok fut. Állítsa le az erőforrást címke hozzáadásához.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | mégse | Nem | Nem |
> | CreateSubscription (Előfizetés létrehozása) | Nem | Nem |
> | Engedélyezi | Nem | Nem |
> | átnevezés | Nem | Nem |
> | Előfizetésdefiníciók | Nem | Nem |
> | Előfizetési műveletek | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Környezetben | Igen | Nem |
> | környezetek / accessPolicies | Nem | Nem |
> | környezetek / eseményforrások | Igen | Nem |
> | környezetek / referenceDataSets | Igen | Nem |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | dedikáltCloudNodes | Igen | Igen |
> | dedikáltCloudServices | Igen | Igen |
> | virtualMachines | Igen | Igen |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | eszközök | Igen | Igen |
> | registeredElőfizetések | Nem | Nem |
> | Szállítók | Nem | Nem |
> | eladók / skus | Nem | Nem |
> | gyártók / vnfs | Nem | Nem |
> | vnfs | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nem | Nem |
> | apiManagementAccounts / apiAcls | Nem | Nem |
> | apiManagementAccounts / apis | Nem | Nem |
> | apiManagementAccounts / apis / apiAcls | Nem | Nem |
> | apiManagementAccounts / apis / connectionAcls | Nem | Nem |
> | apiManagementAccounts / apis / kapcsolatok | Nem | Nem |
> | apiManagementAccounts / apis / kapcsolatok / connectionAcls | Nem | Nem |
> | apiManagementAccounts / apis / localizedDefinitions | Nem | Nem |
> | apiManagementAccounts / connectionAcls | Nem | Nem |
> | apiManagementAccounts / kapcsolatok | Nem | Nem |
> | billingMéterek | Nem | Nem |
> | tanúsítványok | Igen | Igen |
> | connectionGateways (átjárók) | Igen | Igen |
> | Kapcsolatok | Igen | Igen |
> | egyéniApis | Igen | Igen |
> | törölt webhelyek | Nem | Nem |
> | hostingKörnyezet | Igen | Igen |
> | hostingEnvironments / eventGridFilters | Nem | Nem |
> | hostingEnvironments / multiRolePools | Nem | Nem |
> | hostingEnvironments / workerPools | Nem | Nem |
> | közzétételFelhasználók | Nem | Nem |
> | Ajánlások | Nem | Nem |
> | resourceHealthMetadata | Nem | Nem |
> | futási idők | Nem | Nem |
> | szerverFarms | Igen | Igen |
> | serverFarms / eventGridFilters | Nem | Nem |
> | Helyek | Igen | Igen |
> | oldalak / config  | Nem | Nem |
> | oldalak / eventGridFilters | Nem | Nem |
> | oldalak / hostNameBindings | Nem | Nem |
> | oldalak / networkConfig | Nem | Nem |
> | oldalak / premieraddons | Igen | Igen |
> | oldalak / bővítőhely | Igen | Igen |
> | oldalak / bővítőhely / eventGridFilters | Nem | Nem |
> | oldalak / bővítőhely / hostNameBindings | Nem | Nem |
> | oldalak / bővítőhely / networkConfig | Nem | Nem |
> | sourceControls | Nem | Nem |
> | statikus webhelyek | Igen | Igen |
> | Érvényesít | Nem | Nem |
> | verifyHostingEnvironmentVnet | Nem | Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | diagnosztikai beállítások | Nem | Nem |
> | diagnosticSettingsCategories (diagnosztikai beállításokkategóriák) | Nem | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Igen | Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadFigyelő

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Támogatja a címkéket | Címke a költségjelentésben |
> | ------------- | ----------- | ----------- |
> | Összetevők | Nem | Nem |
> | componentsSummary | Nem | Nem |
> | monitorInstances | Nem | Nem |
> | monitorInstancesSummary | Nem | Nem |
> | Monitorok | Nem | Nem |
> | értesítésBeállítások | Nem | Nem |

## <a name="next-steps"></a>További lépések

Ha tudni szeretné, hogyan alkalmazhat címkéket az erőforrásokra, olvassa el [a Címkék használata az Azure-erőforrások rendszerezéséhez (Címkék használata az Azure-erőforrások rendszerezéséhez) témakört.](tag-resources.md)
