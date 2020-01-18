---
title: Művelet támogatásának áthelyezése erőforrástípus szerint
description: Felsorolja az új erőforráscsoporthoz vagy előfizetésbe áthelyezhető Azure-erőforrástípusok listáját.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: da08775ed6c694b95ecec452507f94638091db0c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261058"
---
# <a name="move-operation-support-for-resources"></a>Művelet-támogatás áthelyezése az erőforrásokhoz
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
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. számlázás](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft. számítás](#microsoftcompute)
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
> - [Microsoft. Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. bepillantások](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft. kulcstartó](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft. Network](#microsoftnetwork)
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
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
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
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. előfizetés](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | domainservices | Nem | Nem |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | bérlők | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | konfigurációk | Nem | Nem |
> | javaslatok | Nem | Nem |
> | fóliakondenzát | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | actionrules | Igen | Igen |
> | riasztások | Nem | Nem |
> | alertssummary | Nem | Nem |
> | smartdetectoralertrules | Igen | Igen |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | Kiszolgálók | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | szolgáltatás | Igen | Igen |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | configurationstores | Igen | Igen |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | Spring | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | apiapps | Nem | Nem |
> | appidentities | Nem | Nem |
> | átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
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
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Igen | Igen |
> | automationaccounts/konfigurációk | Igen | Igen |
> | automationaccounts/runbookok | Igen | Igen |

> [!IMPORTANT]
> A runbookok ugyanabban az erőforráscsoporthoz kell tartoznia, mint az Automation-fióknak.

## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Igen | Igen |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | hybriddatamanagers | Nem | Nem |
> | postgresinstances | Nem | Nem |
> | sqlbigdataclusters | Nem | Nem |
> | sqlinstances | Nem | Nem |
> | sqlserverregistrations | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | regisztrációk | Igen | Igen |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft. BatchAI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | fileservers | Nem | Nem |
> | feladatok | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftbilling"></a>Microsoft. számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | billingperiods | Nem | Nem |
> | billingpermissions | Nem | Nem |
> | billingroleassignments | Nem | Nem |
> | billingroledefinitions | Nem | Nem |
> | createbillingroleassignment | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | mapapis | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | biztalk | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nem | Nem |
> | néző | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nem | Nem |
> | tervrajzok | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | Redis | Igen | Igen |

> [!IMPORTANT]
> Ha az Azure cache for Redis-példány virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: [hálózati áthelyezési korlátozások](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Igen | Igen |
> | profilok | Igen | Igen |
> | profilok/végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tanúsítványrendelések | Igen | Igen |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tartománynevek | Igen | Nem |
> | virtualmachines | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nem | Nem |
> | reservedips | Nem | Nem |
> | virtualnetworks | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
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
> | proximityplacementgroups | Nem | Nem |
> | restorepointcollections | Nem | Nem |
> | sharedvmimages | Nem | Nem |
> | sharedvmimages/verziók | Nem | Nem |
> | pillanatképek | Igen | Igen |
> | virtualmachines | Igen | Igen |
> | virtualmachines/bővítmények | Igen | Igen |
> | virtualmachinescalesets | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Virtual Machines áthelyezési útmutató](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft. felhasználás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
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
> | Piacterek | Nem | Nem |
> | operationresults | Nem | Nem |
> | operationstatus | Nem | Nem |
> | Pricesheets | Nem | Nem |
> | termék | Nem | Nem |
> | reservationdetails | Nem | Nem |
> | reservationrecommendations | Nem | Nem |
> | reservationsummaries | Nem | Nem |
> | reservationtransactions | Nem | Nem |
> | tags | Nem | Nem |
> | bérlők | Nem | Nem |
> | feltételek | Nem | Nem |
> | UsageDetails | Nem | Nem |

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | containergroups | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | containergroups | Nem | Nem |
> | serviceassociationlinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | kibocsátásiegység | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/taskruns | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | containerservices | Nem | Nem |
> | managedclusters | Nem | Nem |
> | openshiftmanagedclusters | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | alkalmazás | Nem | Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | riasztások | Nem | Nem |
> | költségvetése | Nem | Nem |
> | összekötők | Igen | Igen |
> | Méretek | Nem | Nem |
> | export | Nem | Nem |
> | externalsubscriptions | Nem | Nem |
> | forecast | Nem | Nem |
> | lekérdezés | Nem | Nem |
> | Reportconfigs | Nem | Nem |
> | jelentések | Nem | Nem |
> | showbackrules | Nem | Nem |
> | kilátással | Nem | Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | hubok | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | szövetségek | Nem | Nem |
> | resourceproviders | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | katalógusok | Igen | Igen |
> | datacatalogs | Nem | Nem |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | csomagok | Nem | Nem |
> | tervek | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | datafactories | Igen | Igen |
> | előállítók | Igen | Igen |

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | services | Nem | Nem |
> | szolgáltatások/projektek | Nem | Nem |
> | bővítőhely | Nem | Nem |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | Kiszolgálók | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | Kiszolgálók | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | servergroups | Nem | Nem |
> | Kiszolgálók | Igen | Igen |
> | serversv2 | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | artifactsources | Igen | Igen |
> | kibocsátások | Igen | Igen |
> | servicetopologies | Igen | Igen |
> | servicetopologies/szolgáltatások | Igen | Igen |
> | servicetopologies/szolgáltatások/serviceunits | Igen | Igen |
> | lépések | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nem | Nem |
> | elasticpools / iothubtenants | Nem | Nem |
> | iothubs | Igen | Igen |
> | provisioningservices | Igen | Igen |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | csővezetékek | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tartományvezérlők | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | labcenters | Nem | Nem |
> | Labs | Igen | Nem |
> | Labs/környezetek | Igen | Igen |
> | Labor/servicerunners | Igen | Igen |
> | Labor/virtualmachines | Igen | Nem |
> | menetrend | Igen | Igen |

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tartomány | Igen | Igen |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tartomány | Igen | Igen |
> | eventSubscriptions | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. |
> | eventsubscriptions | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. | Nem – nem helyezhető el egymástól függetlenül, de automatikusan áthelyezi az előfizetett erőforrással. |
> | extensiontopics | Nem | Nem |
> | témakör | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |
> | névterek | Igen | Igen |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | guestconfigurationassignments | Nem | Nem |
> | szoftver | Nem | Nem |
> | softwareupdateprofile | Nem | Nem |
> | softwareupdates | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nem | Nem |
> | sapmonitors | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |

> [!IMPORTANT]
> A HDInsight-fürtöket új előfizetésre vagy erőforráscsoporthoz helyezheti át. Azonban a HDInsight-fürthöz társított hálózati erőforrások (például a virtuális hálózat, a hálózati adapter vagy a terheléselosztó) nem helyezhetők át az előfizetésekben. Emellett a fürthöz tartozó virtuális géphez csatlakoztatott hálózati adapterek nem helyezhetők át egy új erőforráscsoporthoz.
>
> Amikor új előfizetésre helyez át egy HDInsight-fürtöt, először helyezze át a többi erőforrást (például a Storage-fiókot). Ezután saját maga helyezheti át a HDInsight-fürtöt.

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | gépek | Igen | Igen |
> | gépek/bővítmények | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | datamanagers | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | actiongroups | Igen | Igen |
> | activitylogalerts | Nem | Nem |
> | alertrules | Igen | Igen |
> | automatedexportsettings | Nem | Nem |
> | autoscalesettings | Igen | Igen |
> | alapkonfiguráció | Nem | Nem |
> | calculatebaseline | Nem | Nem |
> | összetevők | Igen | Igen |
> | diagnosticsettings | Nem | Nem |
> | diagnosticsettingscategories | Nem | Nem |
> | eventtypes | Nem | Nem |
> | extendeddiagnosticsettings | Nem | Nem |
> | logdefinitions | Nem | Nem |
> | naplók | Nem | Nem |
> | metricalerts | Nem | Nem |
> | metricbaselines | Nem | Nem |
> | metricdefinitions | Nem | Nem |
> | metricnamespaces | Nem | Nem |
> | metrics | Nem | Nem |
> | myworkbooks | Nem | Nem |
> | scheduledqueryrules | Igen | Igen |
> | topology | Nem | Nem |
> | tranzakciónként | Nem | Nem |
> | vminsightsonboardingstatuses | Nem | Nem |
> | webteszteket | Igen | Igen |
> | munkafüzetek | Igen | Igen |
> | workbooktemplates | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Igen | Igen |
> | Graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> A lemezes titkosításhoz használt kulcstartók nem helyezhetők át ugyanabba az előfizetésbe vagy előfizetésbe tartozó erőforráscsoporthoz.

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nem | Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nem | Nem |
> | integrationaccounts | Igen | Igen |
> | integrationserviceenvironments | Igen | Nem |
> | integrationserviceenvironments/król | Igen | Nem |
> | isolatedenvironments | Nem | Nem |
> | munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Igen | Igen |
> | WebServices | Igen | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Nem | Nem |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | fiókok/munkaterületek | Nem | Nem |
> | fiókok/munkaterületek/projektek | Nem | Nem |
> | teamaccounts | Nem | Nem |
> | teamaccounts/munkaterületek | Nem | Nem |
> | teamaccounts/munkaterületek/projektek | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | identitások | Nem | Nem |
> | userassignedidentities | Nem | Nem |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | registrationassignments | Nem | Nem |
> | registrationdefinitions | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | Mediaservices | Igen | Igen |
> | Mediaservices/liveevents | Igen | Igen |
> | Mediaservices/streamingendpoints | Igen | Igen |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | appclusters | Nem | Nem |

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Igen | Igen |
> | migrateprojects | Igen | Igen |
> | projektek | Nem | Nem |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nem | Nem |
> | netappaccounts / backuppolicies | Nem | Nem |
> | netappaccounts / capacitypools | Nem | Nem |
> | netappaccounts/capacitypools/kötetek | Nem | Nem |
> | netappaccounts/capacitypools/kötetek/mounttargets | Nem | Nem |
> | netappaccounts/capacitypools/kötetek/Pillanatképek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
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
> | frontdoors | Nem | Nem |
> | frontdoorwebapplicationfirewallpolicies | Nem | Nem |
> | loadbalancers | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | localnetworkgateways | Igen | Igen |
> | networkexperimentprofiles | Igen | Igen |
> | networkintentpolicies | Igen | Igen |
> | networkinterfaces | Igen | Igen |
> | networkprofiles | Nem | Nem |
> | networksecuritygroups | Igen | Igen |
> | networkwatchers | Igen | Igen |
> | networkwatchers / connectionmonitors | Igen | Igen |
> | networkwatchers / flowlogs | Igen | Igen |
> | networkwatchers/objektívek | Igen | Igen |
> | networkwatchers / pingmeshes | Igen | Igen |
> | p2svpngateways | Nem | Nem |
> | privatednszones | Igen | Igen |
> | privatednszones / virtualnetworklinks | Igen | Igen |
> | privateendpointredirectmaps | Nem | Nem |
> | privateendpoints | Nem | Nem |
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
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |
> | névterek/notificationhubs | Igen | Igen |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | storageinsightconfigs | Nem | Nem |
> | munkaterületek | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | managementassociations | Nem | Nem |
> | managementconfigurations | Igen | Igen |
> | megoldások | Igen | Igen |
> | kilátással | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | társviszonyok | Igen | Igen |
> | peeringservices | Nem | Nem |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | policyevents | Nem | Nem |
> | policystates | Nem | Nem |
> | policytrackedresources | Nem | Nem |
> | szervizelések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | irányítópultok | Igen | Igen |

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | rootresources | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | kapacitások | Igen | Igen |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | kibocsátások | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems | Nem | Nem |
> | replicationeligibilityresults | Nem | Nem |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | lekérdezés | Igen | Igen |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | availabilitystatuses | Nem | Nem |
> | childavailabilitystatuses | Nem | Nem |
> | childresources | Nem | Nem |
> | események | Nem | Nem |
> | értesítések | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | deploymentscripts | Nem | Nem |
> | linkek | Nem | Nem |
> | tags | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | alkalmazás | Igen | Nem |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | searchservices | Igen | Igen |

> [!IMPORTANT]
> Egy műveletben nem helyezhető át több keresési erőforrás különböző régiókban. Ehelyett külön műveletekben helyezze át őket.

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Nem | Nem |
> | advancedthreatprotectionsettings | Nem | Nem |
> | assessmentmetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | automatizálások szabványának létrehozásában | Igen | Igen |
> | complianceresults | Nem | Nem |
> | Felelésről | Nem | Nem |
> | datacollectionagents | Nem | Nem |
> | datacollectionresults | Nem | Nem |
> | devicesecuritygroups | Nem | Nem |
> | informationprotectionpolicies | Nem | Nem |
> | iotsecuritysolutions | Igen | Igen |
> | servervulnerabilityassessments | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | összesítések | Nem | Nem |
> | alertrules | Nem | Nem |
> | alertruletemplates | Nem | Nem |
> | könyvjelzők | Nem | Nem |
> | esetekben | Nem | Nem |
> | dataconnectors | Nem | Nem |
> | szervezetek | Nem | Nem |
> | entityqueries | Nem | Nem |
> | officeconsents | Nem | Nem |
> | beállítások | Nem | Nem |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | átjárók | Nem | Nem |
> | csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | alkalmazás | Nem | Nem |
> | fürtök | Igen | Igen |
> | fürtök/alkalmazások | Nem | Nem |
> | containergroups | Nem | Nem |
> | containergroupsets | Nem | Nem |
> | edgeclusters | Nem | Nem |
> | hálózatok | Nem | Nem |
> | secretstores | Nem | Nem |
> | volumes | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | alkalmazás | Igen | Igen |
> | átjárók | Igen | Igen |
> | hálózatok | Igen | Igen |
> | titkok | Igen | Igen |
> | volumes | Igen | Igen |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | kibocsátások | Nem | Nem |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | signalr | Igen | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Nem | Nem |
> | alkalmazás | Nem | Nem |
> | jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | instancepools | Nem | Nem |
> | managedinstances | Nem | Nem |
> | managedinstances/adatbázisok | Nem | Nem |
> | Kiszolgálók | Igen | Igen |
> | kiszolgálók/adatbázisok | Igen | Igen |
> | kiszolgálók/elasticpools | Igen | Igen |
> | virtualclusters | Igen | Igen |

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoporthoz kell tartoznia. Ha egy SQL Servert helyez át, az összes adatbázisa is át lesz helyezve. Ez a viselkedés Azure SQL Database és Azure SQL Data Warehouse adatbázisokra vonatkozik.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Igen | Igen |
> | sqlvirtualmachines | Igen | Igen |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | dwvm | Nem | Nem |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Igen | Igen |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Igen | Igen |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | kezelők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Igen | Igen |

> [!IMPORTANT]
> Stream Analytics feladatok futási állapotban nem helyezhetők át.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | környezetben | Nem | Nem |
> | környezetek/eventsources | Nem | Nem |
> | példányok | Nem | Nem |
> | példányok/környezetek | Nem | Nem |
> | példányok/környezetek/eventsources | Nem | Nem |

## <a name="microsoftsubscription"></a>Microsoft. előfizetés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | createsubscription | Nem | Nem |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | createsupportticket | Nem | Nem |
> | supporttickets | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nem | Nem |
> | erőforrások | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | környezetben | Igen | Igen |
> | környezetek/eventsources | Igen | Igen |
> | környezetek/referencedatasets | Igen | Igen |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tárolja | Igen | Igen |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Nem | Nem |
> | dedicatedcloudservices | Nem | Nem |
> | virtualmachines | Nem | Nem |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | tervek | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | tanúsítványok | Nem | Igen |
> | connectiongateways | Igen | Igen |
> | kapcsolatok | Igen | Igen |
> | customapis | Igen | Igen |
> | hostingenvironments | Nem | Nem |
> | kiszolgálófarmok | Igen | Igen |
> | webhelyek | Igen | Igen |
> | helyek/premieraddons | Igen | Igen |
> | helyek/bővítőhelyek | Igen | Igen |
> | staticsites | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nem | Nem |

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetést |
> | ------------- | ----------- | ---------- |
> | összetevők | Nem | Nem |
> | monitorinstances | Nem | Nem |
> | figyeli | Nem | Nem |
> | notificationsettings | Nem | Nem |

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>Következő lépések
Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](move-resource-group-and-subscription.md).

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)fájlt.
