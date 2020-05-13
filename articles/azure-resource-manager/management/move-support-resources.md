---
title: Művelet támogatásának áthelyezése erőforrástípus szerint
description: Felsorolja az új erőforráscsoporthoz vagy előfizetésbe áthelyezhető Azure-erőforrástípusok listáját.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 81d545066ea6bcc1d3e2eecd884671324155d796
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124686"
---
# <a name="move-operation-support-for-resources"></a>Erőforrás-áthelyezési műveletek támogatása
Ez a cikk azt mutatja be, hogy az Azure-erőforrástípus támogatja-e az áthelyezési műveletet. Emellett az erőforrások áthelyezésekor megfontolandó speciális feltételekkel kapcsolatos információkat is tartalmaz.

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft. HRE](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognition](#microsoftcognition)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. felhasználás](#microsoftconsumption)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. Tárolószolgáltatás](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
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
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. kísérletezés](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. bepillantások](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kulcstartó](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
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
> - [Microsoft. SqlVM](#microsoftsqlvm)
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
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
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
> | bérlők | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | konfigurációk | Nem | Nem |
> | javaslatok | Nem | Nem |
> | fóliakondenzát | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | actionrules | Igen | Igen |
> | riasztások | Nem | Nem |
> | alertssummary | Nem | Nem |
> | smartdetectoralertrules | Igen | Igen |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | szolgáltatás | Igen | Igen |

> [!IMPORTANT]
> Nem helyezhető át olyan API Management szolgáltatás, amely a felhasználási SKU-ra van beállítva.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | configurationstores | Igen | Igen |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Spring | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | apiapps | Nem | Nem |
> | appidentities | Nem | Nem |
> | átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | CheckAccess | Nem | Nem |
> | denyassignments | Nem | Nem |
> | findorphanroleassignments | Nem | Nem |
> | zárak | Nem | Nem |
> | engedélyek | Nem | Nem |
> | policyassignments | Nem | Nem |
> | policydefinitions | Nem | Nem |
> | policysetdefinitions | Nem | Nem |
> | RoleAssignments | Nem | Nem |
> | roleassignmentsusagemetrics | Nem | Nem |
> | roledefinitions | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Igen | Igen |
> | automationaccounts/konfigurációk | Igen | Igen |
> | automationaccounts/runbookok | Igen | Igen |

> [!IMPORTANT]
> A runbookok ugyanabban az erőforráscsoporthoz kell tartoznia, mint az Automation-fióknak.

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Igen | Igen |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nem | Nem |
> | postgresinstances | Nem | Nem |
> | sqlinstances | Nem | Nem |
> | sqlserverregistrations | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | regisztrációk | Igen | Igen |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft. BatchAI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | fileservers | Nem | Nem |
> | feladatok | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | billingperiods | Nem | Nem |
> | billingpermissions | Nem | Nem |
> | billingroleassignments | Nem | Nem |
> | billingroledefinitions | Nem | Nem |
> | createbillingroleassignment | Nem | Nem |

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

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Redis | Igen | Igen |

> [!IMPORTANT]
> Ha az Azure cache for Redis-példány virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: [hálózati áthelyezési korlátozások](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Igen | Igen |
> | profilok | Igen | Igen |
> | profilok/végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tanúsítványrendelések | Igen | Igen |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartománynevek | Igen | Nem |
> | virtualmachines | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nem | Nem |
> | reservedips | Nem | Nem |
> | virtualnetworks | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftcognition"></a>Microsoft. Cognition

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Igen | Igen |
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
> | sharedvmextensions | Nem | Nem |
> | sharedvmimages | Nem | Nem |
> | sharedvmimages/verziók | Nem | Nem |
> | pillanatképek | Igen | Igen |
> | sshpublickeys | Nem | Nem |
> | virtualmachines | Igen | Igen |
> | virtualmachines/bővítmények | Igen | Igen |
> | virtualmachinescalesets | Igen | Igen |

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
> | operationstatus | Nem | Nem |
> | pricesheets | Nem | Nem |
> | termékek | Nem | Nem |
> | reservationdetails | Nem | Nem |
> | reservationrecommendations | Nem | Nem |
> | reservationsummaries | Nem | Nem |
> | reservationtransactions | Nem | Nem |
> | címkét | Nem | Nem |
> | bérlők | Nem | Nem |
> | feltételek | Nem | Nem |
> | UsageDetails | Nem | Nem |

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | containergroups | Nem | Nem |

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
> | kibocsátásiegység-forgalmi jegyzékek/agentpools | Nem | Nem |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/taskruns | Igen | Igen |
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
> | költségvetése | Nem | Nem |
> | összekötők | Igen | Igen |
> | Méretek | Nem | Nem |
> | export | Nem | Nem |
> | externalsubscriptions | Nem | Nem |
> | forecast | Nem | Nem |
> | lekérdezés | Nem | Nem |
> | reportconfigs | Nem | Nem |
> | jelentések | Nem | Nem |
> | showbackrules | Nem | Nem |
> | kilátással | Nem | Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Hubs | Nem | Nem |

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
> | databoxedgedevices | Nem | Nem |

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
> | kiszolgálók | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
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
> | applicationgroups | Nem | Nem |
> | hostpools | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftdevices"></a>Microsoft. Devices

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

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartományok | Igen | Igen |

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
> | eventSubscriptions | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. |
> | eventsubscriptions | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. |
> | extensiontopics | Nem | Nem |
> | partnernamespaces | Igen | Igen |
> | partnerregistrations | Nem | Nem |
> | partnertopics | Igen | Igen |
> | systemtopics | Igen | Igen |
> | témakörök | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |
> | névterek | Igen | Igen |

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

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |

> [!IMPORTANT]
> A HDInsight-fürtöket új előfizetésre vagy erőforráscsoporthoz helyezheti át. Azonban a HDInsight-fürthöz társított hálózati erőforrások (például a virtuális hálózat, a hálózati adapter vagy a terheléselosztó) nem helyezhetők át az előfizetésekben. Emellett a fürthöz tartozó virtuális géphez csatlakoztatott hálózati adapterek nem helyezhetők át egy új erőforráscsoporthoz.
>
> Amikor új előfizetésre helyez át egy HDInsight-fürtöt, először helyezze át a többi erőforrást (például a Storage-fiókot). Ezután saját maga helyezheti át a HDInsight-fürtöt.

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
> | gépek/bővítmények | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datamanagers | Igen | Igen |

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
> | datacollectionrules | Nem | Nem |
> | diagnosticsettings | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem |
> | eventtypes | Nem | Nem |
> | extendeddiagnosticsettings | Nem | Nem |
> | guestdiagnosticsettings | Nem | Nem |
> | logdefinitions | Nem | Nem |
> | naplók | Nem | Nem |
> | metricalerts | Nem | Nem |
> | metricbaselines | Nem | Nem |
> | metricdefinitions | Nem | Nem |
> | metricnamespaces | Nem | Nem |
> | metrics | Nem | Nem |
> | myworkbooks | Nem | Nem |
> | notificationgroups | Nem | Nem |
> | privatelinkscopes | Igen | Igen |
> | scheduledqueryrules | Igen | Igen |
> | topology | Nem | Nem |
> | transactions | Nem | Nem |
> | vminsightsonboardingstatuses | Nem | Nem |
> | webteszteket | Igen | Igen |
> | munkafüzetek | Igen | Igen |
> | workbooktemplates | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Igen | Igen |
> | Graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nem | Nem |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> A lemezes titkosításhoz használt kulcstartók nem helyezhetők át ugyanabba az előfizetésbe vagy előfizetésbe tartozó erőforráscsoporthoz.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nem | Nem |

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

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |
> | munkaterületek/számítások | Nem | Nem |

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
> | registrationassignments | Nem | Nem |
> | registrationdefinitions | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | fiókok/privateatlases | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nem | Nem |

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
> | assessmentprojects | Igen | Igen |
> | migrateprojects | Igen | Igen |
> | movecollections | Nem | Nem |
> | projektek | Nem | Nem |

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

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Nem | Nem |
> | applicationgatewaywebapplicationfirewallpolicies | Nem | Nem |
> | applicationsecuritygroups | Igen | Igen |
> | azurefirewalls | Igen | Igen |
> | bastionhosts | Nem | Nem |
> | kapcsolatok | Igen | Igen |
> | ddoscustompolicies | Igen | Igen |
> | ddosprotectionplans | Nem | Nem |
> | dnszones | Igen | Igen |
> | expressroutecircuits | Nem | Nem |
> | expressroutegateways | Nem | Nem |
> | firewallpolicies | Igen | Igen |
> | frontdoors | Nem | Nem |
> | frontdoorwebapplicationfirewallpolicies | Nem | Nem |
> | ipgroups | Igen | Igen |
> | loadbalancers | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | localnetworkgateways | Igen | Igen |
> | natgateways | Igen | Igen |
> | networkexperimentprofiles | Igen | Igen |
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
> | privateendpointredirectmaps | Nem | Nem |
> | privateendpoints | Igen | Igen |
> | privatelinkservices | Nem | Nem |
> | nyilvános IP | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | publicipprefixes | Igen | Igen |
> | routefilters | Nem | Nem |
> | routetables | Igen | Igen |
> | serviceendpointpolicies | Igen | Igen |
> | trafficmanagerprofiles | Igen | Igen |
> | virtualhubs | Nem | Nem |
> | virtualnetworkgateways | Igen | Igen |
> | virtualnetworks | Igen | Igen |
> | virtualnetworktaps | Nem | Nem |
> | virtualrouters | Igen | Igen |
> | virtualwans | Nem | Nem |
> | vpngateways (virtuális WAN) | Nem | Nem |
> | vpnserverconfigurations | Nem | Nem |
> | vpnsites (virtuális WAN) | Nem | Nem |
> | webapplicationfirewallpolicies | Igen | Igen |

> [!IMPORTANT]
> Lásd: [hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md).

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

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | storageinsightconfigs | Nem | Nem |
> | munkaterületek | Igen | Igen |

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
> | megoldások | Igen | Igen |
> | kilátással | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | társviszonyok | Igen | Igen |
> | peeringservices | Nem | Nem |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

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
> | irányítópultok | Igen | Igen |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | rootresources | Nem | Nem |

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

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kibocsátások | Nem | Nem |

## <a name="microsoftquantum"></a>Microsoft. Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nem | Nem |
> | replicationeligibilityresults | Nem | Nem |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

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

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nem | Nem |
> | childavailabilitystatuses | Nem | Nem |
> | childresources | Nem | Nem |
> | események | Nem | Nem |
> | értesítések | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Nem | Nem |
> | linkek | Nem | Nem |
> | címkét | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Nem |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | searchservices | Igen | Igen |

> [!IMPORTANT]
> Egy műveletben nem helyezhető át több keresési erőforrás különböző régiókban. Ehelyett külön műveletekben helyezze át őket.

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nem | Nem |
> | advancedthreatprotectionsettings | Nem | Nem |
> | assessmentmetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | automatizálások szabványának létrehozásában | Igen | Igen |
> | complianceresults | Nem | Nem |
> | felelésről | Nem | Nem |
> | datacollectionagents | Nem | Nem |
> | devicesecuritygroups | Nem | Nem |
> | informationprotectionpolicies | Nem | Nem |
> | iotsecuritysolutions | Igen | Igen |
> | servervulnerabilityassessments | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összesítések | Nem | Nem |
> | alertrules | Nem | Nem |
> | alertruletemplates | Nem | Nem |
> | könyvjelzők | Nem | Nem |
> | esetekben | Nem | Nem |
> | dataconnectors | Nem | Nem |
> | dataconnectorscheckrequirements | Nem | Nem |
> | szervezetek | Nem | Nem |
> | entityqueries | Nem | Nem |
> | események | Nem | Nem |
> | officeconsents | Nem | Nem |
> | beállítások | Nem | Nem |

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

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | instancepools | Nem | Nem |
> | helyek | Igen | Igen |
> | managedinstances | Nem | Nem |
> | managedinstances/adatbázisok | Nem | Nem |
> | kiszolgálók | Igen | Igen |
> | kiszolgálók/adatbázisok | Igen | Igen |
> | kiszolgálók/elasticpools | Igen | Igen |
> | kiszolgálók/jobaccounts | Igen | Igen |
> | kiszolgálók/jobagents | Igen | Igen |
> | virtualclusters | Igen | Igen |

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoporthoz kell tartoznia. Ha egy SQL Servert helyez át, az összes adatbázisa is át lesz helyezve. Ez a viselkedés Azure SQL Database és Azure SQL Data Warehouse adatbázisokra vonatkozik.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Igen | Igen |
> | sqlvirtualmachines | Igen | Igen |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | dwvm | Nem | Nem |

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

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
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
> | createsubscription | Nem | Nem |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | supporttickets | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft. szinapszis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |
> | munkaterületek/bigdatapools | Nem | Nem |
> | munkaterületek/sqlpools | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nem | Nem |
> | resources | Nem | Nem |

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

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | account | Nem | Nem |
> | fiók/bővítmény | Nem | Nem |
> | fiók/projekt | Nem | Nem |

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosításához tekintse meg [a számlázáshoz használt Azure-előfizetés módosítása](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)című témakört.

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

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tanúsítványok | Nem | Igen |
> | connectiongateways | Igen | Igen |
> | kapcsolatok | Igen | Igen |
> | customapis | Igen | Igen |
> | hostingenvironments | Nem | Nem |
> | kubeenvironments | Igen | Igen |
> | kiszolgálófarmok | Igen | Igen |
> | helyek | Igen | Igen |
> | helyek/premieraddons | Igen | Igen |
> | helyek/bővítőhelyek | Igen | Igen |
> | staticsites | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

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

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összetevők | Nem | Nem |
> | monitorinstances | Nem | Nem |
> | figyeli | Nem | Nem |
> | notificationsettings | Nem | Nem |

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>További lépések
Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)fájlt.
