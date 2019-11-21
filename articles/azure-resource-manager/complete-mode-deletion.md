---
title: Törlés Teljes mód esetén
description: Shows how resource types handle complete mode deletion in Azure Resource Manager templates.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232689"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Deletion of Azure resources for complete mode deployments

This article describes how resource types handle deletion when not in a template that is deployed in complete mode.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. For a full description of the behavior, see [Azure Resource Manager deployment modes](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DomainServices | Igen |
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
> | aadsupportcases | Nem |
> | addsservices | Nem |
> | agents | Nem |
> | anonymousapiusers | Nem |
> | konfiguráció | Nem |
> | naplók | Nem |
> | reports | Nem |
> | servicehealthmetrics | Nem |
> | services | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurations | Nem |
> | generateRecommendations | Nem |
> | metaadatok | Nem |
> | recommendations | Nem |
> | suppressions | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | actionRules | Igen |
> | riasztások | Nem |
> | alertsList | Nem |
> | alertsMetaData | Nem |
> | alertsSummary | Nem |
> | alertsSummaryList | Nem |
> | feedback | Nem |
> | smartDetectorAlertRules | Igen |
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

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores | Igen |
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
> | classicAdministrators | Nem |
> | dataAliases | Nem |
> | denyAssignments | Nem |
> | elevateAccess | Nem |
> | findOrphanRoleAssignments | Nem |
> | locks | Nem |
> | permissions | Nem |
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
> | automationAccounts / configurations | Igen |
> | automationAccounts / jobs | Nem |
> | automationAccounts / runbooks | Igen |
> | automationAccounts / softwareUpdateConfigurations | Nem |
> | automationAccounts / webhooks | Nem |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationStores | Igen |
> | configurationStores / eventGridFilters | Nem |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | environments | Nem |
> | environments / accounts | Nem |
> | environments / accounts / namespaces | Nem |
> | environments / accounts / namespaces / configurations | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | b2cDirectories | Igen |
> | b2ctenants | Nem |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hybridDataManagers | Igen |
> | postgresInstances | Igen |
> | sqlBigDataClusters | Igen |
> | sqlInstances | Igen |
> | sqlServerRegistrations | Igen |
> | sqlServerRegistrations / sqlServers | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | registrations | Igen |
> | registrations / customerSubscriptions | Nem |
> | registrations / products | Nem |
> | verificationKeys | Nem |

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
> | billingAccounts / agreements | Nem |
> | billingAccounts / billingPermissions | Nem |
> | billingAccounts / billingProfiles | Nem |
> | billingAccounts / billingProfiles / billingPermissions | Nem |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nem |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nem |
> | billingAccounts / billingProfiles / billingSubscriptions | Nem |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nem |
> | billingAccounts / billingProfiles / customers | Nem |
> | billingAccounts / billingProfiles / invoices | Nem |
> | billingAccounts / billingProfiles / invoices / pricesheet | Nem |
> | billingAccounts / billingProfiles / invoiceSections | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nem |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Nem |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Nem |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Nem |
> | billingAccounts / BillingProfiles / patchOperations | Nem |
> | billingAccounts / billingProfiles / paymentMethods | Nem |
> | billingAccounts / billingProfiles / policies | Nem |
> | billingAccounts / billingProfiles / pricesheet | Nem |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nem |
> | billingAccounts / billingProfiles / products | Nem |
> | billingAccounts / billingProfiles / transactions | Nem |
> | billingAccounts / billingRoleAssignments | Nem |
> | billingAccounts / billingRoleDefinitions | Nem |
> | billingAccounts / billingSubscriptions | Nem |
> | billingAccounts / billingSubscriptions / invoices | Nem |
> | billingAccounts / createBillingRoleAssignment | Nem |
> | billingAccounts / createInvoiceSectionOperations | Nem |
> | billingAccounts / customers | Nem |
> | billingAccounts / customers / billingPermissions | Nem |
> | billingAccounts / customers / billingSubscriptions | Nem |
> | billingAccounts / customers / initiateTransfer | Nem |
> | billingAccounts / customers / policies | Nem |
> | billingAccounts / customers / products | Nem |
> | billingAccounts / customers / transactions | Nem |
> | billingAccounts / customers / transfers | Nem |
> | billingAccounts / departments | Nem |
> | billingAccounts / enrollmentAccounts | Nem |
> | billingAccounts / invoices | Nem |
> | billingAccounts / invoiceSections | Nem |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions | Nem |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Nem |
> | billingAccounts / invoiceSections / elevate | Nem |
> | billingAccounts / invoiceSections / initiateTransfer | Nem |
> | billingAccounts / invoiceSections / patchOperations | Nem |
> | billingAccounts / invoiceSections / productMoveOperations | Nem |
> | billingAccounts / invoiceSections / products | Nem |
> | billingAccounts / invoiceSections / products / transfer | Nem |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Nem |
> | billingAccounts / invoiceSections / transactions | Nem |
> | billingAccounts / invoiceSections / transfers | Nem |
> | billingAccounts / lineOfCredit | Nem |
> | billingAccounts / patchOperations | Nem |
> | billingAccounts / paymentMethods | Nem |
> | billingAccounts / products | Nem |
> | billingAccounts / transactions | Nem |
> | billingPeriods | Nem |
> | billingPermissions | Nem |
> | billingProperty | Nem |
> | billingRoleAssignments | Nem |
> | billingRoleDefinitions | Nem |
> | createBillingRoleAssignment | Nem |
> | departments | Nem |
> | enrollmentAccounts | Nem |
> | invoices | Nem |
> | transfers | Nem |
> | transfers / acceptTransfer | Nem |
> | transfers / declineTransfer | Nem |
> | transfers / operationStatus | Nem |
> | transfers / validateTransfer | Nem |
> | validateAddress | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mapApis | Igen |
> | updateCommunicationPreference | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blockchainMembers | Igen |
> | cordaMembers | Igen |
> | néző | Igen |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | blueprintAssignments | Nem |
> | blueprintAssignments / assignmentOperations | Nem |
> | blueprintAssignments / operations | Nem |
> | blueprints | Nem |
> | blueprints / artifacts | Nem |
> | blueprints / versions | Nem |
> | blueprints / versions / artifacts | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | botServices | Igen |
> | botServices / channels | Nem |
> | botServices / connections | Nem |
> | languages | Nem |
> | sablonok | Nem |

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
> | calculateExchange | Nem |
> | calculatePrice | Nem |
> | calculatePurchasePrice | Nem |
> | catalogs | Nem |
> | commercialReservationOrders | Nem |
> | exchange | Nem |
> | placePurchaseOrder | Nem |
> | reservationOrders | Nem |
> | reservationOrders / calculateRefund | Nem |
> | reservationOrders / merge | Nem |
> | reservationOrders / reservations | Nem |
> | reservationOrders / reservations / revisions | Nem |
> | reservationOrders / return | Nem |
> | reservationOrders / split | Nem |
> | reservationOrders / swap | Nem |
> | reservations | Nem |
> | resources | Nem |
> | validateReservationOrder | Nem |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nem |
> | CdnWebApplicationFirewallPolicies | Igen |
> | edgenodes | Nem |
> | profiles | Igen |
> | profiles / endpoints | Igen |
> | profiles / endpoints / customdomains | Nem |
> | profiles / endpoints / origins | Nem |
> | validateProbe | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | certificateOrders | Igen |
> | certificateOrders / certificates | Nem |
> | validateCertificateRegistrationInformation | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | capabilities | Nem |
> | domainNames | Igen |
> | domainNames / capabilities | Nem |
> | domainNames / internalLoadBalancers | Nem |
> | domainNames / serviceCertificates | Nem |
> | domainNames / slots | Nem |
> | domainNames / slots / roles | Nem |
> | domainNames / slots / roles / metricDefinitions | Nem |
> | domainNames / slots / roles / metrics | Nem |
> | moveSubscriptionResources | Nem |
> | operatingSystemFamilies | Nem |
> | operatingSystems | Nem |
> | kvóták | Nem |
> | resourceTypes | Nem |
> | validateSubscriptionMoveAvailability | Nem |
> | virtualMachines | Igen |
> | virtualMachines / diagnosticSettings | Nem |
> | virtualMachines / metricDefinitions | Nem |
> | virtualMachines / metrics | Nem |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | capabilities | Nem |
> | expressRouteCrossConnections | Nem |
> | expressRouteCrossConnections / peerings | Nem |
> | gatewaySupportedDevices | Nem |
> | networkSecurityGroups | Igen |
> | kvóták | Nem |
> | reservedIps | Igen |
> | virtualNetworks | Igen |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Nem |
> | virtualNetworks / virtualNetworkPeerings | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | capabilities | Nem |
> | disks | Nem |
> | images | Nem |
> | osImages | Nem |
> | osPlatformImages | Nem |
> | publicImages | Nem |
> | kvóták | Nem |
> | storageAccounts | Igen |
> | storageAccounts / blobServices | Nem |
> | storageAccounts / fileServices | Nem |
> | storageAccounts / metricDefinitions | Nem |
> | storageAccounts / metrics | Nem |
> | storageAccounts / queueServices | Nem |
> | storageAccounts / services | Nem |
> | storageAccounts / services / diagnosticSettings | Nem |
> | storageAccounts / services / metricDefinitions | Nem |
> | storageAccounts / services / metrics | Nem |
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
> | UsageAggregates | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | availabilitySets | Igen |
> | diskEncryptionSets | Igen |
> | disks | Igen |
> | galleries | Igen |
> | galleries / applications | Nem |
> | galleries / applications / versions | Nem |
> | galleries / images | Nem |
> | galleries / images / versions | Nem |
> | hostGroups | Igen |
> | hostGroups / hosts | Igen |
> | images | Igen |
> | proximityPlacementGroups | Igen |
> | restorePointCollections | Igen |
> | restorePointCollections / restorePoints | Nem |
> | sharedVMImages | Igen |
> | sharedVMImages / versions | Nem |
> | pillanatképek | Igen |
> | virtualMachines | Igen |
> | virtualMachines / extensions | Igen |
> | virtualMachines / metricDefinitions | Nem |
> | virtualMachineScaleSets | Igen |
> | virtualMachineScaleSets / extensions | Nem |
> | virtualMachineScaleSets / networkInterfaces | Nem |
> | virtualMachineScaleSets / publicIPAddresses | Nem |
> | virtualMachineScaleSets / virtualMachines | Nem |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Nem |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | AggregatedCost | Nem |
> | Egyenlegek | Nem |
> | Költségkeret | Nem |
> | Díjak | Nem |
> | CostTags | Nem |
> | credits | Nem |
> | események | Nem |
> | Előrejelzések | Nem |
> | lots | Nem |
> | Marketplaces | Nem |
> | Pricesheets | Nem |
> | products | Nem |
> | ReservationDetails | Nem |
> | ReservationRecommendations | Nem |
> | ReservationSummaries | Nem |
> | ReservationTransactions | Nem |
> | Címkék | Nem |
> | tenants | Nem |
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
> | registries | Igen |
> | registries / builds | Nem |
> | registries / builds / cancel | Nem |
> | registries / builds / getLogLink | Nem |
> | registries / buildTasks | Igen |
> | registries / buildTasks / steps | Nem |
> | registries / eventGridFilters | Nem |
> | registries / generateCredentials | Nem |
> | registries / getBuildSourceUploadUrl | Nem |
> | registries / GetCredentials | Nem |
> | registries / importImage | Nem |
> | registries / queueBuild | Nem |
> | registries / regenerateCredential | Nem |
> | registries / regenerateCredentials | Nem |
> | registries / replications | Igen |
> | registries / runs | Nem |
> | registries / runs / cancel | Nem |
> | registries / scheduleRun | Nem |
> | registries / scopeMaps | Nem |
> | registries / taskRuns | Igen |
> | registries / tasks | Igen |
> | registries / tokens | Nem |
> | registries / updatePolicies | Nem |
> | registries / webhooks | Igen |
> | registries / webhooks / getCallbackConfig | Nem |
> | registries / webhooks / ping | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | containerServices | Igen |
> | managedClusters | Igen |
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
> | Értesítések | Nem |
> | BillingAccounts | Nem |
> | Költségkeret | Nem |
> | CloudConnectors | Nem |
> | Összekötők | Igen |
> | Departments | Nem |
> | Dimenziók | Nem |
> | EnrollmentAccounts | Nem |
> | Exportálások | Nem |
> | ExternalBillingAccounts | Nem |
> | ExternalBillingAccounts / Alerts | Nem |
> | ExternalBillingAccounts / Dimensions | Nem |
> | ExternalBillingAccounts / Forecast | Nem |
> | ExternalBillingAccounts / Query | Nem |
> | ExternalSubscriptions | Nem |
> | ExternalSubscriptions / Alerts | Nem |
> | ExternalSubscriptions / Dimensions | Nem |
> | ExternalSubscriptions / Forecast | Nem |
> | ExternalSubscriptions / Query | Nem |
> | Forecast | Nem |
> | Lekérdezés | Nem |
> | register | Nem |
> | Reportconfigs | Nem |
> | Jelentések | Nem |
> | Beállítások | Nem |
> | showbackRules | Nem |
> | Nézetek | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | requests | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | associations | Nem |
> | resourceProviders | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | jobs | Igen |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Igen |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | workspaces | Igen |
> | workspaces / virtualNetworkPeerings | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | catalogs | Igen |
> | datacatalogs | Igen |
> | datacatalogs / datasources | Nem |
> | datacatalogs / datasources / scans | Nem |
> | datacatalogs / datasources / scans / datasets | Nem |
> | datacatalogs / datasources / scans / triggers | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataFactories | Igen |
> | dataFactories / diagnosticSettings | Nem |
> | dataFactories / metricDefinitions | Nem |
> | dataFactorySchema | Nem |
> | factories | Igen |
> | factories / integrationRuntimes | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | accounts / dataLakeStoreAccounts | Nem |
> | accounts / storageAccounts | Nem |
> | accounts / storageAccounts / containers | Nem |
> | accounts / transferAnalyticsUnits | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | accounts / eventGridFilters | Nem |
> | accounts / firewallRules | Nem |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |
> | services / projects | Igen |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | accounts / shares | Nem |
> | accounts / shares / datasets | Nem |
> | accounts / shares / invitations | Nem |
> | accounts / shares / providersharesubscriptions | Nem |
> | accounts / shares / synchronizationSettings | Nem |
> | accounts / sharesubscriptions | Nem |
> | accounts / sharesubscriptions / consumerSourceDataSets | Nem |
> | accounts / sharesubscriptions / datasetmappings | Nem |
> | accounts / sharesubscriptions / triggers | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen |
> | servers / advisors | Nem |
> | servers / privateEndpointConnectionProxies | Nem |
> | servers / privateEndpointConnections | Nem |
> | servers / privateLinkResources | Nem |
> | servers / queryTexts | Nem |
> | servers / recoverableServers | Nem |
> | servers / topQueryStatistics | Nem |
> | servers / virtualNetworkRules | Nem |
> | servers / waitStatistics | Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Kiszolgálók | Igen |
> | servers / advisors | Nem |
> | servers / privateEndpointConnectionProxies | Nem |
> | servers / privateEndpointConnections | Nem |
> | servers / privateLinkResources | Nem |
> | servers / queryTexts | Nem |
> | servers / recoverableServers | Nem |
> | servers / topQueryStatistics | Nem |
> | servers / virtualNetworkRules | Nem |
> | servers / waitStatistics | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | serverGroups | Igen |
> | Kiszolgálók | Igen |
> | servers / advisors | Nem |
> | servers / keys | Nem |
> | servers / privateEndpointConnectionProxies | Nem |
> | servers / privateEndpointConnections | Nem |
> | servers / privateLinkResources | Nem |
> | servers / queryTexts | Nem |
> | servers / recoverableServers | Nem |
> | servers / topQueryStatistics | Nem |
> | servers / virtualNetworkRules | Nem |
> | servers / waitStatistics | Nem |
> | serversv2 | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | artifactSources | Igen |
> | rollouts | Igen |
> | serviceTopologies | Igen |
> | serviceTopologies / services | Igen |
> | serviceTopologies / services / serviceUnits | Igen |
> | lépések | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationgroups | Igen |
> | applicationgroups / applications | Nem |
> | applicationgroups / desktops | Nem |
> | applicationgroups / startmenuitems | Nem |
> | hostpools | Igen |
> | hostpools / sessionhosts | Nem |
> | hostpools / sessionhosts / usersessions | Nem |
> | hostpools / usersessions | Nem |
> | workspaces | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | ElasticPools | Igen |
> | ElasticPools / IotHubTenants | Igen |
> | IotHubs | Igen |
> | IotHubs / eventGridFilters | Nem |
> | ProvisioningServices | Igen |
> | usages | Nem |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | pipelines | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | controllers | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labcenters | Igen |
> | labs | Igen |
> | labs / environments | Igen |
> | labs / serviceRunners | Igen |
> | labs / virtualMachines | Igen |
> | schedules | Igen |

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
> | domains | Igen |
> | domains / domainOwnershipIdentifiers | Nem |
> | generateSsoRequest | Nem |
> | topLevelDomains | Nem |
> | validateDomainRegistrationInformation | Nem |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lcsprojects | Nem |
> | lcsprojects / clouddeployments | Nem |
> | lcsprojects / connectors | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | domains | Igen |
> | domains / topics | Nem |
> | eventSubscriptions | Nem |
> | extensionTopics | Nem |
> | topics | Igen |
> | topicTypes | Nem |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | clusters | Igen |
> | namespaces | Igen |
> | namespaces / authorizationrules | Nem |
> | namespaces / disasterrecoveryconfigs | Nem |
> | namespaces / eventhubs | Nem |
> | namespaces / eventhubs / authorizationrules | Nem |
> | namespaces / eventhubs / consumergroups | Nem |
> | namespaces / networkrulesets | Nem |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | funkciókkal | Nem |
> | providers | Nem |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | enroll | Nem |
> | galleryitems | Nem |
> | generateartifactaccessuri | Nem |
> | myareas | Nem |
> | myareas / areas | Nem |
> | myareas / areas / areas | Nem |
> | myareas / areas / areas / galleryitems | Nem |
> | myareas / areas / galleryitems | Nem |
> | myareas / galleryitems | Nem |
> | register | Nem |
> | resources | Nem |
> | retrieveresourcesbyid | Nem |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | configurationProfileAssignments | Nem |
> | guestConfigurationAssignments | Nem |
> | software | Nem |
> | softwareUpdateProfile | Nem |
> | softwareUpdates | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hanaInstances | Igen |
> | sapMonitors | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedicatedHSMs | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | clusters | Igen |
> | clusters / applications | Nem |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | services | Igen |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | machines | Igen |
> | machines / extensions | Igen |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dataManagers | Igen |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | components | Igen |
> | networkScopes | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | jobs | Igen |

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
> | appTemplates | Nem |
> | IoTApps | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Gráf | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deletedVaults | Nem |
> | hsmPools | Igen |
> | vaults | Igen |
> | vaults / accessPolicies | Nem |
> | vaults / eventGridFilters | Nem |
> | vaults / secrets | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | clusters | Igen |
> | clusters / attacheddatabaseconfigurations | Nem |
> | clusters / databases | Nem |
> | clusters / databases / dataconnections | Nem |
> | clusters / databases / eventhubconnections | Nem |
> | clusters / sharedidentities | Nem |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | labaccounts | Igen |
> | felhasználó | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | hostingEnvironments | Igen |
> | integrationAccounts | Igen |
> | integrationServiceEnvironments | Igen |
> | integrationServiceEnvironments / managedApis | Igen |
> | isolatedEnvironments | Igen |
> | workflows | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

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
> | workspaces | Igen |
> | workspaces / computes | Nem |
> | workspaces / eventGridFilters | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | Identities | Nem |
> | userAssignedIdentities | Igen |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nem |
> | registrationAssignments | Nem |
> | registrationDefinitions | Nem |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | getEntities | Nem |
> | managementGroups | Nem |
> | resources | Nem |
> | startTenantBackfill | Nem |
> | tenantBackfillStatus | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Igen |
> | accounts / eventGridFilters | Nem |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | offers | Nem |
> | offerTypes | Nem |
> | offerTypes / publishers | Nem |
> | offerTypes / publishers / offers | Nem |
> | offerTypes / publishers / offers / plans | Nem |
> | offerTypes / publishers / offers / plans / agreements | Nem |
> | offerTypes / publishers / offers / plans / configs | Nem |
> | offerTypes / publishers / offers / plans / configs / importImage | Nem |
> | privategalleryitems | Nem |
> | products | Nem |
> | publishers | Nem |
> | publishers / offers | Nem |
> | publishers / offers / amendments | Nem |

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
> | agreements | Nem |
> | offertypes | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | mediaservices | Igen |
> | mediaservices / accountFilters | Nem |
> | mediaservices / assets | Nem |
> | mediaservices / assets / assetFilters | Nem |
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
> | mediaservices / transforms | Nem |
> | mediaservices / transforms / jobs | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | appClusters | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | assessmentProjects | Igen |
> | migrateprojects | Igen |
> | projects | Igen |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Igen |
> | objectUnderstandingAccounts | Igen |
> | remoteRenderingAccounts | Igen |
> | spatialAnchorsAccounts | Igen |
> | surfaceReconstructionAccounts | Igen |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | netAppAccounts | Igen |
> | netAppAccounts / capacityPools | Igen |
> | netAppAccounts / capacityPools / volumes | Igen |
> | netAppAccounts / capacityPools / volumes / mountTargets | Igen |
> | netAppAccounts / capacityPools / volumes / snapshots | Igen |
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
> | connections | Igen |
> | ddosCustomPolicies | Igen |
> | ddosProtectionPlans | Igen |
> | dnsOperationStatuses | Nem |
> | dnszones | Igen |
> | dnszones / A | Nem |
> | dnszones / AAAA | Nem |
> | dnszones / all | Nem |
> | dnszones / CAA | Nem |
> | dnszones / CNAME | Nem |
> | dnszones / MX | Nem |
> | dnszones / NS | Nem |
> | dnszones / PTR | Nem |
> | dnszones / recordsets | Nem |
> | dnszones / SOA | Nem |
> | dnszones / SRV | Nem |
> | dnszones / TXT | Nem |
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
> | networkWatchers / connectionMonitors | Igen |
> | networkWatchers / lenses | Igen |
> | networkWatchers / pingMeshes | Igen |
> | p2sVpnGateways | Igen |
> | privateDnsOperationStatuses | Nem |
> | privateDnsZones | Igen |
> | privateDnsZones / A | Nem |
> | privateDnsZones / AAAA | Nem |
> | privateDnsZones / all | Nem |
> | privateDnsZones / CNAME | Nem |
> | privateDnsZones / MX | Nem |
> | privateDnsZones / PTR | Nem |
> | privateDnsZones / SOA | Nem |
> | privateDnsZones / SRV | Nem |
> | privateDnsZones / TXT | Nem |
> | privateDnsZones / virtualNetworkLinks | Igen |
> | privateEndpoints | Igen |
> | privateLinkServices | Igen |
> | publicIPAddresses | Igen |
> | publicIPPrefixes | Igen |
> | routeFilters | Igen |
> | routeTables | Igen |
> | serviceEndpointPolicies | Igen |
> | trafficManagerGeographicHierarchies | Nem |
> | trafficmanagerprofiles | Igen |
> | trafficmanagerprofiles / heatMaps | Nem |
> | trafficManagerUserMetricsKeys | Nem |
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
> | namespaces | Igen |
> | namespaces / notificationHubs | Igen |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | osNamespaces | Igen |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | HyperVSites | Igen |
> | ImportSites | Igen |
> | ServerSites | Igen |
> | VMwareSites | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | clusters | Igen |
> | eszközök | Nem |
> | linkTargets | Nem |
> | storageInsightConfigs | Nem |
> | workspaces | Igen |
> | workspaces / dataSources | Nem |
> | workspaces / linkedServices | Nem |
> | workspaces / query | Nem |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managementassociations | Nem |
> | managementconfigurations | Igen |
> | megoldások | Igen |
> | views | Igen |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | legacyPeerings | Nem |
> | peerAsns | Nem |
> | peerings | Igen |
> | peeringServiceProviders | Nem |
> | peeringServices | Igen |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | policyEvents | Nem |
> | policyMetadata | Nem |
> | policyStates | Nem |
> | policyTrackedResources | Nem |
> | remediations | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | consoles | Nem |
> | dashboards | Igen |
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
> | capacities | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | backupProtectedItems | Nem |
> | vaults | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | namespaces | Igen |
> | namespaces / authorizationrules | Nem |
> | namespaces / hybridconnections | Nem |
> | namespaces / hybridconnections / authorizationrules | Nem |
> | namespaces / wcfrelays | Nem |
> | namespaces / wcfrelays / authorizationrules | Nem |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | fiókok | Nem |
> | collections | Igen |
> | collections / applications | Nem |
> | collections / securityprincipals | Nem |
> | templateImages | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | lekérdezés | Igen |
> | resourceChangeDetails | Nem |
> | resourceChanges | Nem |
> | resources | Nem |
> | resourcesHistory | Nem |
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
> | metaadatok | Nem |
> | notifications | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | deployments | Nem |
> | deployments / operations | Nem |
> | deploymentScripts | Igen |
> | deploymentScripts / logs | Nem |
> | links | Nem |
> | notifyResourceJobs | Nem |
> | providers | Nem |
> | resourceGroups | Nem |
> | resources | Nem |
> | subscriptions | Nem |
> | subscriptions / providers | Nem |
> | subscriptions / resources | Nem |
> | subscriptions / tagnames | Nem |
> | subscriptions / tagNames / tagValues | Nem |
> | tenants | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | saasresources | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | jobcollections | Igen |

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
> | adaptiveNetworkHardenings | Nem |
> | advancedThreatProtectionSettings | Nem |
> | riasztások | Nem |
> | allowedConnections | Nem |
> | applicationWhitelistings | Nem |
> | assessmentMetadata | Nem |
> | assessments | Nem |
> | autoDismissAlertsRules | Nem |
> | automations | Igen |
> | AutoProvisioningSettings | Nem |
> | Compliances | Nem |
> | dataCollectionAgents | Nem |
> | deviceSecurityGroups | Nem |
> | discoveredSecuritySolutions | Nem |
> | externalSecuritySolutions | Nem |
> | InformationProtectionPolicies | Nem |
> | iotSecuritySolutions | Igen |
> | iotSecuritySolutions / analyticsModels | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nem |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nem |
> | jitNetworkAccessPolicies | Nem |
> | networkData | Nem |
> | policies | Nem |
> | pricings | Nem |
> | regulatoryComplianceStandards | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nem |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nem |
> | securityContacts | Nem |
> | securitySolutions | Nem |
> | securitySolutionsReferenceData | Nem |
> | securityStatuses | Nem |
> | securityStatusesSummaries | Nem |
> | serverVulnerabilityAssessments | Nem |
> | beállítások | Nem |
> | subAssessments | Nem |
> | feladatok | Nem |
> | topologies | Nem |
> | workspaceSettings | Nem |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | diagnosticSettings | Nem |
> | diagnosticSettingsCategories | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | aggregations | Nem |
> | alertRules | Nem |
> | alertRuleTemplates | Nem |
> | bookmarks | Nem |
> | cases | Nem |
> | dataConnectors | Nem |
> | entities | Nem |
> | entityQueries | Nem |
> | officeConsents | Nem |
> | beállítások | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | namespaces | Igen |
> | namespaces / authorizationrules | Nem |
> | namespaces / disasterrecoveryconfigs | Nem |
> | namespaces / eventgridfilters | Nem |
> | namespaces / networkrulesets | Nem |
> | namespaces / queues | Nem |
> | namespaces / queues / authorizationrules | Nem |
> | namespaces / topics | Nem |
> | namespaces / topics / authorizationrules | Nem |
> | namespaces / topics / subscriptions | Nem |
> | namespaces / topics / subscriptions / rules | Nem |
> | premiumMessagingRegions | Nem |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | clusters | Igen |
> | clusters / applications | Nem |
> | containerGroups | Igen |
> | containerGroupSets | Igen |
> | edgeclusters | Igen |
> | edgeclusters / applications | Nem |
> | networks | Igen |
> | secretstores | Igen |
> | secretstores / certificates | Nem |
> | secretstores / secrets | Nem |
> | volumes | Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | alkalmazás | Igen |
> | containerGroups | Igen |
> | gateways | Igen |
> | networks | Igen |
> | secrets | Igen |
> | volumes | Igen |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | providerRegistrations | Nem |
> | providerRegistrations / resourceTypeRegistrations | Nem |
> | rollouts | Igen |

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
> | hybridUseBenefits | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | applicationDefinitions | Igen |
> | alkalmazás | Igen |
> | jitRequests | Igen |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managedInstances | Igen |
> | managedInstances / databases | Igen |
> | managedInstances / databases / backupShortTermRetentionPolicies | Nem |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Nem |
> | managedInstances / databases / vulnerabilityAssessments | Nem |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Nem |
> | managedInstances / encryptionProtector | Nem |
> | managedInstances / keys | Nem |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nem |
> | managedInstances / vulnerabilityAssessments | Nem |
> | Kiszolgálók | Igen |
> | servers / administrators | Nem |
> | servers / communicationLinks | Nem |
> | servers / databases | Igen |
> | servers / encryptionProtector | Nem |
> | servers / firewallRules | Nem |
> | servers / keys | Nem |
> | servers / restorableDroppedDatabases | Nem |
> | servers / serviceobjectives | Nem |
> | servers / tdeCertificates | Nem |
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
> | storageAccounts / services | Nem |
> | storageAccounts / services / metricDefinitions | Nem |
> | storageAccounts / tableServices | Nem |
> | usages | Nem |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | caches | Igen |
> | caches / storageTargets | Nem |
> | usageModels | Nem |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | replicationGroups | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices / registeredServers | Nem |
> | storageSyncServices / syncGroups | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem |
> | storageSyncServices / workflows | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices / registeredServers | Nem |
> | storageSyncServices / syncGroups | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem |
> | storageSyncServices / workflows | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | storageSyncServices | Igen |
> | storageSyncServices / registeredServers | Nem |
> | storageSyncServices / syncGroups | Nem |
> | storageSyncServices / syncGroups / cloudEndpoints | Nem |
> | storageSyncServices / syncGroups / serverEndpoints | Nem |
> | storageSyncServices / workflows | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | managers | Igen |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | streamingjobs | Igen |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | cancel | Nem |
> | CreateSubscription | Nem |
> | enable | Nem |
> | rename | Nem |
> | SubscriptionDefinitions | Nem |
> | SubscriptionOperations | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | environments | Igen |
> | environments / accessPolicies | Nem |
> | environments / eventsources | Igen |
> | environments / referenceDataSets | Igen |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Igen |
> | dedicatedCloudServices | Igen |
> | virtualMachines | Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Törlés Teljes mód esetén |
> | ------------- | ----------- |
> | apiManagementAccounts | Nem |
> | apiManagementAccounts / apiAcls | Nem |
> | apiManagementAccounts / apis | Nem |
> | apiManagementAccounts / apis / apiAcls | Nem |
> | apiManagementAccounts / apis / connectionAcls | Nem |
> | apiManagementAccounts / apis / connections | Nem |
> | apiManagementAccounts / apis / connections / connectionAcls | Nem |
> | apiManagementAccounts / apis / localizedDefinitions | Nem |
> | apiManagementAccounts / connectionAcls | Nem |
> | apiManagementAccounts / connections | Nem |
> | billingMeters | Nem |
> | certificates | Igen |
> | connectionGateways | Igen |
> | connections | Igen |
> | customApis | Igen |
> | deletedSites | Nem |
> | functions | Nem |
> | hostingEnvironments | Igen |
> | hostingEnvironments / multiRolePools | Nem |
> | hostingEnvironments / workerPools | Nem |
> | publishingUsers | Nem |
> | recommendations | Nem |
> | resourceHealthMetadata | Nem |
> | runtimes | Nem |
> | serverFarms | Igen |
> | serverFarms / eventGridFilters | Nem |
> | sites | Igen |
> | sites/config  | Nem |
> | sites / eventGridFilters | Nem |
> | sites / hostNameBindings | Nem |
> | sites / networkConfig | Nem |
> | sites / premieraddons | Igen |
> | sites / slots | Igen |
> | sites / slots / eventGridFilters | Nem |
> | sites / slots / hostNameBindings | Nem |
> | sites / slots / networkConfig | Nem |
> | sourceControls | Nem |
> | validate | Nem |
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
> | components | Nem |
> | componentsSummary | Nem |
> | monitorInstances | Nem |
> | monitorInstancesSummary | Nem |
> | monitors | Nem |
> | notificationSettings | Nem |

## <a name="next-steps"></a>Következő lépések

To get the same data as a file of comma-separated values, download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
