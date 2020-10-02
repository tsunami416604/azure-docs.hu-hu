---
title: Művelet támogatásának áthelyezése erőforrástípus szerint
description: Felsorolja az új erőforráscsoporthoz vagy előfizetésbe áthelyezhető Azure-erőforrástípusok listáját.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 675f7bb11ed98dd17e8f4ee4a0197d05128af851
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91627108"
---
# <a name="move-operation-support-for-resources"></a>Művelet-támogatás áthelyezése az erőforrásokhoz

Ez a cikk azt mutatja be, hogy az Azure-erőforrástípus támogatja-e az áthelyezési műveletet. Emellett az erőforrások áthelyezésekor megfontolandó speciális feltételekkel kapcsolatos információkat is tartalmaz.

> [!IMPORTANT]
> A legtöbb esetben a gyermek erőforrás nem helyezhető át a fölérendelt erőforrástól függetlenül. A gyermek erőforrások típusa a következő formátumú: `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Például a a `Microsoft.ServiceBus/namespaces/queues` alárendelt erőforrása `Microsoft.ServiceBus/namespaces` . Ha áthelyezi a fölérendelt erőforrást, a rendszer automatikusan áthelyezi a gyermek erőforrást. Ha nem lát alárendelt erőforrást ebben a cikkben, azt feltételezheti, hogy a szülő erőforrással lett áthelyezve. Ha a fölérendelt erőforrás nem támogatja az áthelyezést, a gyermek erőforrás nem helyezhető át.

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

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem |
> | privatelinkforazuread | Igen | Igen |
> | bérlők | Igen | Igen |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
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
> | smartdetectoralertrules | Igen | Igen |
> | smartgroups | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!IMPORTANT]
> Nem helyezhető át olyan API Management szolgáltatás, amely a felhasználási SKU-ra van beállítva.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Nem | Nem |
> | szolgáltatás | Igen | Igen |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | configurationstores | Igen | Igen |
> | configurationstores / eventgridfilters | Nem | Nem |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Spring | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | apiapps | Nem | Nem |
> | appidentities | Nem | Nem |
> | átjárók | Nem | Nem |

## <a name="microsoftattestation"></a>Microsoft. igazolás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Igen | Igen |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Nem | Nem |
> | dataaliases | Nem | Nem |
> | denyassignments | Nem | Nem |
> | elevateaccess | Nem | Nem |
> | findorphanroleassignments | Nem | Nem |
> | zárak | Nem | Nem |
> | engedélyek | Nem | Nem |
> | policyassignments | Nem | Nem |
> | policydefinitions | Nem | Nem |
> | policysetdefinitions | Nem | Nem |
> | privatelinkassociations | Nem | Nem |
> | resourcemanagementprivatelinks | Nem | Nem |
> | RoleAssignments | Nem | Nem |
> | roleassignmentsusagemetrics | Nem | Nem |
> | roledefinitions | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> A runbookok ugyanabban az erőforráscsoporthoz kell tartoznia, mint az Automation-fióknak.
>
> További információ: [Azure Automation-fiók áthelyezése másik előfizetésre](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Igen | Igen |
> | automationaccounts/konfigurációk | Igen | Igen |
> | automationaccounts/runbookok | Igen | Igen |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | privateclouds | Igen | Igen |

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Igen | Igen |
> | b2ctenants | Nem | Nem |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datacontrollers | Nem | Nem |
> | hybriddatamanagers | Nem | Nem |
> | postgresinstances | Nem | Nem |
> | sqlinstances | Nem | Nem |
> | sqlmanagedinstances | Nem | Nem |
> | sqlserverinstances | Nem | Nem |
> | sqlserverregistrations | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Nem | Nem |
> | regisztrációk | Igen | Igen |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Igen | Igen |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Nem | Nem |
> | billingperiods | Nem | Nem |
> | billingpermissions | Nem | Nem |
> | billingproperty | Nem | Nem |
> | billingroleassignments | Nem | Nem |
> | billingroledefinitions | Nem | Nem |
> | részlegek | Nem | Nem |
> | enrollmentaccounts | Nem | Nem |
> | számlák | Nem | Nem |
> | transzferek | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | mapapis | Nem | Nem |

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
> | Watchers | Nem | Nem |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tokenservices | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nem | Nem |
> | tervrajzok | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft. cache

> [!IMPORTANT]
> Ha az Azure cache for Redis-példány virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: [hálózati áthelyezési korlátozások](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Redis | Igen | Igen |
> | redisenterprise | Nem | Nem |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Nem | Nem |
> | calculateexchange | Nem | Nem |
> | calculateprice | Nem | Nem |
> | calculatepurchaseprice | Nem | Nem |
> | katalógusok | Nem | Nem |
> | commercialreservationorders | Nem | Nem |
> | Exchange | Nem | Nem |
> | reservationorders | Nem | Nem |
> | foglalások | Nem | Nem |
> | resources | Nem | Nem |
> | validatereservationorder | Nem | Nem |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Nem | Nem |
> | cdnwebapplicationfirewallpolicies | Igen | Igen |
> | edgenodes | Nem | Nem |
> | profilok | Igen | Igen |
> | profilok/végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tanúsítványrendelések | Igen | Igen |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | képességek | Nem | Nem |
> | tartománynevek | Igen | Nem |
> | kvóták | Nem | Nem |
> | resourcetypes | Nem | Nem |
> | validatesubscriptionmoveavailability | Nem | Nem |
> | virtualmachines | Igen | Igen |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Nem | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | képességek | Nem | Nem |
> | expressroutecrossconnections | Nem | Nem |
> | expressroutecrossconnections/társak | Nem | Nem |
> | gatewaysupporteddevices | Nem | Nem |
> | networksecuritygroups | Nem | Nem |
> | kvóták | Nem | Nem |
> | reservedips | Nem | Nem |
> | virtualnetworks | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | lemezek | Nem | Nem |
> | images | Nem | Nem |
> | osimages | Nem | Nem |
> | osplatformimages | Nem | Nem |
> | publicimages | Nem | Nem |
> | kvóták | Nem | Nem |
> | storageaccounts | Igen | Nem |
> | lemezképet | Nem | Nem |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletek | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | ratecard | Nem | Nem |
> | usageaggregates | Nem | Nem |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Lásd: [Virtual Machines áthelyezési útmutató](./move-limitations/virtual-machines-move-limitations.md).

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
> | virtualmachinescalesets | Igen | Igen |

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
> | serviceassociationlinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kibocsátásiegység | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/agentpools | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | containerservices | Nem | Nem |
> | managedclusters | Nem | Nem |
> | openshiftmanagedclusters | Nem | Nem |

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
> | forecast | Nem | Nem |
> | lekérdezés | Nem | Nem |
> | Regisztráció | Nem | Nem |
> | reportconfigs | Nem | Nem |
> | jelentések | Nem | Nem |
> | beállítások | Nem | Nem |
> | showbackrules | Nem | Nem |
> | kilátással | Nem | Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Hubs | Nem | Nem |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kérelmek | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | szövetségek | Nem | Nem |
> | resourceproviders | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availableskus | Nem | Nem |
> | databoxedgedevices | Igen | Igen |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | katalógusok | Igen | Igen |
> | datacatalogs | Nem | Nem |

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
> | datafactories | Igen | Igen |
> | datafactoryschema | Nem | Nem |
> | előállítók | Igen | Igen |

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

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Nem | Nem |
> | szolgáltatások/projektek | Nem | Nem |
> | bővítőhely | Nem | Nem |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kiszolgálók | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Igen | Igen |
> | kiszolgálók | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Igen | Igen |
> | servergroups | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | serversv2 | Igen | Igen |
> | singleservers | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | artifactsources | Igen | Igen |
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
> | hostpools | Igen | Igen |
> | munkaterületek | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nem | Nem |
> | elasticpools / iothubtenants | Nem | Nem |
> | iothubs | Igen | Igen |
> | provisioningservices | Igen | Igen |

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

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | labcenters | Nem | Nem |
> | Labs | Igen | Nem |
> | Labs/környezetek | Igen | Igen |
> | Labor/servicerunners | Igen | Igen |
> | Labor/virtualmachines | Igen | Nem |
> | menetrend | Igen | Igen |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Nem | Nem |
> | databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartományok | Igen | Igen |
> | generatessorequest | Nem | Nem |
> | topleveldomains | Nem | Nem |
> | validatedomainregistrationinformation | Nem | Nem |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartományok | Igen | Igen |
> | eventsubscriptions | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. |
> | extensiontopics | Nem | Nem |
> | partnernamespaces | Igen | Igen |
> | partnerregistrations | Nem | Nem |
> | partnertopics | Igen | Igen |
> | systemtopics | Igen | Igen |
> | témakörök | Igen | Igen |
> | topictypes | Nem | Nem |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |
> | névterek | Igen | Igen |
> | SKU | Nem | Nem |

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Nem | Nem |

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
> | szoftver | Nem | Nem |
> | softwareupdateprofile | Nem | Nem |
> | softwareupdates | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nem | Nem |
> | sapmonitors | Igen | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Nem | Nem |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> A HDInsight-fürtöket új előfizetésre vagy erőforráscsoporthoz helyezheti át. Azonban a HDInsight-fürthöz társított hálózati erőforrások (például a virtuális hálózat, a hálózati adapter vagy a terheléselosztó) nem helyezhetők át az előfizetésekben. Emellett a fürthöz tartozó virtuális géphez csatlakoztatott hálózati adapterek nem helyezhetők át egy új erőforráscsoporthoz.
>
> Amikor új előfizetésre helyez át egy HDInsight-fürtöt, először helyezze át a többi erőforrást (például a Storage-fiókot). Ezután saját maga helyezheti át a HDInsight-fürtöt.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | gépek | Igen | Igen |
> | gépek/bővítmények | Igen | Igen |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datamanagers | Igen | Igen |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | eszközök | Nem | Nem |
> | vnfs | Nem | Nem |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összetevők | Nem | Nem |
> | networkscopes | Nem | Nem |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits)

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | actiongroups | Igen | Igen |
> | activitylogalerts | Nem | Nem |
> | alertrules | Igen | Igen |
> | autoscalesettings | Igen | Igen |
> | alapkonfiguráció | Nem | Nem |
> | összetevők | Igen | Igen |
> | datacollectionrules | Nem | Nem |
> | diagnosticsettings | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem |
> | eventcategories | Nem | Nem |
> | eventtypes | Nem | Nem |
> | extendeddiagnosticsettings | Nem | Nem |
> | guestdiagnosticsettings | Nem | Nem |
> | listmigrationdate | Nem | Nem |
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
> | privatelinkscopes | Nem | Nem |
> | rollbacktolegacypricingmodel | Nem | Nem |
> | scheduledqueryrules | Igen | Igen |
> | topology | Nem | Nem |
> | transactions | Nem | Nem |
> | vminsightsonboardingstatuses | Nem | Nem |
> | webteszteket | Igen | Igen |
> | webtesztek/gettestresultfile | Nem | Nem |
> | munkafüzetek | Igen | Igen |
> | workbooktemplates | Igen | Igen |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | apptemplates | Nem | Nem |
> | iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!IMPORTANT]
> A lemezes titkosításhoz használt kulcstartók nem helyezhetők át ugyanabba az előfizetésbe vagy előfizetésbe tartozó erőforráscsoporthoz.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Nem | Nem |
> | hsmpools | Nem | Nem |
> | managedhsms | Nem | Nem |
> | boltívek | Igen | Igen |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Igen | Igen |
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

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nem | Nem |
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
> | munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Nem | Nem |
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
> | teamaccounts | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | configurationassignments | Nem | Nem |
> | maintenanceconfigurations | Igen | Igen |
> | frissítések | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | identitások | Nem | Nem |
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
> | registrationassignments | Nem | Nem |
> | registrationdefinitions | Nem | Nem |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | getentities | Nem | Nem |
> | managementgroups | Nem | Nem |
> | managementgroups/beállítások | Nem | Nem |
> | resources | Nem | Nem |
> | starttenantbackfill | Nem | Nem |
> | tenantbackfillstatus | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | fiókok/privateatlases | Igen | Igen |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kínál | Nem | Nem |
> | offertypes | Nem | Nem |
> | privategalleryitems | Nem | Nem |
> | privatestoreclient | Nem | Nem |
> | privatestores | Nem | Nem |
> | termékek | Nem | Nem |
> | közzétevők | Nem | Nem |
> | Regisztráció | Nem | Nem |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nem | Nem |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | megállapodások | Nem | Nem |
> | offertypes | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Mediaservices | Igen | Igen |
> | Mediaservices/liveevents | Igen | Igen |
> | Mediaservices/streamingendpoints | Igen | Igen |

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
> | migrateprojects | Nem | Nem |
> | movecollections | Nem | Nem |
> | projektek | Nem | Nem |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Nem | Nem |
> | objectunderstandingaccounts | Nem | Nem |
> | remoterenderingaccounts | Igen | Igen |
> | spatialanchorsaccounts | Igen | Igen |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nem | Nem |
> | netappaccounts / capacitypools | Nem | Nem |
> | netappaccounts/capacitypools/kötetek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Lásd: [hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nem | Nem |
> | applicationgatewaywebapplicationfirewallpolicies | Nem | Nem |
> | applicationsecuritygroups | Igen | Igen |
> | azurefirewalls | Nem | Nem |
> | bastionhosts | Nem | Nem |
> | bgpservicecommunities | Nem | Nem |
> | kapcsolatok | Igen | Igen |
> | ddoscustompolicies | Igen | Igen |
> | ddosprotectionplans | Nem | Nem |
> | dnszones | Igen | Igen |
> | expressroutecircuits | Nem | Nem |
> | expressroutegateways | Nem | Nem |
> | expressrouteserviceproviders | Nem | Nem |
> | firewallpolicies | Igen | Igen |
> | frontdoors | Nem | Nem |
> | ipallocations | Igen | Igen |
> | ipgroups | Igen | Igen |
> | loadbalancers | Igen – alapszintű SKU<br> Igen – szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | localnetworkgateways | Igen | Igen |
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
> | p2svpngateways | Nem | Nem |
> | privatednszones | Igen | Igen |
> | privatednszones / virtualnetworklinks | Igen | Igen |
> | privatednszonesinternal | Nem | Nem |
> | privateendpointredirectmaps | Nem | Nem |
> | privateendpoints | Igen | Igen |
> | privatelinkservices | Nem | Nem |
> | nyilvános IP | Igen – alapszintű SKU<br>Igen – szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
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

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |
> | névterek/notificationhubs | Igen | Igen |

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
> | serversites | Nem | Nem |
> | vmwaresites | Nem | Nem |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits).
>
> A csatolt Automation-fiókkal rendelkező munkaterületek nem helyezhetők át. Az áthelyezési művelet megkezdése előtt ne felejtse el összekapcsolni az Automation-fiókokat.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | deletedworkspaces | Nem | Nem |
> | linktargets | Nem | Nem |
> | storageinsightconfigs | Nem | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nem | Nem |
> | managementconfigurations | Igen | Igen |
> | megoldások | Igen | Igen |
> | kilátással | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Nem | Nem |
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
> | usersettings | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kapacitások | Igen | Igen |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Nem | Nem |
> | providerregistrations | Nem | Nem |
> | kibocsátások | Nem | Nem |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Nem | Nem |
> | boltívek | Igen | Igen |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Nem | Nem |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
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
> | childresources | Nem | Nem |
> | emergingissues | Nem | Nem |
> | események | Nem | Nem |
> | metaadatok | Nem | Nem |
> | értesítések | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | központi telepítések | Nem | Nem |
> | deploymentscripts | Nem | Nem |
> | deploymentscripts/naplók | Nem | Nem |
> | linkek | Nem | Nem |
> | szolgáltatók | Nem | Nem |
> | resourcegroups | Nem | Nem |
> | resources | Nem | Nem |
> | előfizetések | Nem | Nem |
> | tags | Nem | Nem |
> | templatespecs | Nem | Nem |
> | templatespecs/verziók | Nem | Nem |
> | bérlők | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Nem |
> | saasresources | Nem | Nem |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!IMPORTANT]
> Egy műveletben nem helyezhető át több keresési erőforrás különböző régiókban. Ehelyett külön műveletekben helyezze át őket.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Nem | Nem |
> | searchservices | Igen | Igen |

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
> | szervezetek | Nem | Nem |
> | entityqueries | Nem | Nem |
> | incidensek | Nem | Nem |
> | officeconsents | Nem | Nem |
> | beállítások | Nem | Nem |
> | threatintelligence | Nem | Nem |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | consoleservices | Nem | Nem |

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
> | névterek | Igen | Igen |
> | premiummessagingregions | Nem | Nem |
> | SKU | Nem | Nem |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Nem | Nem |
> | fürtök | Igen | Igen |
> | containergroups | Nem | Nem |
> | containergroupsets | Nem | Nem |
> | edgeclusters | Nem | Nem |
> | managedclusters | Nem | Nem |
> | hálózatok | Nem | Nem |
> | secretstores | Nem | Nem |
> | volumes | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Igen |
> | containergroups | Nem | Nem |
> | átjárók | Igen | Igen |
> | hálózatok | Igen | Igen |
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
> | signalr | Igen | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nem | Nem |
> | alkalmazások | Nem | Nem |
> | jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoporthoz kell tartoznia. Ha egy SQL Servert helyez át, az összes adatbázisa is át lesz helyezve. Ez a viselkedés a Azure SQL Database és az Azure szinapszis Analytics adatbázisaira vonatkozik.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | instancepools | Nem | Nem |
> | helyek | Igen | Igen |
> | managedinstances | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | kiszolgálók/adatbázisok | Igen | Igen |
> | kiszolgálók/adatbázisok/backuplongtermretentionpolicies | Igen | Igen |
> | kiszolgálók/elasticpools | Igen | Igen |
> | kiszolgálók/jobaccounts | Igen | Igen |
> | kiszolgálók/jobagents | Igen | Igen |
> | virtualclusters | Igen | Igen |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Igen | Igen |
> | sqlvirtualmachines | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Igen | Igen |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | gyorsítótárak | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Igen | Igen |

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

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Stream Analytics feladatok futási állapotban nem helyezhetők át.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | streamingjobs | Igen | Igen |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | környezetben | Nem | Nem |
> | esetben | Nem | Nem |

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | előfizetések | Nem | Nem |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Nem | Nem |
> | supporttickets | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft. szinapszis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Igen | Igen |
> | munkaterületek/bigdatapools | Igen | Igen |
> | munkaterületek/sqlpools | Igen | Igen |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | környezetben | Igen | Igen |
> | környezetek/eventsources | Igen | Igen |
> | környezetek/referencedatasets | Igen | Igen |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárolja | Igen | Igen |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nem | Nem |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosításához tekintse meg [a számlázáshoz használt Azure-előfizetés módosítása](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)című témakört.

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | account | Nem | Nem |
> | fiók/bővítmény | Nem | Nem |
> | fiók/projekt | Nem | Nem |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | arczones | Nem | Nem |
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
> | virtualmachines | Nem | Nem |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | eszközök | Nem | Nem |
> | vnfs | Nem | Nem |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | tervek | Nem | Nem |
> | registeredsubscriptions | Nem | Nem |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availablestacks | Nem | Nem |
> | billingmeters | Nem | Nem |
> | tanúsítványok | Nem | Igen |
> | connectiongateways | Igen | Igen |
> | kapcsolatok | Igen | Igen |
> | customapis | Igen | Igen |
> | deletedsites | Nem | Nem |
> | deploymentlocations | Nem | Nem |
> | georegions | Nem | Nem |
> | hostingenvironments | Nem | Nem |
> | kubeenvironments | Igen | Igen |
> | publishingusers | Nem | Nem |
> | javaslatok | Nem | Nem |
> | resourcehealthmetadata | Nem | Nem |
> | Runtimes | Nem | Nem |
> | kiszolgálófarmok | Igen | Igen |
> | kiszolgálófarmok/eventgridfilters | Nem | Nem |
> | helyek | Igen | Igen |
> | helyek/premieraddons | Igen | Igen |
> | helyek/bővítőhelyek | Igen | Igen |
> | sourcecontrols | Nem | Nem |
> | staticsites | Nem | Nem |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Nem | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nem | Nem |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
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

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>További lépések

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket tartalmazó fájlokat, töltse le [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
