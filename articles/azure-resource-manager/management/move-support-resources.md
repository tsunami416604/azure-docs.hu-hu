---
title: Művelet támogatásának áthelyezése erőforrástípus szerint
description: Felsorolja az új erőforráscsoporthoz vagy előfizetésbe áthelyezhető Azure-erőforrástípusok listáját.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 7b7f6f05c9cae80cffb64245a3fc8a6b0890d577
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539227"
---
# <a name="move-operation-support-for-resources"></a>Erőforrás-áthelyezési műveletek támogatása

Ez a cikk azt mutatja be, hogy az Azure-erőforrástípus támogatja-e az áthelyezési műveletet. Emellett az erőforrások áthelyezésekor megfontolandó speciális feltételekkel kapcsolatos információkat is tartalmaz.

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft. HRE](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. igazolás](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. felhasználás](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. Tárolószolgáltatás](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CostManagementExports](#microsoftcostmanagementexports)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft. Databricks](#microsoftdatabricks)
> - [Microsoft. DataCatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. DataExchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. DataLake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. Data Lake Store](#microsoftdatalakestore)
> - [Microsoft. DataMigration](#microsoftdatamigration)
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. kísérletezés](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. features](#microsoftfeatures)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. bepillantások](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kulcstartó](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
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
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. PowerPlatform](#microsoftpowerplatform)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft. Quantum](#microsoftquantum)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. előfizetés](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. szinapszis](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | domainservices | Nem | Nem |
> | domainservices / oucontainer | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem |
> | műveletek | Nem | Nem |
> | privatelinkforazuread | Igen | Igen |
> | bérlők | Igen | Igen |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | supportproviders | Nem | Nem |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Nem | Nem |
> | addsservices | Nem | Nem |
> | ügynökök | Nem | Nem |
> | anonymousapiusers | Nem | Nem |
> | konfiguráció | Nem | Nem |
> | naplók | Nem | Nem |
> | műveletek | Nem | Nem |
> | jelentések | Nem | Nem |
> | servicehealthmetrics | Nem | Nem |
> | services | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | konfigurációk | Nem | Nem |
> | generaterecommendations | Nem | Nem |
> | metaadatok | Nem | Nem |
> | műveletek | Nem | Nem |
> | javaslatok | Nem | Nem |
> | fóliakondenzát | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | actionrules | Igen | Igen |
> | riasztások | Nem | Nem |
> | alertslist | Nem | Nem |
> | alertsmetadata | Nem | Nem |
> | alertssummary | Nem | Nem |
> | alertssummarylist | Nem | Nem |
> | műveletek | Nem | Nem |
> | smartdetectoralertrules | Igen | Igen |
> | smartgroups | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checkfeedbackrequired | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | checkservicenameavailability | Nem | Nem |
> | műveletek | Nem | Nem |
> | reportfeedback | Nem | Nem |
> | szolgáltatás | Igen | Igen |
> | validateservicename | Nem | Nem |

> [!IMPORTANT]
> Nem helyezhető át olyan API Management szolgáltatás, amely a felhasználási SKU-ra van beállítva.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | configurationstores | Igen | Igen |
> | configurationstores / eventgridfilters | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationstatus | Nem | Nem |
> | műveletek | Nem | Nem |
> | Spring | Igen | Igen |
> | Spring/apps | Nem | Nem |
> | Spring/apps/üzemelő példányok | Nem | Nem |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | apiapps | Nem | Nem |
> | appidentities | Nem | Nem |
> | átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftattestation"></a>Microsoft. igazolás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | CheckAccess | Nem | Nem |
> | classicadministrators | Nem | Nem |
> | dataaliases | Nem | Nem |
> | denyassignments | Nem | Nem |
> | elevateaccess | Nem | Nem |
> | findorphanroleassignments | Nem | Nem |
> | zárak | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationstatus | Nem | Nem |
> | engedélyek | Nem | Nem |
> | policyassignments | Nem | Nem |
> | policydefinitions | Nem | Nem |
> | policysetdefinitions | Nem | Nem |
> | privatelinkassociations | Nem | Nem |
> | provideroperations | Nem | Nem |
> | resourcemanagementprivatelinks | Nem | Nem |
> | RoleAssignments | Nem | Nem |
> | roleassignmentsusagemetrics | Nem | Nem |
> | roledefinitions | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Igen | Igen |
> | automationaccounts/konfigurációk | Igen | Igen |
> | automationaccounts/feladatok | Nem | Nem |
> | automationaccounts / privateendpointconnectionproxies | Nem | Nem |
> | automationaccounts / privateendpointconnections | Nem | Nem |
> | automationaccounts / privatelinkresources | Nem | Nem |
> | automationaccounts/runbookok | Igen | Igen |
> | automationaccounts / softwareupdateconfigurations | Nem | Nem |
> | automationaccounts/webhookok | Nem | Nem |
> | műveletek | Nem | Nem |

> [!IMPORTANT]
> A runbookok ugyanabban az erőforráscsoporthoz kell tartoznia, mint az Automation-fióknak.
>
> További információ: [Azure Automation-fiók áthelyezése másik előfizetésre](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checkquotaavailability | Nem | Nem |
> | helyszínek/checktrialavailability | Nem | Nem |
> | műveletek | Nem | Nem |
> | privateclouds | Igen | Igen |
> | privateclouds/fürtök | Nem | Nem |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Igen | Igen |
> | b2ctenants | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Nem | Nem |
> | hybriddatamanagers | Nem | Nem |
> | műveletek | Nem | Nem |
> | postgresinstances | Nem | Nem |
> | sqlinstances | Nem | Nem |
> | sqlmanagedinstances | Nem | Nem |
> | sqlserverinstances | Nem | Nem |
> | sqlserverregistrations | Igen | Igen |
> | sqlserverregistrations / sqlservers | Nem | Nem |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nem | Nem |
> | műveletek | Nem | Nem |
> | regisztrációk | Igen | Igen |
> | regisztrációk/customersubscriptions | Nem | Nem |
> | regisztrációk/termékek | Nem | Nem |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/accountoperationresults | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/kvóták | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nem | Nem |
> | billingaccounts/szerződések | Nem | Nem |
> | billingaccounts / billingpermissions | Nem | Nem |
> | billingaccounts / billingprofiles | Nem | Nem |
> | billingaccounts / billingprofiles / availablebalance | Nem | Nem |
> | billingaccounts / billingprofiles / billingpermissions | Nem | Nem |
> | billingaccounts / billingprofiles / billingroleassignments | Nem | Nem |
> | billingaccounts / billingprofiles / billingroledefinitions | Nem | Nem |
> | billingaccounts / billingprofiles / billingsubscriptions | Nem | Nem |
> | billingaccounts / billingprofiles / createbillingroleassignment | Nem | Nem |
> | billingaccounts/billingprofiles/ügyfelek | Nem | Nem |
> | billingaccounts/billingprofiles/utasítások | Nem | Nem |
> | billingaccounts/billingprofiles/számlák | Nem | Nem |
> | billingaccounts/billingprofiles/számlák/árlista | Nem | Nem |
> | billingaccounts/billingprofiles/számlák/tranzakciók | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections / billingpermissions | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections / billingroleassignments | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections / billingroledefinitions | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections / billingsubscriptions | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections / createbillingroleassignment | Nem | Nem |
> | billingaccounts / billingprofiles / invoicesections / initiatetransfer | Nem | Nem |
> | billingaccounts/billingprofiles/invoicesections/termékek | Nem | Nem |
> | billingaccounts/billingprofiles/invoicesections/termékek/átvitel | Nem | Nem |
> | billingaccounts/billingprofiles/invoicesections/termékek/updateautorenew | Nem | Nem |
> | billingaccounts/billingprofiles/invoicesections/tranzakciók | Nem | Nem |
> | billingaccounts/billingprofiles/invoicesections/Transfers | Nem | Nem |
> | billingaccounts / billingprofiles / patchoperations | Nem | Nem |
> | billingaccounts / billingprofiles / paymentmethods | Nem | Nem |
> | billingaccounts/billingprofiles/házirendek | Nem | Nem |
> | billingaccounts/billingprofiles/árlista | Nem | Nem |
> | billingaccounts / billingprofiles / pricesheetdownloadoperations | Nem | Nem |
> | billingaccounts/billingprofiles/termékek | Nem | Nem |
> | billingaccounts/billingprofiles/tranzakciók | Nem | Nem |
> | billingaccounts / billingroleassignments | Nem | Nem |
> | billingaccounts / billingroledefinitions | Nem | Nem |
> | billingaccounts / billingsubscriptions | Nem | Nem |
> | billingaccounts/billingsubscriptions/számlák | Nem | Nem |
> | billingaccounts / createbillingroleassignment | Nem | Nem |
> | billingaccounts / createinvoicesectionoperations | Nem | Nem |
> | billingaccounts/ügyfelek | Nem | Nem |
> | billingaccounts/ügyfelek/billingpermissions | Nem | Nem |
> | billingaccounts/ügyfelek/billingsubscriptions | Nem | Nem |
> | billingaccounts/ügyfelek/initiatetransfer | Nem | Nem |
> | billingaccounts/ügyfelek/szabályzatok | Nem | Nem |
> | billingaccounts/ügyfelek/termékek | Nem | Nem |
> | billingaccounts/ügyfelek/tranzakciók | Nem | Nem |
> | billingaccounts/ügyfelek/átvitelek | Nem | Nem |
> | billingaccounts/részlegek | Nem | Nem |
> | billingaccounts / enrollmentaccounts | Nem | Nem |
> | billingaccounts/számlák | Nem | Nem |
> | billingaccounts / invoicesections | Nem | Nem |
> | billingaccounts / invoicesections / billingsubscriptionmoveoperations | Nem | Nem |
> | billingaccounts / invoicesections / billingsubscriptions | Nem | Nem |
> | billingaccounts/invoicesections/billingsubscriptions/átvitel | Nem | Nem |
> | billingaccounts/invoicesections/Jogosultságszint-emelés | Nem | Nem |
> | billingaccounts / invoicesections / initiatetransfer | Nem | Nem |
> | billingaccounts / invoicesections / patchoperations | Nem | Nem |
> | billingaccounts / invoicesections / productmoveoperations | Nem | Nem |
> | billingaccounts/invoicesections/termékek | Nem | Nem |
> | billingaccounts/invoicesections/termékek/átvitel | Nem | Nem |
> | billingaccounts/invoicesections/termékek/updateautorenew | Nem | Nem |
> | billingaccounts / invoicesections / producttransfersresults | Nem | Nem |
> | billingaccounts/invoicesections/tranzakciók | Nem | Nem |
> | billingaccounts/invoicesections/átvitel | Nem | Nem |
> | billingaccounts / lineofcredit | Nem | Nem |
> | billingaccounts / listinvoicesectionswithcreatesubscriptionpermission | Nem | Nem |
> | billingaccounts / operationresults | Nem | Nem |
> | billingaccounts / patchoperations | Nem | Nem |
> | billingaccounts / paymentmethods | Nem | Nem |
> | billingaccounts/termékek | Nem | Nem |
> | billingaccounts/tranzakciók | Nem | Nem |
> | billingperiods | Nem | Nem |
> | billingpermissions | Nem | Nem |
> | billingproperty | Nem | Nem |
> | billingroleassignments | Nem | Nem |
> | billingroledefinitions | Nem | Nem |
> | createbillingroleassignment | Nem | Nem |
> | részlegek | Nem | Nem |
> | enrollmentaccounts | Nem | Nem |
> | számlák | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationstatus | Nem | Nem |
> | transzferek | Nem | Nem |
> | átvitelek/accepttransfer | Nem | Nem |
> | átvitelek/declinetransfer | Nem | Nem |
> | átvitelek/operationstatus | Nem | Nem |
> | átvitelek/validatetransfer | Nem | Nem |
> | validateaddress | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | listcommunicationpreference | Nem | Nem |
> | mapapis | Nem | Nem |
> | műveletek | Nem | Nem |
> | updatecommunicationpreference | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | biztalk | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nem | Nem |
> | cordamembers | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/blockchainmemberoperationresults | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/listconsortiums | Nem | Nem |
> | helyszínek/watcheroperationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | Watchers | Nem | Nem |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |
> | tokenservices | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nem | Nem |
> | blueprintassignments / assignmentoperations | Nem | Nem |
> | blueprintassignments/műveletek | Nem | Nem |
> | tervrajzok | Nem | Nem |
> | tervrajzok/összetevők | Nem | Nem |
> | tervezetek/verziók | Nem | Nem |
> | tervrajzok/verziók/összetevők | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | botservices | Igen | Igen |
> | botservices/csatornák | Nem | Nem |
> | botservices/kapcsolatok | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | listauthserviceproviders | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | műveletek | Nem | Nem |
> | Redis | Igen | Igen |
> | Redis/eventgridfilters | Nem | Nem |
> | Redis/privatelinkresources | Nem | Nem |
> | redisenterprise | Nem | Nem |

> [!IMPORTANT]
> Ha az Azure cache for Redis-példány virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: [hálózati áthelyezési korlátozások](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nem | Nem |
> | calculateexchange | Nem | Nem |
> | calculateprice | Nem | Nem |
> | calculatepurchaseprice | Nem | Nem |
> | katalógusok | Nem | Nem |
> | checkoffers | Nem | Nem |
> | checkpurchasestatus | Nem | Nem |
> | checkscopes | Nem | Nem |
> | commercialreservationorders | Nem | Nem |
> | Exchange | Nem | Nem |
> | listbenefits | Nem | Nem |
> | műveletek | Nem | Nem |
> | placepurchaseorder | Nem | Nem |
> | reservationorders | Nem | Nem |
> | reservationorders / availablescopes | Nem | Nem |
> | reservationorders / calculaterefund | Nem | Nem |
> | reservationorders/egyesítés | Nem | Nem |
> | reservationorders/foglalások | Nem | Nem |
> | reservationorders/foglalások/availablescopes | Nem | Nem |
> | reservationorders/foglalások/változatok | Nem | Nem |
> | reservationorders/Return | Nem | Nem |
> | reservationorders/felosztás | Nem | Nem |
> | reservationorders/swap | Nem | Nem |
> | foglalások | Nem | Nem |
> | resources | Nem | Nem |
> | validatereservationorder | Nem | Nem |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nem | Nem |
> | cdnwebapplicationfirewallpolicies | Igen | Igen |
> | checknameavailability | Nem | Nem |
> | checkresourceusage | Nem | Nem |
> | edgenodes | Nem | Nem |
> | operationresults | Nem | Nem |
> | operationresults / profileresults | Nem | Nem |
> | operationresults / profileresults / endpointresults | Nem | Nem |
> | operationresults / profileresults / endpointresults / customdomainresults | Nem | Nem |
> | operationresults / profileresults / endpointresults / origingroupresults | Nem | Nem |
> | operationresults / profileresults / endpointresults / originresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | profilok | Igen | Igen |
> | profilok/végpontok | Igen | Igen |
> | profilok/végpontok/customdomains | Nem | Nem |
> | profilok/végpontok/origingroups | Nem | Nem |
> | profilok/végpontok/eredetek | Nem | Nem |
> | validateprobe | Nem | Nem |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tanúsítványrendelések | Igen | Igen |
> | tanúsítványrendelések/tanúsítványok | Nem | Nem |
> | műveletek | Nem | Nem |
> | validatecertificateregistrationinformation | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | képességek | Nem | Nem |
> | checkdomainnameavailability | Nem | Nem |
> | tartománynevek | Igen | Nem |
> | tartománynevek/képességek | Nem | Nem |
> | tartománynevek/internalloadbalancers | Nem | Nem |
> | tartománynevek/servicecertificates | Nem | Nem |
> | tartománynevek/tárolóhelyek | Nem | Nem |
> | tartománynevek/bővítőhelyek/szerepkörök | Nem | Nem |
> | tartománynevek/bővítőhelyek/szerepkörök/metricdefinitions | Nem | Nem |
> | tartománynevek/bővítőhelyek/szerepkörök/mérőszámok | Nem | Nem |
> | movesubscriptionresources | Nem | Nem |
> | operatingsystemfamilies | Nem | Nem |
> | operatingsystems | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationstatuses | Nem | Nem |
> | kvóták | Nem | Nem |
> | resourcetypes | Nem | Nem |
> | validatesubscriptionmoveavailability | Nem | Nem |
> | virtualmachines | Igen | Nem |
> | virtualmachines / diagnosticsettings | Nem | Nem |
> | virtualmachines / metricdefinitions | Nem | Nem |
> | virtualmachines/mérőszámok | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | képességek | Nem | Nem |
> | expressroutecrossconnections | Nem | Nem |
> | expressroutecrossconnections/társak | Nem | Nem |
> | gatewaysupporteddevices | Nem | Nem |
> | networksecuritygroups | Nem | Nem |
> | műveletek | Nem | Nem |
> | kvóták | Nem | Nem |
> | reservedips | Nem | Nem |
> | virtualnetworks | Nem | Nem |
> | virtualnetworks/remotevirtualnetworkpeeringproxies | Nem | Nem |
> | virtualnetworks/virtualnetworkpeerings | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | képességek | Nem | Nem |
> | checkstorageaccountavailability | Nem | Nem |
> | lemezek | Nem | Nem |
> | images | Nem | Nem |
> | műveletek | Nem | Nem |
> | osimages | Nem | Nem |
> | osplatformimages | Nem | Nem |
> | publicimages | Nem | Nem |
> | kvóták | Nem | Nem |
> | storageaccounts | Igen | Nem |
> | storageaccounts/blobservices | Nem | Nem |
> | storageaccounts/fileservices | Nem | Nem |
> | storageaccounts/metricdefinitions | Nem | Nem |
> | storageaccounts/mérőszámok | Nem | Nem |
> | storageaccounts/queueservices | Nem | Nem |
> | storageaccounts/szolgáltatások | Nem | Nem |
> | storageaccounts/szolgáltatások/diagnosticsettings | Nem | Nem |
> | storageaccounts/szolgáltatások/metricdefinitions | Nem | Nem |
> | storageaccounts/szolgáltatások/mérőszámok | Nem | Nem |
> | storageaccounts/tableservices | Nem | Nem |
> | storageaccounts/lemezképet | Nem | Nem |
> | lemezképet | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | checkdomainavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/checkskuavailability | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |
> | ratecard | Nem | Nem |
> | usageaggregates | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Igen | Igen |
> | diskaccesses | Nem | Nem |
> | diskencryptionsets | Nem | Nem |
> | lemezek | Igen | Igen |
> | katalógusok | Nem | Nem |
> | galériák/lemezképek | Nem | Nem |
> | galériák/lemezképek/verziók | Nem | Nem |
> | hostgroups | Nem | Nem |
> | hostgroups/gazdagépek | Nem | Nem |
> | images | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/artifactpublishers | Nem | Nem |
> | helyszínek/capsoperations | Nem | Nem |
> | helyszínek/kitelepülések | Nem | Nem |
> | helyszínek/loganalytics | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/közzétevők | Nem | Nem |
> | helyszínek/runcommands | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | helyszínek/virtualmachines | Nem | Nem |
> | helyszínek/méreteinek listáján | Nem | Nem |
> | helyszínek/vsmoperations | Nem | Nem |
> | műveletek | Nem | Nem |
> | proximityplacementgroups | Igen | Igen |
> | restorepointcollections | Nem | Nem |
> | restorepointcollections / restorepoints | Nem | Nem |
> | sharedvmextensions | Nem | Nem |
> | sharedvmimages | Nem | Nem |
> | sharedvmimages/verziók | Nem | Nem |
> | pillanatképek | Igen | Igen |
> | sshpublickeys | Nem | Nem |
> | virtualmachines | Igen | Igen |
> | virtualmachines/bővítmények | Igen | Igen |
> | virtualmachines / metricdefinitions | Nem | Nem |
> | virtualmachines / runcommands | Nem | Nem |
> | virtualmachinescalesets | Igen | Igen |
> | virtualmachinescalesets/bővítmények | Nem | Nem |
> | virtualmachinescalesets/networkinterfaces | Nem | Nem |
> | virtualmachinescalesets/nyilvános IP | Nem | Nem |
> | virtualmachinescalesets/virtualmachines | Nem | Nem |
> | virtualmachinescalesets/virtualmachines/networkinterfaces | Nem | Nem |

> [!IMPORTANT]
> Lásd: [Virtual Machines áthelyezési útmutató](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Nem | Nem |
> | elosztja | Nem | Nem |
> | költségvetése | Nem | Nem |
> | díjak | Nem | Nem |
> | costtags | Nem | Nem |
> | hitelek | Nem | Nem |
> | események | Nem | Nem |
> | előrejelzések | Nem | Nem |
> | számos | Nem | Nem |
> | piacterek | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationstatus | Nem | Nem |
> | pricesheets | Nem | Nem |
> | termékek | Nem | Nem |
> | reservationdetails | Nem | Nem |
> | reservationrecommendationdetails | Nem | Nem |
> | reservationrecommendations | Nem | Nem |
> | reservationsummaries | Nem | Nem |
> | reservationtransactions | Nem | Nem |
> | tags | Nem | Nem |
> | bérlők | Nem | Nem |
> | feltételek | Nem | Nem |
> | UsageDetails | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | containergroups | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/cachedimages | Nem | Nem |
> | helyszínek/képességek | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | műveletek | Nem | Nem |
> | serviceassociationlinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/engedélyezés | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/setupauth | Nem | Nem |
> | műveletek | Nem | Nem |
> | kibocsátásiegység | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/agentpools | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/agentpools/listqueuestatus | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/Mégse | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildek/getloglink | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks/listsourcerepositoryproperties | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks/lépések | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks/lépések/listbuildarguments | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/eventgridfilters | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/exportpipelines | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/generatecredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/getbuildsourceuploadurl | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/getcredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/importimage | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/importpipelines | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/listbuildsourceuploadurl | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/listcredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/ListPolicies | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/listusages | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/pipelineruns | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/privateendpointconnectionproxies | Nem | Nem |
> | nyilvántartások/privateendpointconnectionproxies/érvényesítés | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/privateendpointconnections | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/privatelinkresources | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/queuebuild | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regeneratecredential | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/regeneratecredentials | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/futtatások | Nem | Nem |
> | nyilvántartások/futtatások/megszakítás | Nem | Nem |
> | nyilvántartások/futtatások/listlogsasurl | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/schedulerun | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/scopemaps | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/taskruns | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/taskruns/listdetails | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Igen |
> | nyilvántartások/feladatok/listdetails | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/jogkivonatok | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/updatepolicies | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/getcallbackconfig | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/listevents | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok/ping | Nem | Nem |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | containerservices | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/openshiftclusters | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/rendszerszervezők | Nem | Nem |
> | managedclusters | Nem | Nem |
> | openshiftmanagedclusters | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Nem | Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | riasztások | Nem | Nem |
> | billingaccounts | Nem | Nem |
> | költségvetése | Nem | Nem |
> | cloudconnectors | Nem | Nem |
> | összekötők | Igen | Igen |
> | részlegek | Nem | Nem |
> | Méretek | Nem | Nem |
> | enrollmentaccounts | Nem | Nem |
> | export | Nem | Nem |
> | externalbillingaccounts | Nem | Nem |
> | externalbillingaccounts/riasztások | Nem | Nem |
> | externalbillingaccounts/méretek | Nem | Nem |
> | externalbillingaccounts/előrejelzés | Nem | Nem |
> | externalbillingaccounts/lekérdezés | Nem | Nem |
> | externalsubscriptions | Nem | Nem |
> | externalsubscriptions/riasztások | Nem | Nem |
> | externalsubscriptions/méretek | Nem | Nem |
> | externalsubscriptions/előrejelzés | Nem | Nem |
> | externalsubscriptions/lekérdezés | Nem | Nem |
> | forecast | Nem | Nem |
> | műveletek | Nem | Nem |
> | lekérdezés | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | reportconfigs | Nem | Nem |
> | jelentések | Nem | Nem |
> | beállítások | Nem | Nem |
> | showbackrules | Nem | Nem |
> | kilátással | Nem | Nem |

## <a name="microsoftcostmanagementexports"></a>Microsoft. CostManagementExports

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Hubs | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |
> | kérelmek | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | szövetségek | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | resourceproviders | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | feladatok | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/availableskus | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/regionconfiguration | Nem | Nem |
> | helyszínek/validateaddress | Nem | Nem |
> | helyszínek/validateinputs | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availableskus | Nem | Nem |
> | databoxedgedevices | Igen | Igen |
> | databoxedgedevices / checknameavailability | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/getnetworkpolicies | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | munkaterületek | Nem | Nem |
> | munkaterületek/dbworkspaces | Nem | Nem |
> | munkaterületek/virtualnetworkpeerings | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | katalógusok | Igen | Igen |
> | checknameavailability | Nem | Nem |
> | datacatalogs | Nem | Nem |
> | helyek | Nem | Nem |
> | helyek/feladatok | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | csomagok | Nem | Nem |
> | tervek | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checkazuredatafactorynameavailability | Nem | Nem |
> | checkdatafactorynameavailability | Nem | Nem |
> | datafactories | Igen | Igen |
> | datafactories / diagnosticsettings | Nem | Nem |
> | datafactories / metricdefinitions | Nem | Nem |
> | datafactoryschema | Nem | Nem |
> | előállítók | Igen | Igen |
> | gyárak/integrationruntimes | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/configurefactoryrepo | Nem | Nem |
> | helyszínek/getfeaturevalue | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | fiókok/datalakestoreaccounts | Nem | Nem |
> | fiókok/storageaccounts | Nem | Nem |
> | fiókok/storageaccounts/tárolók | Nem | Nem |
> | fiókok/storageaccounts/tárolók/listsastokens | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/képesség | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | fiókok/eventgridfilters | Nem | Nem |
> | fiókok/firewallrules | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/képesség | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | services | Nem | Nem |
> | szolgáltatások/projektek | Nem | Nem |
> | bővítőhely | Nem | Nem |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nem | Nem |
> | helyek | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | fiókok/megosztások | Nem | Nem |
> | fiókok/megosztások/adatkészletek | Nem | Nem |
> | fiókok/megosztások/meghívók | Nem | Nem |
> | fiókok/megosztások/providersharesubscriptions | Nem | Nem |
> | fiókok/megosztások/synchronizationsettings | Nem | Nem |
> | fiókok/sharesubscriptions | Nem | Nem |
> | fiókok/sharesubscriptions/consumersourcedatasets | Nem | Nem |
> | fiókok/sharesubscriptions/datasetmappings | Nem | Nem |
> | fiókok/sharesubscriptions/eseményindítók | Nem | Nem |
> | listinvitations | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/consumerinvitations | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/rejectinvitation | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/azureasyncoperation | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/performancetiers | Nem | Nem |
> | helyszínek/privateendpointconnectionazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionoperationresults | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyoperationresults | Nem | Nem |
> | helyszínek/recommendedactionsessionsazureasyncoperation | Nem | Nem |
> | helyszínek/recommendedactionsessionsoperationresults | Nem | Nem |
> | helyszínek/securityalertpoliciesazureasyncoperation | Nem | Nem |
> | helyszínek/securityalertpoliciesoperationresults | Nem | Nem |
> | helyszínek/serverkeyazureasyncoperation | Nem | Nem |
> | helyszínek/serverkeyoperationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/privateendpointconnectionproxies | Nem | Nem |
> | kiszolgálók/privateendpointconnections | Nem | Nem |
> | kiszolgálók/privatelinkresources | Nem | Nem |
> | kiszolgálók/querytexts | Nem | Nem |
> | kiszolgálók/recoverableservers | Nem | Nem |
> | kiszolgálók/topquerystatistics | Nem | Nem |
> | kiszolgálók/virtualnetworkrules | Nem | Nem |
> | kiszolgálók/waitstatistics | Nem | Nem |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/administratorazureasyncoperation | Nem | Nem |
> | helyszínek/administratoroperationresults | Nem | Nem |
> | helyszínek/azureasyncoperation | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/performancetiers | Nem | Nem |
> | helyszínek/privateendpointconnectionazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionoperationresults | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyoperationresults | Nem | Nem |
> | helyszínek/recommendedactionsessionsazureasyncoperation | Nem | Nem |
> | helyszínek/recommendedactionsessionsoperationresults | Nem | Nem |
> | helyszínek/securityalertpoliciesazureasyncoperation | Nem | Nem |
> | helyszínek/securityalertpoliciesoperationresults | Nem | Nem |
> | helyszínek/serverkeyazureasyncoperation | Nem | Nem |
> | helyszínek/serverkeyoperationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/privateendpointconnectionproxies | Nem | Nem |
> | kiszolgálók/privateendpointconnections | Nem | Nem |
> | kiszolgálók/privatelinkresources | Nem | Nem |
> | kiszolgálók/querytexts | Nem | Nem |
> | kiszolgálók/recoverableservers | Nem | Nem |
> | kiszolgálók/topquerystatistics | Nem | Nem |
> | kiszolgálók/virtualnetworkrules | Nem | Nem |
> | kiszolgálók/waitstatistics | Nem | Nem |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/administratorazureasyncoperation | Nem | Nem |
> | helyszínek/administratoroperationresults | Nem | Nem |
> | helyszínek/azureasyncoperation | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/performancetiers | Nem | Nem |
> | helyszínek/privateendpointconnectionazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionoperationresults | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyoperationresults | Nem | Nem |
> | helyszínek/recommendedactionsessionsazureasyncoperation | Nem | Nem |
> | helyszínek/recommendedactionsessionsoperationresults | Nem | Nem |
> | helyszínek/securityalertpoliciesazureasyncoperation | Nem | Nem |
> | helyszínek/securityalertpoliciesoperationresults | Nem | Nem |
> | helyszínek/serverkeyazureasyncoperation | Nem | Nem |
> | helyszínek/serverkeyoperationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | servergroups | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/privateendpointconnectionproxies | Nem | Nem |
> | kiszolgálók/privateendpointconnections | Nem | Nem |
> | kiszolgálók/privatelinkresources | Nem | Nem |
> | kiszolgálók/querytexts | Nem | Nem |
> | kiszolgálók/recoverableservers | Nem | Nem |
> | kiszolgálók/topquerystatistics | Nem | Nem |
> | kiszolgálók/virtualnetworkrules | Nem | Nem |
> | kiszolgálók/waitstatistics | Nem | Nem |
> | serversv2 | Igen | Igen |
> | singleservers | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | artifactsources | Igen | Igen |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | kibocsátások | Igen | Igen |
> | servicetopologies | Igen | Igen |
> | servicetopologies/szolgáltatások | Igen | Igen |
> | servicetopologies/szolgáltatások/serviceunits | Igen | Igen |
> | lépések | Igen | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Igen | Igen |
> | applicationgroups/alkalmazások | Nem | Nem |
> | applicationgroups/asztali számítógépek | Nem | Nem |
> | applicationgroups / startmenuitems | Nem | Nem |
> | hostpools | Igen | Igen |
> | hostpools / sessionhosts | Nem | Nem |
> | hostpools / sessionhosts / usersessions | Nem | Nem |
> | hostpools / usersessions | Nem | Nem |
> | műveletek | Nem | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | checkprovisioningservicenameavailability | Nem | Nem |
> | elasticpools | Nem | Nem |
> | elasticpools / iothubtenants | Nem | Nem |
> | iothubs | Igen | Igen |
> | iothubs/eventgridfilters | Nem | Nem |
> | iothubs/securitysettings | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | provisioningservices | Igen | Igen |
> | használat | Nem | Nem |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | csővezetékek | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartományvezérlők | Igen | Igen |
> | vezérlők/listconnectiondetails | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/checkcontainerhostmapping | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | labcenters | Nem | Nem |
> | Labs | Igen | Nem |
> | Labs/környezetek | Igen | Igen |
> | Labor/servicerunners | Igen | Igen |
> | Labor/virtualmachines | Igen | Nem |
> | helyek | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | műveletek | Nem | Nem |
> | menetrend | Igen | Igen |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nem | Nem |
> | digitaltwinsinstances / operationresults | Nem | Nem |
> | helyek | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nem | Nem |
> | databaseaccounts | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checkdomainavailability | Nem | Nem |
> | tartományok | Igen | Igen |
> | tartományok/domainownershipidentifiers | Nem | Nem |
> | generatessorequest | Nem | Nem |
> | listdomainrecommendations | Nem | Nem |
> | műveletek | Nem | Nem |
> | topleveldomains | Nem | Nem |
> | validatedomainregistrationinformation | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartományok | Igen | Igen |
> | tartományok/témakörök | Nem | Nem |
> | eventsubscriptions | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. |
> | extensiontopics | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/eventsubscriptions | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | helyszínek/topictypes | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationsstatus | Nem | Nem |
> | partnernamespaces | Igen | Igen |
> | partnernamespaces/eventchannels | Nem | Nem |
> | partnerregistrations | Nem | Nem |
> | partnertopics | Igen | Igen |
> | partnertopics / eventsubscriptions | Nem | Nem |
> | systemtopics | Igen | Igen |
> | systemtopics / eventsubscriptions | Nem | Nem |
> | témakörök | Igen | Igen |
> | topictypes | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availableclusterregions | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | checknamespaceavailability | Nem | Nem |
> | fürtök | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | névterek | Igen | Igen |
> | névterek/engedélyezési szabályok | Nem | Nem |
> | névterek/disasterrecoveryconfigs | Nem | Nem |
> | névterek/disasterrecoveryconfigs/checknameavailability | Nem | Nem |
> | névterek/eventhubs | Nem | Nem |
> | névterek/eventhubs/engedélyezési szabályok | Nem | Nem |
> | névterek/eventhubs/consumergroups | Nem | Nem |
> | névterek/networkrulesets | Nem | Nem |
> | műveletek | Nem | Nem |
> | SKU | Nem | Nem |

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |

## <a name="microsoftfeatures"></a>Microsoft. features

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | featureproviders | Nem | Nem |
> | funkciók | Nem | Nem |
> | műveletek | Nem | Nem |
> | szolgáltatók | Nem | Nem |
> | subscriptionfeatureregistrations | Nem | Nem |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Nem | Nem |
> | automanagedvmconfigurationprofiles | Nem | Nem |
> | guestconfigurationassignments | Nem | Nem |
> | műveletek | Nem | Nem |
> | szoftver | Nem | Nem |
> | softwareupdateprofile | Nem | Nem |
> | softwareupdates | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | műveletek | Nem | Nem |
> | sapmonitors | Igen | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Nem | Nem |
> | helyek | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |
> | fürtök/alkalmazások | Nem | Nem |
> | fürtök/operationresults | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/azureasyncoperations | Nem | Nem |
> | helyszínek/billingspecs | Nem | Nem |
> | helyszínek/képességek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | helyszínek/validatecreaterequest | Nem | Nem |
> | műveletek | Nem | Nem |

> [!IMPORTANT]
> A HDInsight-fürtöket új előfizetésre vagy erőforráscsoporthoz helyezheti át. Azonban a HDInsight-fürthöz társított hálózati erőforrások (például a virtuális hálózat, a hálózati adapter vagy a terheléselosztó) nem helyezhetők át az előfizetésekben. Emellett a fürthöz tartozó virtuális géphez csatlakoztatott hálózati adapterek nem helyezhetők át egy új erőforráscsoporthoz.
>
> Amikor új előfizetésre helyez át egy HDInsight-fürtöt, először helyezze át a többi erőforrást (például a Storage-fiókot). Ezután saját maga helyezheti át a HDInsight-fürtöt.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | services | Igen | Igen |
> | szolgáltatások/privateendpointconnections | Nem | Nem |
> | szolgáltatások/privatelinkresources | Nem | Nem |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationstatus | Nem | Nem |
> | gépek | Igen | Igen |
> | gépek/bővítmények | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datamanagers | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | eszközök | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | vnfs | Nem | Nem |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összetevők | Nem | Nem |
> | helyek | Nem | Nem |
> | networkscopes | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | feladatok | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | actiongroups | Igen | Igen |
> | activitylogalerts | Nem | Nem |
> | alertrules | Igen | Igen |
> | autoscalesettings | Igen | Igen |
> | alapkonfiguráció | Nem | Nem |
> | calculatebaseline | Nem | Nem |
> | összetevők | Igen | Igen |
> | összetevők/események | Nem | Nem |
> | összetevők/linkedstorageaccounts | Nem | Nem |
> | összetevők/metaadatok | Nem | Nem |
> | összetevők/mérőszámok | Nem | Nem |
> | összetevők/pricingplans | Nem | Nem |
> | összetevők/lekérdezés | Nem | Nem |
> | datacollectionrules | Nem | Nem |
> | diagnosticsettings | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem |
> | eventcategories | Nem | Nem |
> | eventtypes | Nem | Nem |
> | extendeddiagnosticsettings | Nem | Nem |
> | guestdiagnosticsettings | Nem | Nem |
> | listmigrationdate | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | logdefinitions | Nem | Nem |
> | logprofiles | Nem | Nem |
> | naplók | Nem | Nem |
> | metricalerts | Nem | Nem |
> | metricbaselines | Nem | Nem |
> | metricbatch | Nem | Nem |
> | metricdefinitions | Nem | Nem |
> | metricnamespaces | Nem | Nem |
> | metrics | Nem | Nem |
> | migratealertrules | Nem | Nem |
> | migratetonewpricingmodel | Nem | Nem |
> | myworkbooks | Nem | Nem |
> | notificationgroups | Nem | Nem |
> | műveletek | Nem | Nem |
> | privatelinkscopeoperationstatuses | Nem | Nem |
> | privatelinkscopes | Nem | Nem |
> | privatelinkscopes / privateendpointconnectionproxies | Nem | Nem |
> | privatelinkscopes / privateendpointconnections | Nem | Nem |
> | privatelinkscopes / scopedresources | Nem | Nem |
> | rollbacktolegacypricingmodel | Nem | Nem |
> | scheduledqueryrules | Igen | Igen |
> | topology | Nem | Nem |
> | transactions | Nem | Nem |
> | vminsightsonboardingstatuses | Nem | Nem |
> | webteszteket | Igen | Igen |
> | webtesztek/gettestresultfile | Nem | Nem |
> | munkafüzetek | Igen | Igen |
> | workbooktemplates | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | checksubdomainavailability | Nem | Nem |
> | iotapps | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Igen | Igen |
> | Graph | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | deletedvaults | Nem | Nem |
> | hsmpools | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/deletedvaults | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | managedhsms | Nem | Nem |
> | műveletek | Nem | Nem |
> | boltívek | Igen | Igen |
> | tárolók/accesspolicies | Nem | Nem |
> | tárolók/eventgridfilters | Nem | Nem |
> | tárolók/titkok | Nem | Nem |

> [!IMPORTANT]
> A lemezes titkosításhoz használt kulcstartók nem helyezhetők át ugyanabba az előfizetésbe vagy előfizetésbe tartozó erőforráscsoporthoz.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | registeredsubscriptions | Nem | Nem |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |
> | fürtök/attacheddatabaseconfigurations | Nem | Nem |
> | fürtök/adatbázisok | Nem | Nem |
> | fürtök/adatbázisok/dataconnections | Nem | Nem |
> | fürtök/adatbázisok/eventhubconnections | Nem | Nem |
> | fürtök/adatbázisok/principalassignments | Nem | Nem |
> | fürtök/principalassignments | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | műveletek | Nem | Nem |
> | felhasználók | Nem | Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nem | Nem |
> | integrationaccounts | Igen | Igen |
> | integrationserviceenvironments | Igen | Nem |
> | integrationserviceenvironments/król | Igen | Nem |
> | isolatedenvironments | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/munkafolyamatok | Nem | Nem |
> | műveletek | Nem | Nem |
> | munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | műveletek | Nem | Nem |
> | WebServices | Igen | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nem | Nem |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | fiókok/munkaterületek | Nem | Nem |
> | fiókok/munkaterületek/projektek | Nem | Nem |
> | teamaccounts | Nem | Nem |
> | teamaccounts/munkaterületek | Nem | Nem |
> | teamaccounts/munkaterületek/projektek | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/computeoperationsstatus | Nem | Nem |
> | helyszínek/kvóták | Nem | Nem |
> | helyszínek/updatequotas | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | helyszínek/méreteinek listáján | Nem | Nem |
> | helyszínek/workspaceoperationsstatus | Nem | Nem |
> | műveletek | Nem | Nem |
> | munkaterületek | Nem | Nem |
> | munkaterületek/számítások | Nem | Nem |
> | munkaterületek/eventgridfilters | Nem | Nem |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applyupdates | Nem | Nem |
> | configurationassignments | Nem | Nem |
> | maintenanceconfigurations | Igen | Igen |
> | frissítések | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | identitások | Nem | Nem |
> | műveletek | Nem | Nem |
> | userassignedidentities | Nem | Nem |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | managednetworks | Nem | Nem |
> | managednetworks / managednetworkgroups | Nem | Nem |
> | managednetworks / managednetworkpeeringpolicies | Nem | Nem |
> | értesítés | Nem | Nem |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationstatuses | Nem | Nem |
> | registrationassignments | Nem | Nem |
> | registrationdefinitions | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | getentities | Nem | Nem |
> | managementgroups | Nem | Nem |
> | managementgroups/beállítások | Nem | Nem |
> | operationresults | Nem | Nem |
> | operationresults/asyncoperation | Nem | Nem |
> | műveletek | Nem | Nem |
> | resources | Nem | Nem |
> | starttenantbackfill | Nem | Nem |
> | tenantbackfillstatus | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | fiókok/eventgridfilters | Nem | Nem |
> | fiókok/privateatlases | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | listavailableoffers | Nem | Nem |
> | kínál | Nem | Nem |
> | offertypes | Nem | Nem |
> | offertypes/közzétevők | Nem | Nem |
> | offertypes/kiadók/ajánlatok | Nem | Nem |
> | offertypes/kiadók/ajánlatok/csomagok | Nem | Nem |
> | offertypes/kiadók/ajánlatok/csomagok/szerződések | Nem | Nem |
> | offertypes/kiadók/ajánlatok/csomagok/konfigurációk | Nem | Nem |
> | offertypes/kiadók/ajánlatok/csomagok/konfigurációk/importimage | Nem | Nem |
> | műveletek | Nem | Nem |
> | privategalleryitems | Nem | Nem |
> | privatestoreclient | Nem | Nem |
> | privatestores | Nem | Nem |
> | privatestores/ajánlatok | Nem | Nem |
> | termékek | Nem | Nem |
> | közzétevők | Nem | Nem |
> | kiadók/ajánlatok | Nem | Nem |
> | közzétevők/ajánlatok/módosítások | Nem | Nem |
> | Regisztráció | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nem | Nem |
> | listcommunicationpreference | Nem | Nem |
> | műveletek | Nem | Nem |
> | updatecommunicationpreference | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | megállapodások | Nem | Nem |
> | offertypes | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | Mediaservices | Igen | Igen |
> | Mediaservices/accountfilters | Nem | Nem |
> | Mediaservices/-eszközök | Nem | Nem |
> | Mediaservices/eszközök/assetfilters | Nem | Nem |
> | Mediaservices/contentkeypolicies | Nem | Nem |
> | Mediaservices/eventgridfilters | Nem | Nem |
> | Mediaservices/liveeventoperations | Nem | Nem |
> | Mediaservices/liveevents | Igen | Igen |
> | Mediaservices/liveevents/liveoutputs | Nem | Nem |
> | Mediaservices/liveoutputoperations | Nem | Nem |
> | Mediaservices/streamingendpointoperations | Nem | Nem |
> | Mediaservices/streamingendpoints | Igen | Igen |
> | Mediaservices/streaminglocators | Nem | Nem |
> | Mediaservices/streamingpolicies | Nem | Nem |
> | Mediaservices/átalakítások | Nem | Nem |
> | Mediaservices/átalakítások/feladatok | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | appclusters | Nem | Nem |

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/assessmentoptions | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | migrateprojects | Nem | Nem |
> | movecollections | Nem | Nem |
> | műveletek | Nem | Nem |
> | projektek | Nem | Nem |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | objectunderstandingaccounts | Nem | Nem |
> | műveletek | Nem | Nem |
> | remoterenderingaccounts | Igen | Igen |
> | spatialanchorsaccounts | Igen | Igen |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nem | Nem |
> | netappaccounts / backuppolicies | Nem | Nem |
> | netappaccounts / capacitypools | Nem | Nem |
> | netappaccounts/capacitypools/kötetek | Nem | Nem |
> | netappaccounts/capacitypools/kötetek/mounttargets | Nem | Nem |
> | netappaccounts/capacitypools/kötetek/Pillanatképek | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applicationgatewayavailablerequestheaders | Nem | Nem |
> | applicationgatewayavailableresponseheaders | Nem | Nem |
> | applicationgatewayavailableservervariables | Nem | Nem |
> | applicationgatewayavailablessloptions | Nem | Nem |
> | applicationgatewayavailablewafrulesets | Nem | Nem |
> | applicationgateways | Nem | Nem |
> | applicationgatewaywebapplicationfirewallpolicies | Nem | Nem |
> | applicationsecuritygroups | Igen | Igen |
> | azurefirewallfqdntags | Nem | Nem |
> | azurefirewalls | Nem | Nem |
> | bastionhosts | Nem | Nem |
> | bgpservicecommunities | Nem | Nem |
> | checkfrontdoornameavailability | Nem | Nem |
> | checktrafficmanagernameavailability | Nem | Nem |
> | kapcsolatok | Igen | Igen |
> | ddoscustompolicies | Igen | Igen |
> | ddosprotectionplans | Nem | Nem |
> | dnsoperationresults | Nem | Nem |
> | dnsoperationstatuses | Nem | Nem |
> | dnszones | Igen | Igen |
> | dnszones/a | Nem | Nem |
> | dnszones/AAAA | Nem | Nem |
> | dnszones/mind | Nem | Nem |
> | dnszones/CAA | Nem | Nem |
> | dnszones/CNAME | Nem | Nem |
> | dnszones/MX | Nem | Nem |
> | dnszones/NS | Nem | Nem |
> | dnszones/PTR | Nem | Nem |
> | dnszones/rekordhalmazok | Nem | Nem |
> | dnszones/SOA | Nem | Nem |
> | dnszones/SRV | Nem | Nem |
> | dnszones/txt | Nem | Nem |
> | expressroutecircuits | Nem | Nem |
> | expressroutegateways | Nem | Nem |
> | expressrouteserviceproviders | Nem | Nem |
> | firewallpolicies | Igen | Igen |
> | frontdooroperationresults | Nem | Nem |
> | frontdoors | Nem | Nem |
> | frontdoors / frontendendpoints | Nem | Nem |
> | frontdoorwebapplicationfirewallmanagedrulesets | Nem | Nem |
> | frontdoorwebapplicationfirewallpolicies | Nem | Nem |
> | getdnsresourcereference | Nem | Nem |
> | internalnotify | Nem | Nem |
> | ipallocations | Igen | Igen |
> | ipgroups | Igen | Igen |
> | loadbalancers | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | localnetworkgateways | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/autoapprovedprivatelinkservices | Nem | Nem |
> | helyszínek/availabledelegations | Nem | Nem |
> | helyszínek/availableprivateendpointtypes | Nem | Nem |
> | helyszínek/availableservicealiases | Nem | Nem |
> | helyszínek/baremetaltenants | Nem | Nem |
> | helyszínek/batchnotifyprivateendpointsforresourcemove | Nem | Nem |
> | helyszínek/batchvalidateprivateendpointsforresourcemove | Nem | Nem |
> | helyszínek/checkacceleratednetworkingsupport | Nem | Nem |
> | helyszínek/checkdnsnameavailability | Nem | Nem |
> | helyszínek/checkprivatelinkservicevisibility | Nem | Nem |
> | helyszínek/commitinternalazurenetworkmanagerconfiguration | Nem | Nem |
> | helyszínek/effectiveresourceownership | Nem | Nem |
> | helyszínek/nfvoperationresults | Nem | Nem |
> | helyszínek/nfvoperations | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/servicetags | Nem | Nem |
> | helyszínek/setresourceownership | Nem | Nem |
> | helyszínek/supportedvirtualmachinesizes | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | helyszínek/validateresourceownership | Nem | Nem |
> | helyszínek/virtualnetworkavailableendpointservices | Nem | Nem |
> | natgateways | Nem | Nem |
> | networkexperimentprofiles | Nem | Nem |
> | networkintentpolicies | Igen | Igen |
> | networkinterfaces | Igen | Igen |
> | networkprofiles | Nem | Nem |
> | networksecuritygroups | Igen | Igen |
> | networkwatchers | Igen | Nem |
> | networkwatchers / connectionmonitors | Igen | Nem |
> | networkwatchers / flowlogs | Igen | Nem |
> | networkwatchers / pingmeshes | Igen | Nem |
> | műveletek | Nem | Nem |
> | p2svpngateways | Nem | Nem |
> | privatednsoperationresults | Nem | Nem |
> | privatednsoperationstatuses | Nem | Nem |
> | privatednszones | Igen | Igen |
> | privatednszones/a | Nem | Nem |
> | privatednszones/AAAA | Nem | Nem |
> | privatednszones/mind | Nem | Nem |
> | privatednszones/CNAME | Nem | Nem |
> | privatednszones/MX | Nem | Nem |
> | privatednszones/PTR | Nem | Nem |
> | privatednszones/SOA | Nem | Nem |
> | privatednszones/SRV | Nem | Nem |
> | privatednszones/txt | Nem | Nem |
> | privatednszones / virtualnetworklinks | Igen | Igen |
> | privatednszonesinternal | Nem | Nem |
> | privateendpointredirectmaps | Nem | Nem |
> | privateendpoints | Igen | Igen |
> | privatelinkservices | Nem | Nem |
> | nyilvános IP | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | publicipprefixes | Igen | Igen |
> | routefilters | Nem | Nem |
> | routetables | Igen | Igen |
> | securitypartnerproviders | Igen | Igen |
> | serviceendpointpolicies | Igen | Igen |
> | trafficmanagergeographichierarchies | Nem | Nem |
> | trafficmanagerprofiles | Igen | Igen |
> | trafficmanagerprofiles/intenzitástérképei | Nem | Nem |
> | trafficmanagerusermetricskeys | Nem | Nem |
> | virtualhubs | Nem | Nem |
> | virtualnetworkgateways | Igen | Igen |
> | virtualnetworks | Igen | Igen |
> | virtualnetworktaps | Nem | Nem |
> | virtualrouters | Igen | Igen |
> | virtualwans | Nem | Nem |
> | vpngateways (virtuális WAN) | Nem | Nem |
> | vpnserverconfigurations | Nem | Nem |
> | vpnsites (virtuális WAN) | Nem | Nem |

> [!IMPORTANT]
> Lásd: [hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | checknamespaceavailability | Nem | Nem |
> | névterek | Igen | Igen |
> | névterek/notificationhubs | Igen | Igen |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Igen | Igen |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hypervsites | Nem | Nem |
> | importsites | Nem | Nem |
> | műveletek | Nem | Nem |
> | serversites | Nem | Nem |
> | vmwaresites | Nem | Nem |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | deletedworkspaces | Nem | Nem |
> | linktargets | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | storageinsightconfigs | Nem | Nem |
> | munkaterületek | Igen | Igen |
> | munkaterületek/adatforrások | Nem | Nem |
> | munkaterületek/linkedservices | Nem | Nem |
> | munkaterületek/linkedstorageaccounts | Nem | Nem |
> | munkaterületek/metaadatok | Nem | Nem |
> | munkaterületek/lekérdezés | Nem | Nem |
> | munkaterületek/scopedprivatelinkproxies | Nem | Nem |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits).
>
> A csatolt Automation-fiókkal rendelkező munkaterületek nem helyezhetők át. Az áthelyezési művelet megkezdése előtt ne felejtse el összekapcsolni az Automation-fiókokat.

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nem | Nem |
> | managementconfigurations | Igen | Igen |
> | műveletek | Nem | Nem |
> | megoldások | Igen | Igen |
> | kilátással | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checkserviceprovideravailability | Nem | Nem |
> | legacypeerings | Nem | Nem |
> | műveletek | Nem | Nem |
> | peerasns | Nem | Nem |
> | peeringlocations | Nem | Nem |
> | társviszonyok | Nem | Nem |
> | peeringservicecountries | Nem | Nem |
> | peeringservicelocations | Nem | Nem |
> | peeringserviceproviders | Nem | Nem |
> | peeringservices | Nem | Nem |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | asyncoperationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | policyevents | Nem | Nem |
> | policystates | Nem | Nem |
> | policytrackedresources | Nem | Nem |
> | szervizelések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> |  -konzolok | Nem | Nem |
> | irányítópultok | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/konzolok | Nem | Nem |
> | helyszínek/usersettings | Nem | Nem |
> | műveletek | Nem | Nem |
> | usersettings | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kapacitások | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftpowerplatform"></a>Microsoft. PowerPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nem | Nem |
> | providerregistrations | Nem | Nem |
> | providerregistrations / resourcetyperegistrations | Nem | Nem |
> | kibocsátások | Nem | Nem |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/allocatedstamp | Nem | Nem |
> | helyszínek/allocatestamp | Nem | Nem |
> | helyszínek/backupaadproperties | Nem | Nem |
> | helyszínek/backupcrossregionrestore | Nem | Nem |
> | helyszínek/backupcrrjob | Nem | Nem |
> | helyszínek/backupcrrjobs | Nem | Nem |
> | helyszínek/backupcrroperationresults | Nem | Nem |
> | helyszínek/backupcrroperationsstatus | Nem | Nem |
> | helyszínek/backupprevalidateprotection | Nem | Nem |
> | helyszínek/backupstatus | Nem | Nem |
> | helyszínek/backupvalidatefeatures | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | műveletek | Nem | Nem |
> | replicationeligibilityresults | Nem | Nem |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationsstatus | Nem | Nem |
> | openshiftclusters | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | névterek | Igen | Igen |
> | névterek/engedélyezési szabályok | Nem | Nem |
> | névterek/hybridconnections | Nem | Nem |
> | névterek/hybridconnections/engedélyezési szabályok | Nem | Nem |
> | névterek/privateendpointconnections | Nem | Nem |
> | névterek/wcfrelays | Nem | Nem |
> | névterek/wcfrelays/engedélyezési szabályok | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |
> | lekérdezések | Igen | Igen |
> | resourcechangedetails | Nem | Nem |
> | resourcechanges | Nem | Nem |
> | resources | Nem | Nem |
> | resourceshistory | Nem | Nem |
> | subscriptionsstatus | Nem | Nem |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nem | Nem |
> | childavailabilitystatuses | Nem | Nem |
> | childresources | Nem | Nem |
> | emergingissues | Nem | Nem |
> | események | Nem | Nem |
> | metaadatok | Nem | Nem |
> | értesítések | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | calculatetemplatehash | Nem | Nem |
> | checkpolicycompliance | Nem | Nem |
> | checkresourcename | Nem | Nem |
> | központi telepítések | Nem | Nem |
> | üzembe helyezések/műveletek | Nem | Nem |
> | deploymentscripts | Nem | Nem |
> | deploymentscripts/naplók | Nem | Nem |
> | linkek | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/deploymentscriptoperationresults | Nem | Nem |
> | notifyresourcejobs | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | szolgáltatók | Nem | Nem |
> | resourcegroups | Nem | Nem |
> | resources | Nem | Nem |
> | előfizetések | Nem | Nem |
> | előfizetések/helyszínek | Nem | Nem |
> | előfizetések/operationresults | Nem | Nem |
> | előfizetések/szolgáltatók | Nem | Nem |
> | előfizetések/resourcegroups | Nem | Nem |
> | előfizetések/resourcegroups/erőforrások | Nem | Nem |
> | előfizetések/erőforrások | Nem | Nem |
> | előfizetések/tagNames | Nem | Nem |
> | előfizetések/tagNames/tagvalues | Nem | Nem |
> | tags | Nem | Nem |
> | templatespecs | Nem | Nem |
> | templatespecs/verziók | Nem | Nem |
> | bérlők | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Nem |
> | checkmoderneligibility | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | saasresources | Nem | Nem |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | checkservicenameavailability | Nem | Nem |
> | műveletek | Nem | Nem |
> | resourcehealthmetadata | Nem | Nem |
> | searchservices | Igen | Igen |

> [!IMPORTANT]
> Egy műveletben nem helyezhető át több keresési erőforrás különböző régiókban. Ehelyett külön műveletekben helyezze át őket.

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nem | Nem |
> | advancedthreatprotectionsettings | Nem | Nem |
> | riasztások | Nem | Nem |
> | allowedconnections | Nem | Nem |
> | applicationwhitelistings | Nem | Nem |
> | assessmentmetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | autodismissalertsrules | Nem | Nem |
> | automatizálások szabványának létrehozásában | Igen | Igen |
> | autoprovisioningsettings | Nem | Nem |
> | complianceresults | Nem | Nem |
> | felelésről | Nem | Nem |
> | datacollectionagents | Nem | Nem |
> | devicesecuritygroups | Nem | Nem |
> | discoveredsecuritysolutions | Nem | Nem |
> | externalsecuritysolutions | Nem | Nem |
> | informationprotectionpolicies | Nem | Nem |
> | iotsecuritysolutions | Igen | Igen |
> | iotsecuritysolutions / analyticsmodels | Nem | Nem |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Nem | Nem |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Nem | Nem |
> | jitnetworkaccesspolicies | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/riasztások | Nem | Nem |
> | helyszínek/allowedconnections | Nem | Nem |
> | helyszínek/applicationwhitelistings | Nem | Nem |
> | helyszínek/discoveredsecuritysolutions | Nem | Nem |
> | helyszínek/externalsecuritysolutions | Nem | Nem |
> | helyszínek/jitnetworkaccesspolicies | Nem | Nem |
> | helyszínek/securitysolutions | Nem | Nem |
> | helyszínek/securitysolutionsreferencedata | Nem | Nem |
> | helyszínek/feladatok | Nem | Nem |
> | helyszínek/topológiák | Nem | Nem |
> | műveletek | Nem | Nem |
> | policies | Nem | Nem |
> | pricings | Nem | Nem |
> | regulatorycompliancestandards | Nem | Nem |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Nem | Nem |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Nem | Nem |
> | securitycontacts | Nem | Nem |
> | securitysolutions | Nem | Nem |
> | securitysolutionsreferencedata | Nem | Nem |
> | securitystatuses | Nem | Nem |
> | securitystatusessummaries | Nem | Nem |
> | servervulnerabilityassessments | Nem | Nem |
> | beállítások | Nem | Nem |
> | alértékelések | Nem | Nem |
> | feladatok | Nem | Nem |
> | topológiák | Nem | Nem |
> | workspacesettings | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összesítések | Nem | Nem |
> | alertrules | Nem | Nem |
> | alertruletemplates | Nem | Nem |
> | automationrules | Nem | Nem |
> | könyvjelzők | Nem | Nem |
> | esetekben | Nem | Nem |
> | dataconnectors | Nem | Nem |
> | dataconnectorscheckrequirements | Nem | Nem |
> | szervezetek | Nem | Nem |
> | entityqueries | Nem | Nem |
> | incidensek | Nem | Nem |
> | officeconsents | Nem | Nem |
> | műveletek | Nem | Nem |
> | beállítások | Nem | Nem |
> | threatintelligence | Nem | Nem |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/consoleservices | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | átjárók | Nem | Nem |
> | csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | checknamespaceavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | névterek | Igen | Igen |
> | névterek/engedélyezési szabályok | Nem | Nem |
> | névterek/disasterrecoveryconfigs | Nem | Nem |
> | névterek/disasterrecoveryconfigs/checknameavailability | Nem | Nem |
> | névterek/eventgridfilters | Nem | Nem |
> | névterek/networkrulesets | Nem | Nem |
> | névterek/várólisták | Nem | Nem |
> | névterek/várólisták/engedélyezési szabályok | Nem | Nem |
> | névterek/témakörök | Nem | Nem |
> | névterek/témakörök/engedélyezési szabályok | Nem | Nem |
> | névterek/témakörök/előfizetések | Nem | Nem |
> | névterek/témakörök/előfizetések/szabályok | Nem | Nem |
> | műveletek | Nem | Nem |
> | premiummessagingregions | Nem | Nem |
> | SKU | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Nem | Nem |
> | fürtök | Igen | Igen |
> | fürtök/alkalmazások | Nem | Nem |
> | containergroups | Nem | Nem |
> | containergroupsets | Nem | Nem |
> | edgeclusters | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/clusterversions | Nem | Nem |
> | helyszínek/környezetek | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | managedclusters | Nem | Nem |
> | hálózatok | Nem | Nem |
> | műveletek | Nem | Nem |
> | secretstores | Nem | Nem |
> | volumes | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Igen |
> | containergroups | Nem | Nem |
> | átjárók | Igen | Igen |
> | helyek | Nem | Nem |
> | helyszínek/applicationoperations | Nem | Nem |
> | helyszínek/gatewayoperations | Nem | Nem |
> | helyszínek/networkoperations | Nem | Nem |
> | helyszínek/secretoperations | Nem | Nem |
> | helyszínek/volumeoperations | Nem | Nem |
> | hálózatok | Igen | Igen |
> | műveletek | Nem | Nem |
> | titkok | Igen | Igen |
> | volumes | Igen | Igen |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kibocsátások | Nem | Nem |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | műveletek | Nem | Nem |
> | signalr | Igen | Igen |
> | jelző/eventgridfilters | Nem | Nem |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nem | Nem |
> | alkalmazások | Nem | Nem |
> | jitrequests | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | instancepools | Nem | Nem |
> | helyek | Igen | Igen |
> | helyszínek/administratorazureasyncoperation | Nem | Nem |
> | helyszínek/administratoroperationresults | Nem | Nem |
> | helyszínek/auditingsettingsazureasyncoperation | Nem | Nem |
> | helyszínek/auditingsettingsoperationresults | Nem | Nem |
> | helyszínek/képességek | Nem | Nem |
> | helyszínek/databaseazureasyncoperation | Nem | Nem |
> | helyszínek/databaseoperationresults | Nem | Nem |
> | helyszínek/databaserestoreazureasyncoperation | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnetsazureasyncoperation | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnetsoperationresults | Nem | Nem |
> | helyszínek/dnsaliasasyncoperation | Nem | Nem |
> | helyszínek/dnsaliasoperationresults | Nem | Nem |
> | helyszínek/elasticpoolazureasyncoperation | Nem | Nem |
> | helyszínek/elasticpooloperationresults | Nem | Nem |
> | helyszínek/encryptionprotectorazureasyncoperation | Nem | Nem |
> | helyszínek/encryptionprotectoroperationresults | Nem | Nem |
> | helyszínek/extendedauditingsettingsazureasyncoperation | Nem | Nem |
> | helyszínek/extendedauditingsettingsoperationresults | Nem | Nem |
> | helyszínek/failovergroupazureasyncoperation | Nem | Nem |
> | helyszínek/failovergroupoperationresults | Nem | Nem |
> | helyszínek/firewallrulesazureasyncoperation | Nem | Nem |
> | helyszínek/firewallrulesoperationresults | Nem | Nem |
> | helyszínek/instancefailovergroupazureasyncoperation | Nem | Nem |
> | helyszínek/instancefailovergroupoperationresults | Nem | Nem |
> | helyszínek/instancefailovergroups | Nem | Nem |
> | helyszínek/instancepoolazureasyncoperation | Nem | Nem |
> | helyszínek/instancepooloperationresults | Nem | Nem |
> | helyszínek/jobagentazureasyncoperation | Nem | Nem |
> | helyszínek/jobagentoperationresults | Nem | Nem |
> | helyszínek/longtermretentionbackupazureasyncoperation | Nem | Nem |
> | helyszínek/longtermretentionbackupoperationresults | Nem | Nem |
> | helyszínek/longtermretentionbackups | Nem | Nem |
> | helyszínek/longtermretentionmanagedinstancebackupazureasyncoperation | Nem | Nem |
> | helyszínek/longtermretentionmanagedinstancebackupoperationresults | Nem | Nem |
> | helyszínek/longtermretentionmanagedinstancebackups | Nem | Nem |
> | helyszínek/longtermretentionmanagedinstances | Nem | Nem |
> | helyszínek/longtermretentionpolicyazureasyncoperation | Nem | Nem |
> | helyszínek/longtermretentionpolicyoperationresults | Nem | Nem |
> | helyszínek/longtermretentionservers | Nem | Nem |
> | helyszínek/manageddatabaseazureasyncoperation | Nem | Nem |
> | helyszínek/manageddatabasecompleterestoreazureasyncoperation | Nem | Nem |
> | helyszínek/manageddatabasecompleterestoreoperationresults | Nem | Nem |
> | helyszínek/manageddatabaseoperationresults | Nem | Nem |
> | helyszínek/manageddatabaserestoreazureasyncoperation | Nem | Nem |
> | helyszínek/manageddatabaserestoreoperationresults | Nem | Nem |
> | helyszínek/managedinstanceazureasyncoperation | Nem | Nem |
> | helyszínek/managedinstanceencryptionprotectorazureasyncoperation | Nem | Nem |
> | helyszínek/managedinstanceencryptionprotectoroperationresults | Nem | Nem |
> | helyszínek/managedinstancekeyazureasyncoperation | Nem | Nem |
> | helyszínek/managedinstancekeyoperationresults | Nem | Nem |
> | helyszínek/managedinstancelongtermretentionpolicyazureasyncoperation | Nem | Nem |
> | helyszínek/managedinstancelongtermretentionpolicyoperationresults | Nem | Nem |
> | helyszínek/managedinstanceoperationresults | Nem | Nem |
> | helyszínek/managedinstancetdecertazureasyncoperation | Nem | Nem |
> | helyszínek/managedinstancetdecertoperationresults | Nem | Nem |
> | helyszínek/managedserversecurityalertpoliciesazureasyncoperation | Nem | Nem |
> | helyszínek/managedserversecurityalertpoliciesoperationresults | Nem | Nem |
> | helyszínek/managedshorttermretentionpolicyazureasyncoperation | Nem | Nem |
> | helyszínek/managedshorttermretentionpolicyoperationresults | Nem | Nem |
> | helyszínek/notifyazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionoperationresults | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyazureasyncoperation | Nem | Nem |
> | helyszínek/privateendpointconnectionproxyoperationresults | Nem | Nem |
> | helyszínek/securityalertpoliciesazureasyncoperation | Nem | Nem |
> | helyszínek/securityalertpoliciesoperationresults | Nem | Nem |
> | helyszínek/serveradministratorazureasyncoperation | Nem | Nem |
> | helyszínek/serveradministratoroperationresults | Nem | Nem |
> | helyszínek/serverazureasyncoperation | Nem | Nem |
> | helyszínek/serverkeyazureasyncoperation | Nem | Nem |
> | helyszínek/serverkeyoperationresults | Nem | Nem |
> | helyszínek/serveroperationresults | Nem | Nem |
> | helyszínek/shorttermretentionpolicyazureasyncoperation | Nem | Nem |
> | helyszínek/shorttermretentionpolicyoperationresults | Nem | Nem |
> | helyszínek/syncagentoperationresults | Nem | Nem |
> | helyszínek/syncdatabaseids | Nem | Nem |
> | helyszínek/syncgroupoperationresults | Nem | Nem |
> | helyszínek/syncmemberoperationresults | Nem | Nem |
> | helyszínek/tdecertazureasyncoperation | Nem | Nem |
> | helyszínek/tdecertoperationresults | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | helyszínek/virtualclusterazureasyncoperation | Nem | Nem |
> | helyszínek/virtualclusteroperationresults | Nem | Nem |
> | helyszínek/virtualnetworkrulesazureasyncoperation | Nem | Nem |
> | helyszínek/virtualnetworkrulesoperationresults | Nem | Nem |
> | helyszínek/vulnerabilityassessmentscanazureasyncoperation | Nem | Nem |
> | helyszínek/vulnerabilityassessmentscanoperationresults | Nem | Nem |
> | managedinstances | Nem | Nem |
> | managedinstances/rendszergazdák | Nem | Nem |
> | managedinstances/adatbázisok | Nem | Nem |
> | managedinstances/adatbázisok/backuplongtermretentionpolicies | Nem | Nem |
> | managedinstances/adatbázisok/vulnerabilityassessments | Nem | Nem |
> | managedinstances / metricdefinitions | Nem | Nem |
> | managedinstances/mérőszámok | Nem | Nem |
> | managedinstances / recoverabledatabases | Nem | Nem |
> | managedinstances / tdecertificates | Nem | Nem |
> | managedinstances / vulnerabilityassessments | Nem | Nem |
> | műveletek | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | kiszolgálók/administratoroperationresults | Nem | Nem |
> | kiszolgálók/rendszergazdák | Nem | Nem |
> | kiszolgálók/tanácsadók | Nem | Nem |
> | kiszolgálók/aggregateddatabasemetrics | Nem | Nem |
> | kiszolgálók/auditingpolicies | Nem | Nem |
> | kiszolgálók/auditingsettings | Nem | Nem |
> | kiszolgálók/automatictuning | Nem | Nem |
> | kiszolgálók/communicationlinks | Nem | Nem |
> | kiszolgálók/connectionpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok | Igen | Igen |
> | kiszolgálók/adatbázisok/tanácsadók | Nem | Nem |
> | kiszolgálók/adatbázisok/auditingpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/auditingsettings | Nem | Nem |
> | kiszolgálók/adatbázisok/auditrecords | Nem | Nem |
> | kiszolgálók/adatbázisok/automatictuning | Nem | Nem |
> | kiszolgálók/adatbázisok/backuplongtermretentionpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/backupshorttermretentionpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/connectionpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/datamaskingpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/datamaskingpolicies/szabályok | Nem | Nem |
> | kiszolgálók/adatbázisok/bővítmények | Nem | Nem |
> | kiszolgálók/adatbázisok/geobackuppolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/metricdefinitions | Nem | Nem |
> | kiszolgálók/adatbázisok/mérőszámok | Nem | Nem |
> | kiszolgálók/adatbázisok/recommendedsensitivitylabels | Nem | Nem |
> | kiszolgálók/adatbázisok/securityalertpolicies | Nem | Nem |
> | kiszolgálók/adatbázisok/syncgroups | Nem | Nem |
> | kiszolgálók/adatbázisok/syncgroups/syncmembers | Nem | Nem |
> | kiszolgálók/adatbázisok/topqueries | Nem | Nem |
> | kiszolgálók/adatbázisok/topqueries/QueryText | Nem | Nem |
> | kiszolgálók/adatbázisok/transparentdataencryption | Nem | Nem |
> | kiszolgálók/adatbázisok/vulnerabilityassessment | Nem | Nem |
> | kiszolgálók/adatbázisok/vulnerabilityassessments | Nem | Nem |
> | kiszolgálók/adatbázisok/vulnerabilityassessmentscans | Nem | Nem |
> | kiszolgálók/adatbázisok/vulnerabilityassessmentsettings | Nem | Nem |
> | kiszolgálók/adatbázisok/workloadgroups | Nem | Nem |
> | kiszolgálók/databasesecuritypolicies | Nem | Nem |
> | kiszolgálók/disasterrecoveryconfiguration | Nem | Nem |
> | kiszolgálók/dnsaliases | Nem | Nem |
> | kiszolgálók/elasticpoolestimates | Nem | Nem |
> | kiszolgálók/elasticpools | Igen | Igen |
> | kiszolgálók/elasticpools/tanácsadók | Nem | Nem |
> | kiszolgálók/elasticpools/metricdefinitions | Nem | Nem |
> | kiszolgálók/elasticpools/mérőszámok | Nem | Nem |
> | kiszolgálók/encryptionprotector | Nem | Nem |
> | kiszolgálók/extendedauditingsettings | Nem | Nem |
> | kiszolgálók/failovergroups | Nem | Nem |
> | kiszolgálók/importálás | Nem | Nem |
> | kiszolgálók/importexportoperationresults | Nem | Nem |
> | kiszolgálók/jobaccounts | Igen | Igen |
> | kiszolgálók/jobagents | Igen | Igen |
> | kiszolgálók/jobagents/feladatok | Nem | Nem |
> | kiszolgálók/jobagents/feladatok/végrehajtások | Nem | Nem |
> | kiszolgálók/jobagents/feladatok/lépések | Nem | Nem |
> | kiszolgálók/kulcsok | Nem | Nem |
> | kiszolgálók/operationresults | Nem | Nem |
> | kiszolgálók/recommendedelasticpools | Nem | Nem |
> | kiszolgálók/recoverabledatabases | Nem | Nem |
> | kiszolgálók/restorabledroppeddatabases | Nem | Nem |
> | kiszolgálók/securityalertpolicies | Nem | Nem |
> | kiszolgálók/serviceobjectives | Nem | Nem |
> | kiszolgálók/syncagents | Nem | Nem |
> | kiszolgálók/tdecertificates | Nem | Nem |
> | kiszolgálók/használat | Nem | Nem |
> | kiszolgálók/virtualnetworkrules | Nem | Nem |
> | kiszolgálók/vulnerabilityassessments | Nem | Nem |
> | virtualclusters | Igen | Igen |

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoporthoz kell tartoznia. Ha egy SQL Servert helyez át, az összes adatbázisa is át lesz helyezve. Ez a viselkedés a Azure SQL Database és az Azure szinapszis Analytics adatbázisaira vonatkozik.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/availabilitygrouplisteneroperationresults | Nem | Nem |
> | helyszínek/operationtypes | Nem | Nem |
> | helyszínek/sqlvirtualmachinegroupoperationresults | Nem | Nem |
> | helyszínek/sqlvirtualmachineoperationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | sqlvirtualmachinegroups | Igen | Igen |
> | sqlvirtualmachinegroups / availabilitygrouplisteners | Nem | Nem |
> | sqlvirtualmachines | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/asyncoperations | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | műveletek | Nem | Nem |
> | storageaccounts | Igen | Igen |
> | storageaccounts/blobservices | Nem | Nem |
> | storageaccounts/fileservices | Nem | Nem |
> | storageaccounts/listaccountsas | Nem | Nem |
> | storageaccounts/listservicesas | Nem | Nem |
> | storageaccounts/queueservices | Nem | Nem |
> | storageaccounts/szolgáltatások | Nem | Nem |
> | storageaccounts/szolgáltatások/metricdefinitions | Nem | Nem |
> | storageaccounts/tableservices | Nem | Nem |
> | használat | Nem | Nem |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | gyorsítótárak | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/checknameavailability | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/munkafolyamatok | Nem | Nem |
> | műveletek | Nem | Nem |
> | storagesyncservices | Igen | Igen |
> | storagesyncservices / registeredservers | Nem | Nem |
> | storagesyncservices / syncgroups | Nem | Nem |
> | storagesyncservices / syncgroups / cloudendpoints | Nem | Nem |
> | storagesyncservices / syncgroups / serverendpoints | Nem | Nem |
> | storagesyncservices/munkafolyamatok | Nem | Nem |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kezelők | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/kvóták | Nem | Nem |
> | műveletek | Nem | Nem |
> | streamingjobs | Igen | Igen |

> [!IMPORTANT]
> Stream Analytics feladatok futási állapotban nem helyezhetők át.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | környezetben | Nem | Nem |
> | környezetek/eventsources | Nem | Nem |
> | esetben | Nem | Nem |
> | példányok/környezetek | Nem | Nem |
> | példányok/környezetek/eventsources | Nem | Nem |

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | mégse | Nem | Nem |
> | createsubscription | Nem | Nem |
> | engedélyezése | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | átnevezés | Nem | Nem |
> | subscriptiondefinitions | Nem | Nem |
> | subscriptionoperations | Nem | Nem |
> | előfizetések | Nem | Nem |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | operationresults | Nem | Nem |
> | műveletek | Nem | Nem |
> | operationsstatus | Nem | Nem |
> | services | Nem | Nem |
> | szolgáltatások/problemclassifications | Nem | Nem |
> | supporttickets | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft. szinapszis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Nem | Nem |
> | műveletek | Nem | Nem |
> | munkaterületek | Igen | Igen |
> | munkaterületek/bigdatapools | Igen | Igen |
> | munkaterületek/operationresults | Nem | Nem |
> | munkaterületek/operationstatuses | Nem | Nem |
> | munkaterületek/sqlpools | Igen | Igen |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | környezetben | Igen | Igen |
> | környezetek/accesspolicies | Nem | Nem |
> | környezetek/eventsources | Igen | Igen |
> | környezetek/referencedatasets | Igen | Igen |
> | műveletek | Nem | Nem |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárolja | Igen | Igen |
> | üzletek/accesspolicies | Nem | Nem |
> | üzletek/szolgáltatások | Nem | Nem |
> | üzletek/szolgáltatások/jogkivonatok | Nem | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nem | Nem |
> | imagetemplates / runoutputs | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | account | Nem | Nem |
> | fiók/bővítmény | Nem | Nem |
> | fiók/projekt | Nem | Nem |
> | checknameavailability | Nem | Nem |
> | műveletek | Nem | Nem |

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosításához tekintse meg [a számlázáshoz használt Azure-előfizetés módosítása](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)című témakört.

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | arczones | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | resourcepools | Nem | Nem |
> | vCenter | Nem | Nem |
> | virtualmachines | Nem | Nem |
> | virtualmachinetemplates | Nem | Nem |
> | virtualnetworks | Nem | Nem |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nem | Nem |
> | dedicatedcloudservices | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/elérhetőség | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/privateclouds | Nem | Nem |
> | Locations/privateclouds/resourcepools | Nem | Nem |
> | Locations/privateclouds/virtualmachinetemplates | Nem | Nem |
> | Locations/privateclouds/virtualnetworks | Nem | Nem |
> | helyszínek/használat | Nem | Nem |
> | műveletek | Nem | Nem |
> | virtualmachines | Nem | Nem |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | eszközök | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | vnfs | Nem | Nem |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | műveletek | Nem | Nem |
> | tervek | Nem | Nem |
> | registeredsubscriptions | Nem | Nem |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nem | Nem |
> | billingmeters | Nem | Nem |
> | tanúsítványok | Nem | Igen |
> | checknameavailability | Nem | Nem |
> | connectiongateways | Igen | Igen |
> | kapcsolatok | Igen | Igen |
> | customapis | Igen | Igen |
> | deletedsites | Nem | Nem |
> | deploymentlocations | Nem | Nem |
> | georegions | Nem | Nem |
> | hostingenvironments | Nem | Nem |
> | hostingenvironments / eventgridfilters | Nem | Nem |
> | hostingenvironments / multirolepools | Nem | Nem |
> | hostingenvironments / workerpools | Nem | Nem |
> | ishostingenvironmentnameavailable | Nem | Nem |
> | ishostnameavailable | Nem | Nem |
> | isusernameavailable | Nem | Nem |
> | kubeenvironments | Igen | Igen |
> | listsitesassignedtohostname | Nem | Nem |
> | helyek | Nem | Nem |
> | helyszínek/apioperations | Nem | Nem |
> | helyszínek/connectiongatewayinstallations | Nem | Nem |
> | helyszínek/deletedsites | Nem | Nem |
> | helyszínek/deletevirtualnetworkorsubnets | Nem | Nem |
> | helyszínek/extractapidefinitionfromwsdl | Nem | Nem |
> | helyszínek/getnetworkpolicies | Nem | Nem |
> | helyszínek/listwsdlinterfaces | Nem | Nem |
> | helyszínek/król | Nem | Nem |
> | helyszínek/operationresults | Nem | Nem |
> | helyszínek/műveletek | Nem | Nem |
> | helyszínek/futtatókörnyezetek | Nem | Nem |
> | műveletek | Nem | Nem |
> | publishingusers | Nem | Nem |
> | javaslatok | Nem | Nem |
> | resourcehealthmetadata | Nem | Nem |
> | Runtimes | Nem | Nem |
> | kiszolgálófarmok | Igen | Igen |
> | kiszolgálófarmok/eventgridfilters | Nem | Nem |
> | helyek | Igen | Igen |
> | helyek/eventgridfilters | Nem | Nem |
> | helyek/hostnamebindings | Nem | Nem |
> | helyek/networkconfig | Nem | Nem |
> | helyek/premieraddons | Igen | Igen |
> | helyek/bővítőhelyek | Igen | Igen |
> | helyek/bővítőhelyek/eventgridfilters | Nem | Nem |
> | helyek/bővítőhelyek/hostnamebindings | Nem | Nem |
> | helyek/bővítőhelyek/networkconfig | Nem | Nem |
> | sourcecontrols | Nem | Nem |
> | staticsites | Nem | Nem |
> | érvényesít | Nem | Nem |
> | verifyhostingenvironmentvnet | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | multipleactivationkeys | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | helyek | Nem | Nem |
> | helyszínek/operationstatuses | Nem | Nem |
> | műveletek | Nem | Nem |
> | számítási feladatok | Nem | Nem |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összetevők | Nem | Nem |
> | componentssummary | Nem | Nem |
> | monitorinstances | Nem | Nem |
> | monitorinstancessummary | Nem | Nem |
> | figyeli | Nem | Nem |
> | notificationsettings | Nem | Nem |
> | műveletek | Nem | Nem |

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>Következő lépések
Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket tartalmazó fájlokat, töltse le [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
