---
title: Tag support for resources
description: Shows which Azure resource types support tags. Provides details for all Azure services.
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422142"
---
# <a name="tag-support-for-azure-resources"></a>Tag support for Azure resources
This article describes whether a resource type supports [tags](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report. You can view costs by tags in the [Cost Management cost analysis](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) and the [Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md).

To get the same data as a file of comma-separated values, download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
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
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
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
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
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
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
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
> - [Microsoft.Solutions](#microsoftsolutions)
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
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | Igen | Igen |
> | DomainServices / oucontainer | Nem | Nem |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Nem | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Nem | Nem |
> | addsservices | Nem | Nem |
> | agents | Nem | Nem |
> | anonymousapiusers | Nem | Nem |
> | konfiguráció | Nem | Nem |
> | naplók | Nem | Nem |
> | reports | Nem | Nem |
> | servicehealthmetrics | Nem | Nem |
> | services | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurations | Nem | Nem |
> | generateRecommendations | Nem | Nem |
> | metaadatok | Nem | Nem |
> | recommendations | Nem | Nem |
> | suppressions | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | Igen | Igen |
> | riasztások | Nem | Nem |
> | alertsList | Nem | Nem |
> | alertsMetaData | Nem | Nem |
> | alertsSummary | Nem | Nem |
> | alertsSummaryList | Nem | Nem |
> | feedback | Nem | Nem |
> | smartDetectorAlertRules | Igen | Igen |
> | smartDetectorRuntimeEnvironments | Nem | Nem |
> | smartGroups | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Nem | Nem |
> | szolgáltatás | Igen | Igen |
> | validateServiceName | Nem | Nem |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Igen | Igen |
> | configurationStores / eventGridFilters | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | Igen | Igen |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Nem | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Nem | Nem |
> | dataAliases | Nem | Nem |
> | denyAssignments | Nem | Nem |
> | elevateAccess | Nem | Nem |
> | findOrphanRoleAssignments | Nem | Nem |
> | locks | Nem | Nem |
> | permissions | Nem | Nem |
> | policyAssignments | Nem | Nem |
> | policyDefinitions | Nem | Nem |
> | policySetDefinitions | Nem | Nem |
> | providerOperations | Nem | Nem |
> | roleAssignments | Nem | Nem |
> | roleDefinitions | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Igen | Igen |
> | automationAccounts / configurations | Igen | Igen |
> | automationAccounts / jobs | Nem | Nem |
> | automationAccounts / runbooks | Igen | Igen |
> | automationAccounts / softwareUpdateConfigurations | Nem | Nem |
> | automationAccounts / webhooks | Nem | Nem |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Igen | Igen |
> | configurationStores / eventGridFilters | Nem | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Nem | Nem |
> | environments / accounts | Nem | Nem |
> | environments / accounts / namespaces | Nem | Nem |
> | environments / accounts / namespaces / configurations | Nem | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Igen | Nem |
> | b2ctenants | Nem | Nem |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Igen | Igen |
> | postgresInstances | Igen | Igen |
> | sqlBigDataClusters | Igen | Igen |
> | sqlInstances | Igen | Igen |
> | sqlServerRegistrations | Igen | Igen |
> | sqlServerRegistrations / sqlServers | Nem | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | Igen | Igen |
> | registrations / customerSubscriptions | Nem | Nem |
> | registrations / products | Nem | Nem |
> | verificationKeys | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Igen | Igen |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Nem | Nem |
> | billingAccounts / agreements | Nem | Nem |
> | billingAccounts / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles | Nem | Nem |
> | billingAccounts / billingProfiles / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / customers | Nem | Nem |
> | billingAccounts / billingProfiles / invoices | Nem | Nem |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nem | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nem | Nem |
> | billingAccounts / BillingProfiles / patchOperations | Nem | Nem |
> | billingAccounts / billingProfiles / paymentMethods | Nem | Nem |
> | billingAccounts / billingProfiles / policies | Nem | Nem |
> | billingAccounts / billingProfiles / pricesheet | Nem | Nem |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nem | Nem |
> | billingAccounts / billingProfiles / products | Nem | Nem |
> | billingAccounts / billingProfiles / transactions | Nem | Nem |
> | billingAccounts / billingRoleAssignments | Nem | Nem |
> | billingAccounts / billingRoleDefinitions | Nem | Nem |
> | billingAccounts / billingSubscriptions | Nem | Nem |
> | billingAccounts / billingSubscriptions / invoices | Nem | Nem |
> | billingAccounts / createBillingRoleAssignment | Nem | Nem |
> | billingAccounts / createInvoiceSectionOperations | Nem | Nem |
> | billingAccounts / customers | Nem | Nem |
> | billingAccounts / customers / billingPermissions | Nem | Nem |
> | billingAccounts / customers / billingSubscriptions | Nem | Nem |
> | billingAccounts / customers / initiateTransfer | Nem | Nem |
> | billingAccounts / customers / policies | Nem | Nem |
> | billingAccounts / customers / products | Nem | Nem |
> | billingAccounts / customers / transactions | Nem | Nem |
> | billingAccounts / customers / transfers | Nem | Nem |
> | billingAccounts / departments | Nem | Nem |
> | billingAccounts / enrollmentAccounts | Nem | Nem |
> | billingAccounts / invoices | Nem | Nem |
> | billingAccounts / invoiceSections | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions | Nem | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nem | Nem |
> | billingAccounts / invoiceSections / elevate | Nem | Nem |
> | billingAccounts / invoiceSections / initiateTransfer | Nem | Nem |
> | billingAccounts / invoiceSections / patchOperations | Nem | Nem |
> | billingAccounts / invoiceSections / productMoveOperations | Nem | Nem |
> | billingAccounts / invoiceSections / products | Nem | Nem |
> | billingAccounts / invoiceSections / products / transfer | Nem | Nem |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nem | Nem |
> | billingAccounts / invoiceSections / transactions | Nem | Nem |
> | billingAccounts / invoiceSections / transfers | Nem | Nem |
> | billingAccounts / lineOfCredit | Nem | Nem |
> | billingAccounts / patchOperations | Nem | Nem |
> | billingAccounts / paymentMethods | Nem | Nem |
> | billingAccounts / products | Nem | Nem |
> | billingAccounts / transactions | Nem | Nem |
> | billingPeriods | Nem | Nem |
> | billingPermissions | Nem | Nem |
> | billingProperty | Nem | Nem |
> | billingRoleAssignments | Nem | Nem |
> | billingRoleDefinitions | Nem | Nem |
> | createBillingRoleAssignment | Nem | Nem |
> | departments | Nem | Nem |
> | enrollmentAccounts | Nem | Nem |
> | invoices | Nem | Nem |
> | transfers | Nem | Nem |
> | transfers / acceptTransfer | Nem | Nem |
> | transfers / declineTransfer | Nem | Nem |
> | transfers / operationStatus | Nem | Nem |
> | transfers / validateTransfer | Nem | Nem |
> | validateAddress | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | Igen | Igen |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Igen | Igen |
> | cordaMembers | Igen | Igen |
> | néző | Igen | Igen |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Nem | Nem |
> | blueprintAssignments / assignmentOperations | Nem | Nem |
> | blueprintAssignments / operations | Nem | Nem |
> | blueprints | Nem | Nem |
> | blueprints / artifacts | Nem | Nem |
> | blueprints / versions | Nem | Nem |
> | blueprints / versions / artifacts | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | Igen | Igen |
> | botServices / channels | Nem | Nem |
> | botServices / connections | Nem | Nem |
> | languages | Nem | Nem |
> | sablonok | Nem | Nem |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | Igen | Igen |
> | RedisConfigDefinition | Nem | Nem |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Nem | Nem |
> | calculateExchange | Nem | Nem |
> | calculatePrice | Nem | Nem |
> | calculatePurchasePrice | Nem | Nem |
> | catalogs | Nem | Nem |
> | commercialReservationOrders | Nem | Nem |
> | exchange | Nem | Nem |
> | placePurchaseOrder | Nem | Nem |
> | reservationOrders | Nem | Nem |
> | reservationOrders / calculateRefund | Nem | Nem |
> | reservationOrders / merge | Nem | Nem |
> | reservationOrders / reservations | Nem | Nem |
> | reservationOrders / reservations / revisions | Nem | Nem |
> | reservationOrders / return | Nem | Nem |
> | reservationOrders / split | Nem | Nem |
> | reservationOrders / swap | Nem | Nem |
> | reservations | Nem | Nem |
> | resources | Nem | Nem |
> | validateReservationOrder | Nem | Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem | Nem |
> | CdnWebApplicationFirewallPolicies | Igen | Igen |
> | edgenodes | Nem | Nem |
> | profiles | Igen | Igen |
> | profiles / endpoints | Igen | Igen |
> | profiles / endpoints / customdomains | Nem | Nem |
> | profiles / endpoints / origins | Nem | Nem |
> | validateProbe | Nem | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Igen | Igen |
> | certificateOrders / certificates | Nem | Nem |
> | validateCertificateRegistrationInformation | Nem | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nem | Nem |
> | domainNames | Nem | Nem |
> | domainNames / capabilities | Nem | Nem |
> | domainNames / internalLoadBalancers | Nem | Nem |
> | domainNames / serviceCertificates | Nem | Nem |
> | domainNames / slots | Nem | Nem |
> | domainNames / slots / roles | Nem | Nem |
> | domainNames / slots / roles / metricDefinitions | Nem | Nem |
> | domainNames / slots / roles / metrics | Nem | Nem |
> | moveSubscriptionResources | Nem | Nem |
> | operatingSystemFamilies | Nem | Nem |
> | operatingSystems | Nem | Nem |
> | kvóták | Nem | Nem |
> | resourceTypes | Nem | Nem |
> | validateSubscriptionMoveAvailability | Nem | Nem |
> | virtualMachines | Nem | Nem |
> | virtualMachines / diagnosticSettings | Nem | Nem |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachines / metrics | Nem | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Nem | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nem | Nem |
> | expressRouteCrossConnections | Nem | Nem |
> | expressRouteCrossConnections / peerings | Nem | Nem |
> | gatewaySupportedDevices | Nem | Nem |
> | networkSecurityGroups | Nem | Nem |
> | kvóták | Nem | Nem |
> | reservedIps | Nem | Nem |
> | virtualNetworks | Nem | Nem |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nem | Nem |
> | virtualNetworks / virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Nem | Nem |
> | disks | Nem | Nem |
> | images | Nem | Nem |
> | osImages | Nem | Nem |
> | osPlatformImages | Nem | Nem |
> | publicImages | Nem | Nem |
> | kvóták | Nem | Nem |
> | storageAccounts | Nem | Nem |
> | storageAccounts / blobServices | Nem | Nem |
> | storageAccounts / fileServices | Nem | Nem |
> | storageAccounts / metricDefinitions | Nem | Nem |
> | storageAccounts / metrics | Nem | Nem |
> | storageAccounts / queueServices | Nem | Nem |
> | storageAccounts / services | Nem | Nem |
> | storageAccounts / services / diagnosticSettings | Nem | Nem |
> | storageAccounts / services / metricDefinitions | Nem | Nem |
> | storageAccounts / services / metrics | Nem | Nem |
> | storageAccounts / tableServices | Nem | Nem |
> | storageAccounts / vmImages | Nem | Nem |
> | vmImages | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Nem | Nem |
> | UsageAggregates | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Igen | Igen |
> | diskEncryptionSets | Igen | Igen |
> | disks | Igen | Igen |
> | galleries | Igen | Igen |
> | galleries / applications | Nem | Nem |
> | galleries / applications / versions | Nem | Nem |
> | galleries / images | Nem | Nem |
> | galleries / images / versions | Nem | Nem |
> | hostGroups | Igen | Igen |
> | hostGroups / hosts | Igen | Igen |
> | images | Igen | Igen |
> | proximityPlacementGroups | Igen | Igen |
> | restorePointCollections | Igen | Igen |
> | restorePointCollections / restorePoints | Nem | Nem |
> | sharedVMImages | Igen | Igen |
> | sharedVMImages / versions | Nem | Nem |
> | pillanatképek | Igen | Igen |
> | virtualMachines | Igen | Igen |
> | virtualMachines / extensions | Igen | Igen |
> | virtualMachines / metricDefinitions | Nem | Nem |
> | virtualMachineScaleSets | Igen | Igen |
> | virtualMachineScaleSets / extensions | Nem | Nem |
> | virtualMachineScaleSets / networkInterfaces | Nem | Nem |
> | virtualMachineScaleSets / publicIPAddresses | Nem | Nem |
> | virtualMachineScaleSets / virtualMachines | Nem | Nem |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nem | Nem |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Nem | Nem |
> | Egyenlegek | Nem | Nem |
> | Költségkeret | Nem | Nem |
> | Díjak | Nem | Nem |
> | CostTags | Nem | Nem |
> | credits | Nem | Nem |
> | események | Nem | Nem |
> | Előrejelzések | Nem | Nem |
> | lots | Nem | Nem |
> | Marketplaces | Nem | Nem |
> | Pricesheets | Nem | Nem |
> | products | Nem | Nem |
> | ReservationDetails | Nem | Nem |
> | ReservationRecommendations | Nem | Nem |
> | ReservationSummaries | Nem | Nem |
> | ReservationTransactions | Nem | Nem |
> | Címkék | Nem | Nem |
> | tenants | Nem | Nem |
> | Fogalmak | Nem | Nem |
> | UsageDetails | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | Igen | Igen |
> | serviceAssociationLinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | Igen | Igen |
> | registries / builds | Nem | Nem |
> | registries / builds / cancel | Nem | Nem |
> | registries / builds / getLogLink | Nem | Nem |
> | registries / buildTasks | Igen | Igen |
> | registries / buildTasks / steps | Nem | Nem |
> | registries / eventGridFilters | Nem | Nem |
> | registries / generateCredentials | Nem | Nem |
> | registries / getBuildSourceUploadUrl | Nem | Nem |
> | registries / GetCredentials | Nem | Nem |
> | registries / importImage | Nem | Nem |
> | registries / queueBuild | Nem | Nem |
> | registries / regenerateCredential | Nem | Nem |
> | registries / regenerateCredentials | Nem | Nem |
> | registries / replications | Igen | Igen |
> | registries / runs | Nem | Nem |
> | registries / runs / cancel | Nem | Nem |
> | registries / scheduleRun | Nem | Nem |
> | registries / scopeMaps | Nem | Nem |
> | registries / taskRuns | Igen | Igen |
> | registries / tasks | Igen | Igen |
> | registries / tokens | Nem | Nem |
> | registries / updatePolicies | Nem | Nem |
> | registries / webhooks | Igen | Igen |
> | registries / webhooks / getCallbackConfig | Nem | Nem |
> | registries / webhooks / ping | Nem | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | Igen | Igen |
> | managedClusters | Igen | Igen |
> | openShiftManagedClusters | Igen | Igen |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Értesítések | Nem | Nem |
> | BillingAccounts | Nem | Nem |
> | Költségkeret | Nem | Nem |
> | CloudConnectors | Nem | Nem |
> | Összekötők | Igen | Igen |
> | Departments | Nem | Nem |
> | Dimenziók | Nem | Nem |
> | EnrollmentAccounts | Nem | Nem |
> | Exportálások | Nem | Nem |
> | ExternalBillingAccounts | Nem | Nem |
> | ExternalBillingAccounts / Alerts | Nem | Nem |
> | ExternalBillingAccounts / Dimensions | Nem | Nem |
> | ExternalBillingAccounts / Forecast | Nem | Nem |
> | ExternalBillingAccounts / Query | Nem | Nem |
> | ExternalSubscriptions | Nem | Nem |
> | ExternalSubscriptions / Alerts | Nem | Nem |
> | ExternalSubscriptions / Dimensions | Nem | Nem |
> | ExternalSubscriptions / Forecast | Nem | Nem |
> | ExternalSubscriptions / Query | Nem | Nem |
> | Forecast | Nem | Nem |
> | Lekérdezés | Nem | Nem |
> | register | Nem | Nem |
> | Reportconfigs | Nem | Nem |
> | Jelentések | Nem | Nem |
> | Beállítások | Nem | Nem |
> | showbackRules | Nem | Nem |
> | Nézetek | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Nem | Nem |
> | resourceProviders | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Igen | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Igen | Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Igen | Nem |
> | workspaces / virtualNetworkPeerings | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | Igen | Igen |
> | datacatalogs | Igen | Igen |
> | datacatalogs / datasources | Nem | Nem |
> | datacatalogs / datasources / scans | Nem | Nem |
> | datacatalogs / datasources / scans / datasets | Nem | Nem |
> | datacatalogs / datasources / scans / triggers | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | Igen | Nem |
> | dataFactories / diagnosticSettings | Nem | Nem |
> | dataFactories / metricDefinitions | Nem | Nem |
> | dataFactorySchema | Nem | Nem |
> | factories | Igen | Nem |
> | factories / integrationRuntimes | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | accounts / dataLakeStoreAccounts | Nem | Nem |
> | accounts / storageAccounts | Nem | Nem |
> | accounts / storageAccounts / containers | Nem | Nem |
> | accounts / transferAnalyticsUnits | Nem | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | accounts / eventGridFilters | Nem | Nem |
> | accounts / firewallRules | Nem | Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Nem | Nem |
> | services / projects | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | accounts / shares | Nem | Nem |
> | accounts / shares / datasets | Nem | Nem |
> | accounts / shares / invitations | Nem | Nem |
> | accounts / shares / providersharesubscriptions | Nem | Nem |
> | accounts / shares / synchronizationSettings | Nem | Nem |
> | accounts / sharesubscriptions | Nem | Nem |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nem | Nem |
> | accounts / sharesubscriptions / datasetmappings | Nem | Nem |
> | accounts / sharesubscriptions / triggers | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Kiszolgálók | Igen | Igen |
> | servers / advisors | Nem | Nem |
> | servers / privateEndpointConnectionProxies | Nem | Nem |
> | servers / privateEndpointConnections | Nem | Nem |
> | servers / privateLinkResources | Nem | Nem |
> | servers / queryTexts | Nem | Nem |
> | servers / recoverableServers | Nem | Nem |
> | servers / topQueryStatistics | Nem | Nem |
> | servers / virtualNetworkRules | Nem | Nem |
> | servers / waitStatistics | Nem | Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Kiszolgálók | Igen | Igen |
> | servers / advisors | Nem | Nem |
> | servers / privateEndpointConnectionProxies | Nem | Nem |
> | servers / privateEndpointConnections | Nem | Nem |
> | servers / privateLinkResources | Nem | Nem |
> | servers / queryTexts | Nem | Nem |
> | servers / recoverableServers | Nem | Nem |
> | servers / topQueryStatistics | Nem | Nem |
> | servers / virtualNetworkRules | Nem | Nem |
> | servers / waitStatistics | Nem | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | Igen | Igen |
> | Kiszolgálók | Igen | Igen |
> | servers / advisors | Nem | Nem |
> | servers / keys | Nem | Nem |
> | servers / privateEndpointConnectionProxies | Nem | Nem |
> | servers / privateEndpointConnections | Nem | Nem |
> | servers / privateLinkResources | Nem | Nem |
> | servers / queryTexts | Nem | Nem |
> | servers / recoverableServers | Nem | Nem |
> | servers / topQueryStatistics | Nem | Nem |
> | servers / virtualNetworkRules | Nem | Nem |
> | servers / waitStatistics | Nem | Nem |
> | serversv2 | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | Igen | Igen |
> | rollouts | Igen | Igen |
> | serviceTopologies | Igen | Igen |
> | serviceTopologies / services | Igen | Igen |
> | serviceTopologies / services / serviceUnits | Igen | Igen |
> | lépések | Igen | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Igen | Igen |
> | applicationgroups / applications | Nem | Nem |
> | applicationgroups / desktops | Nem | Nem |
> | applicationgroups / startmenuitems | Nem | Nem |
> | hostpools | Igen | Igen |
> | hostpools / sessionhosts | Nem | Nem |
> | hostpools / sessionhosts / usersessions | Nem | Nem |
> | hostpools / usersessions | Nem | Nem |
> | workspaces | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Igen | Igen |
> | ElasticPools / IotHubTenants | Igen | Igen |
> | IotHubs | Igen | Igen |
> | IotHubs / eventGridFilters | Nem | Nem |
> | ProvisioningServices | Igen | Igen |
> | usages | Nem | Nem |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | Igen | Igen |
> | labs | Igen | Igen |
> | labs / environments | Igen | Igen |
> | labs / serviceRunners | Igen | Igen |
> | labs / virtualMachines | Igen | Igen |
> | schedules | Igen | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Nem | Nem |
> | databaseAccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Igen | Igen |
> | domains / domainOwnershipIdentifiers | Nem | Nem |
> | generateSsoRequest | Nem | Nem |
> | topLevelDomains | Nem | Nem |
> | validateDomainRegistrationInformation | Nem | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Nem | Nem |
> | lcsprojects / clouddeployments | Nem | Nem |
> | lcsprojects / connectors | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Igen | Igen |
> | domains / topics | Nem | Nem |
> | eventSubscriptions | Nem | Nem |
> | extensionTopics | Nem | Nem |
> | topics | Igen | Igen |
> | topicTypes | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Igen | Igen |
> | namespaces | Igen | Igen |
> | namespaces / authorizationrules | Nem | Nem |
> | namespaces / disasterrecoveryconfigs | Nem | Nem |
> | namespaces / eventhubs | Nem | Nem |
> | namespaces / eventhubs / authorizationrules | Nem | Nem |
> | namespaces / eventhubs / consumergroups | Nem | Nem |
> | namespaces / networkrulesets | Nem | Nem |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | funkciókkal | Nem | Nem |
> | providers | Nem | Nem |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Nem | Nem |
> | galleryitems | Nem | Nem |
> | generateartifactaccessuri | Nem | Nem |
> | myareas | Nem | Nem |
> | myareas / areas | Nem | Nem |
> | myareas / areas / areas | Nem | Nem |
> | myareas / areas / areas / galleryitems | Nem | Nem |
> | myareas / areas / galleryitems | Nem | Nem |
> | myareas / galleryitems | Nem | Nem |
> | register | Nem | Nem |
> | resources | Nem | Nem |
> | retrieveresourcesbyid | Nem | Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Nem | Nem |
> | guestConfigurationAssignments | Nem | Nem |
> | software | Nem | Nem |
> | softwareUpdateProfile | Nem | Nem |
> | softwareUpdates | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Igen | Igen |
> | sapMonitors | Igen | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Igen | Igen |
> | clusters / applications | Nem | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | Igen | Igen |
> | machines / extensions | Igen | Igen |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | Igen | Igen |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Igen | Igen |
> | networkScopes | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Igen | Igen |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Nem | Nem |
> | IoTApps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Gráf | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Nem | Nem |
> | hsmPools | Igen | Igen |
> | vaults | Igen | Igen |
> | vaults / accessPolicies | Nem | Nem |
> | vaults / eventGridFilters | Nem | Nem |
> | vaults / secrets | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Igen | Igen |
> | clusters / attacheddatabaseconfigurations | Nem | Nem |
> | clusters / databases | Nem | Nem |
> | clusters / databases / dataconnections | Nem | Nem |
> | clusters / databases / eventhubconnections | Nem | Nem |
> | clusters / sharedidentities | Nem | Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | Igen | Igen |
> | felhasználó | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Igen | Igen |
> | integrationAccounts | Igen | Igen |
> | integrationServiceEnvironments | Igen | Igen |
> | integrationServiceEnvironments / managedApis | Igen | Igen |
> | isolatedEnvironments | Igen | Igen |
> | workflows | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Igen | Igen |
> | webServices | Igen | Igen |
> | Munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Igen | Igen |
> | workspaces / computes | Nem | Nem |
> | workspaces / eventGridFilters | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Nem | Nem |
> | userAssignedIdentities | Igen | Igen |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Nem | Nem |
> | registrationAssignments | Nem | Nem |
> | registrationDefinitions | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Nem | Nem |
> | managementGroups | Nem | Nem |
> | resources | Nem | Nem |
> | startTenantBackfill | Nem | Nem |
> | tenantBackfillStatus | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Igen | Igen |
> | accounts / eventGridFilters | Nem | Nem |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Nem | Nem |
> | offerTypes | Nem | Nem |
> | offerTypes / publishers | Nem | Nem |
> | offerTypes / publishers / offers | Nem | Nem |
> | offerTypes / publishers / offers / plans | Nem | Nem |
> | offerTypes / publishers / offers / plans / agreements | Nem | Nem |
> | offerTypes / publishers / offers / plans / configs | Nem | Nem |
> | offerTypes / publishers / offers / plans / configs / importImage | Nem | Nem |
> | privategalleryitems | Nem | Nem |
> | products | Nem | Nem |
> | publishers | Nem | Nem |
> | publishers / offers | Nem | Nem |
> | publishers / offers / amendments | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Igen | Igen |
> | updateCommunicationPreference | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Nem | Nem |
> | offertypes | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | Igen | Igen |
> | mediaservices / accountFilters | Nem | Nem |
> | mediaservices / assets | Nem | Nem |
> | mediaservices / assets / assetFilters | Nem | Nem |
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
> | mediaservices / transforms | Nem | Nem |
> | mediaservices / transforms / jobs | Nem | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | Igen | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Igen | Igen |
> | migrateprojects | Igen | Igen |
> | projects | Igen | Igen |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Igen | Igen |
> | objectUnderstandingAccounts | Igen | Igen |
> | remoteRenderingAccounts | Igen | Igen |
> | spatialAnchorsAccounts | Igen | Igen |
> | surfaceReconstructionAccounts | Igen | Igen |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Igen | Nem |
> | netAppAccounts / capacityPools | Igen | Nem |
> | netAppAccounts / capacityPools / volumes | Igen | Nem |
> | netAppAccounts / capacityPools / volumes / mountTargets | Igen | Nem |
> | netAppAccounts / capacityPools / volumes / snapshots | Igen | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Igen | Igen |
> | applicationGatewayWebApplicationFirewallPolicies | Igen | Igen |
> | applicationSecurityGroups | Igen | Igen |
> | azureFirewallFqdnTags | Nem | Nem |
> | azureFirewalls | Igen | Nem |
> | bastionHosts | Igen | Igen |
> | bgpServiceCommunities | Nem | Nem |
> | connections | Igen | Igen |
> | ddosCustomPolicies | Igen | Igen |
> | ddosProtectionPlans | Igen | Igen |
> | dnsOperationStatuses | Nem | Nem |
> | dnszones | Igen | Igen |
> | dnszones / A | Nem | Nem |
> | dnszones / AAAA | Nem | Nem |
> | dnszones / all | Nem | Nem |
> | dnszones / CAA | Nem | Nem |
> | dnszones / CNAME | Nem | Nem |
> | dnszones / MX | Nem | Nem |
> | dnszones / NS | Nem | Nem |
> | dnszones / PTR | Nem | Nem |
> | dnszones / recordsets | Nem | Nem |
> | dnszones / SOA | Nem | Nem |
> | dnszones / SRV | Nem | Nem |
> | dnszones / TXT | Nem | Nem |
> | expressRouteCircuits | Igen | Igen |
> | expressRouteCrossConnections | Igen | Igen |
> | expressRouteGateways | Igen | Igen |
> | expressRoutePorts | Igen | Igen |
> | expressRouteServiceProviders | Nem | Nem |
> | firewallPolicies | Igen | Igen |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | Igen |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Nem |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | Igen |
> | getDnsResourceReference | Nem | Nem |
> | internalNotify | Nem | Nem |
> | loadBalancers | Igen | Nem |
> | localNetworkGateways | Igen | Igen |
> | natGateways | Igen | Igen |
> | networkIntentPolicies | Igen | Igen |
> | networkInterfaces | Igen | Igen |
> | networkProfiles | Igen | Igen |
> | networkSecurityGroups | Igen | Igen |
> | networkWatchers | Igen | Nem |
> | networkWatchers / connectionMonitors | Igen | Nem |
> | networkWatchers / lenses | Igen | Nem |
> | networkWatchers / pingMeshes | Igen | Nem |
> | p2sVpnGateways | Igen | Igen |
> | privateDnsOperationStatuses | Nem | Nem |
> | privateDnsZones | Igen | Igen |
> | privateDnsZones / A | Nem | Nem |
> | privateDnsZones / AAAA | Nem | Nem |
> | privateDnsZones / all | Nem | Nem |
> | privateDnsZones / CNAME | Nem | Nem |
> | privateDnsZones / MX | Nem | Nem |
> | privateDnsZones / PTR | Nem | Nem |
> | privateDnsZones / SOA | Nem | Nem |
> | privateDnsZones / SRV | Nem | Nem |
> | privateDnsZones / TXT | Nem | Nem |
> | privateDnsZones / virtualNetworkLinks | Igen | Igen |
> | privateEndpoints | Igen | Igen |
> | privateLinkServices | Igen | Igen |
> | publicIPAddresses | Igen | Igen |
> | publicIPPrefixes | Igen | Igen |
> | routeFilters | Igen | Igen |
> | routeTables | Igen | Igen |
> | serviceEndpointPolicies | Igen | Igen |
> | trafficManagerGeographicHierarchies | Nem | Nem |
> | trafficmanagerprofiles | Igen | Igen |
> | trafficmanagerprofiles/heatMaps | Nem | Nem |
> | trafficManagerUserMetricsKeys | Nem | Nem |
> | virtualHubs | Igen | Igen |
> | virtualNetworkGateways | Igen | Igen |
> | virtualNetworks | Igen | Igen |
> | virtualNetworkTaps | Igen | Igen |
> | virtualWans | Igen | Igen |
> | vpnGateways | Igen | Nem |
> | vpnSites | Igen | Igen |
> | webApplicationFirewallPolicies | Igen | Igen |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Igen | Nem |
> | namespaces / notificationHubs | Igen | Nem |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Igen | Igen |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Igen | Igen |
> | ImportSites | Igen | Igen |
> | ServerSites | Igen | Igen |
> | VMwareSites | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Igen | Igen |
> | eszközök | Nem | Nem |
> | linkTargets | Nem | Nem |
> | storageInsightConfigs | Nem | Nem |
> | workspaces | Igen | Igen |
> | workspaces / dataSources | Nem | Nem |
> | workspaces / linkedServices | Nem | Nem |
> | workspaces / query | Nem | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Nem | Nem |
> | managementconfigurations | Igen | Igen |
> | megoldások | Igen | Igen |
> | views | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Nem | Nem |
> | peerAsns | Nem | Nem |
> | peerings | Igen | Igen |
> | peeringServiceProviders | Nem | Nem |
> | peeringServices | Igen | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Nem | Nem |
> | policyMetadata | Nem | Nem |
> | policyStates | Nem | Nem |
> | policyTrackedResources | Nem | Nem |
> | remediations | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Nem | Nem |
> | dashboards | Igen | Igen |
> | userSettings | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | Igen | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Nem | Nem |
> | vaults | Igen | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Igen | Igen |
> | namespaces / authorizationrules | Nem | Nem |
> | namespaces / hybridconnections | Nem | Nem |
> | namespaces / hybridconnections / authorizationrules | Nem | Nem |
> | namespaces / wcfrelays | Nem | Nem |
> | namespaces / wcfrelays / authorizationrules | Nem | Nem |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | fiókok | Nem | Nem |
> | collections | Igen | Igen |
> | collections / applications | Nem | Nem |
> | collections / securityprincipals | Nem | Nem |
> | templateImages | Nem | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lekérdezés | Igen | Igen |
> | resourceChangeDetails | Nem | Nem |
> | resourceChanges | Nem | Nem |
> | resources | Nem | Nem |
> | resourcesHistory | Nem | Nem |
> | subscriptionsStatus | Nem | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Nem | Nem |
> | childAvailabilityStatuses | Nem | Nem |
> | childResources | Nem | Nem |
> | események | Nem | Nem |
> | impactedResources | Nem | Nem |
> | metaadatok | Nem | Nem |
> | notifications | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | Igen | Nem |
> | deployments / operations | Nem | Nem |
> | deploymentScripts | Igen | Igen |
> | deploymentScripts / logs | Nem | Nem |
> | links | Nem | Nem |
> | notifyResourceJobs | Nem | Nem |
> | providers | Nem | Nem |
> | resourceGroups | Igen | Nem |
> | subscriptions | Nem | Nem |
> | tenants | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | alkalmazás | Igen | Igen |
> | saasresources | Nem | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Nem | Nem |
> | searchServices | Igen | Igen |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Nem | Nem |
> | advancedThreatProtectionSettings | Nem | Nem |
> | riasztások | Nem | Nem |
> | allowedConnections | Nem | Nem |
> | applicationWhitelistings | Nem | Nem |
> | assessmentMetadata | Nem | Nem |
> | assessments | Nem | Nem |
> | autoDismissAlertsRules | Nem | Nem |
> | automations | Igen | Igen |
> | AutoProvisioningSettings | Nem | Nem |
> | Compliances | Nem | Nem |
> | dataCollectionAgents | Nem | Nem |
> | deviceSecurityGroups | Nem | Nem |
> | discoveredSecuritySolutions | Nem | Nem |
> | externalSecuritySolutions | Nem | Nem |
> | InformationProtectionPolicies | Nem | Nem |
> | iotSecuritySolutions | Igen | Igen |
> | iotSecuritySolutions / analyticsModels | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nem | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nem | Nem |
> | jitNetworkAccessPolicies | Nem | Nem |
> | networkData | Nem | Nem |
> | policies | Nem | Nem |
> | pricings | Nem | Nem |
> | regulatoryComplianceStandards | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nem | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nem | Nem |
> | securityContacts | Nem | Nem |
> | securitySolutions | Nem | Nem |
> | securitySolutionsReferenceData | Nem | Nem |
> | securityStatuses | Nem | Nem |
> | securityStatusesSummaries | Nem | Nem |
> | serverVulnerabilityAssessments | Nem | Nem |
> | beállítások | Nem | Nem |
> | subAssessments | Nem | Nem |
> | feladatok | Nem | Nem |
> | topologies | Nem | Nem |
> | workspaceSettings | Nem | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Nem | Nem |
> | alertRules | Nem | Nem |
> | alertRuleTemplates | Nem | Nem |
> | bookmarks | Nem | Nem |
> | cases | Nem | Nem |
> | dataConnectors | Nem | Nem |
> | entities | Nem | Nem |
> | entityQueries | Nem | Nem |
> | officeConsents | Nem | Nem |
> | beállítások | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Igen | Nem |
> | namespaces / authorizationrules | Nem | Nem |
> | namespaces / disasterrecoveryconfigs | Nem | Nem |
> | namespaces / eventgridfilters | Nem | Nem |
> | namespaces / networkrulesets | Nem | Nem |
> | namespaces / queues | Nem | Nem |
> | namespaces / queues / authorizationrules | Nem | Nem |
> | namespaces / topics | Nem | Nem |
> | namespaces / topics / authorizationrules | Nem | Nem |
> | namespaces / topics / subscriptions | Nem | Nem |
> | namespaces / topics / subscriptions / rules | Nem | Nem |
> | premiumMessagingRegions | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | alkalmazás | Igen | Igen |
> | clusters | Igen | Igen |
> | clusters / applications | Nem | Nem |
> | containerGroups | Igen | Igen |
> | containerGroupSets | Igen | Igen |
> | edgeclusters | Igen | Igen |
> | edgeclusters / applications | Nem | Nem |
> | networks | Igen | Igen |
> | secretstores | Igen | Igen |
> | secretstores / certificates | Nem | Nem |
> | secretstores / secrets | Nem | Nem |
> | volumes | Igen | Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | alkalmazás | Igen | Igen |
> | containerGroups | Igen | Igen |
> | gateways | Igen | Igen |
> | networks | Igen | Igen |
> | secrets | Igen | Igen |
> | volumes | Igen | Igen |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Nem | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem | Nem |
> | rollouts | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | Igen | Igen |
> | SignalR / eventGridFilters | Nem | Nem |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Igen | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Igen | Igen |
> | alkalmazás | Igen | Igen |
> | jitRequests | Igen | Igen |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | Igen | Igen |
> | managedInstances / databases | Nem | Nem |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nem | Nem |
> | managedInstances / databases / vulnerabilityAssessments | Nem | Nem |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nem | Nem |
> | managedInstances / encryptionProtector | Nem | Nem |
> | managedInstances / keys | Nem | Nem |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nem | Nem |
> | managedInstances / vulnerabilityAssessments | Nem | Nem |
> | Kiszolgálók | Igen | Igen |
> | servers / administrators | Nem | Nem |
> | servers / communicationLinks | Nem | Nem |
> | servers / databases | Yes (see [note below](#sqlnote)) | Igen |
> | servers / encryptionProtector | Nem | Nem |
> | servers / firewallRules | Nem | Nem |
> | servers / keys | Nem | Nem |
> | servers / restorableDroppedDatabases | Nem | Nem |
> | servers / serviceobjectives | Nem | Nem |
> | servers / tdeCertificates | Nem | Nem |
> | virtualClusters | Nem | Nem |

<a id="sqlnote" />

> [!NOTE]
> The Master database doesn't support tags, but other databases, including Azure SQL Data Warehouse databases, support tags. Azure SQL Data Warehouse databases must be in Active (not Paused) state.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Igen | Igen |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nem | Nem |
> | SqlVirtualMachines | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Igen | Igen |
> | storageAccounts / blobServices | Nem | Nem |
> | storageAccounts / fileServices | Nem | Nem |
> | storageAccounts / queueServices | Nem | Nem |
> | storageAccounts / services | Nem | Nem |
> | storageAccounts / services / metricDefinitions | Nem | Nem |
> | storageAccounts / tableServices | Nem | Nem |
> | usages | Nem | Nem |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | Igen | Igen |
> | caches / storageTargets | Nem | Nem |
> | usageModels | Nem | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Igen |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / workflows | Nem | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Igen |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / workflows | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Igen | Igen |
> | storageSyncServices / registeredServers | Nem | Nem |
> | storageSyncServices / syncGroups | Nem | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem | Nem |
> | storageSyncServices / workflows | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | Igen | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Yes (see note below) | Igen |

> [!NOTE]
> You can't add a tag when streamingjobs is running. Stop the resource to add a tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Nem | Nem |
> | CreateSubscription | Nem | Nem |
> | enable | Nem | Nem |
> | rename | Nem | Nem |
> | SubscriptionDefinitions | Nem | Nem |
> | SubscriptionOperations | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Igen | Nem |
> | environments / accessPolicies | Nem | Nem |
> | environments / eventsources | Igen | Nem |
> | environments / referenceDataSets | Igen | Nem |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Igen | Igen |
> | dedicatedCloudServices | Igen | Igen |
> | virtualMachines | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Nem | Nem |
> | apiManagementAccounts / apiAcls | Nem | Nem |
> | apiManagementAccounts / apis | Nem | Nem |
> | apiManagementAccounts / apis / apiAcls | Nem | Nem |
> | apiManagementAccounts / apis / connectionAcls | Nem | Nem |
> | apiManagementAccounts / apis / connections | Nem | Nem |
> | apiManagementAccounts / apis / connections / connectionAcls | Nem | Nem |
> | apiManagementAccounts / apis / localizedDefinitions | Nem | Nem |
> | apiManagementAccounts / connectionAcls | Nem | Nem |
> | apiManagementAccounts / connections | Nem | Nem |
> | billingMeters | Nem | Nem |
> | certificates | Igen | Igen |
> | connectionGateways | Igen | Igen |
> | connections | Igen | Igen |
> | customApis | Igen | Igen |
> | deletedSites | Nem | Nem |
> | functions | Nem | Nem |
> | hostingEnvironments | Igen | Igen |
> | hostingEnvironments / multiRolePools | Nem | Nem |
> | hostingEnvironments / workerPools | Nem | Nem |
> | publishingUsers | Nem | Nem |
> | recommendations | Nem | Nem |
> | resourceHealthMetadata | Nem | Nem |
> | runtimes | Nem | Nem |
> | serverFarms | Igen | Igen |
> | serverFarms / eventGridFilters | Nem | Nem |
> | sites | Igen | Igen |
> | sites / config  | Nem | Nem |
> | sites / eventGridFilters | Nem | Nem |
> | sites / hostNameBindings | Nem | Nem |
> | sites / networkConfig | Nem | Nem |
> | sites / premieraddons | Igen | Igen |
> | sites / slots | Igen | Igen |
> | sites / slots / eventGridFilters | Nem | Nem |
> | sites / slots / hostNameBindings | Nem | Nem |
> | sites / slots / networkConfig | Nem | Nem |
> | sourceControls | Nem | Nem |
> | validate | Nem | Nem |
> | verifyHostingEnvironmentVnet | Nem | Nem |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Nem | Nem |
> | diagnosticSettingsCategories | Nem | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Igen | Igen |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Nem | Nem |
> | componentsSummary | Nem | Nem |
> | monitorInstances | Nem | Nem |
> | monitorInstancesSummary | Nem | Nem |
> | monitors | Nem | Nem |
> | notificationSettings | Nem | Nem |

## <a name="next-steps"></a>Következő lépések

To learn how to apply tags to resources, see [Use tags to organize your Azure resources](resource-group-using-tags.md).
