---
title: A művelet támogatásának áthelyezése Azure-erőforrástípus szerint
description: Felsorolja az új erőforráscsoporthoz vagy előfizetésbe áthelyezhető Azure-erőforrástípusok listáját.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 73f4b6fe4714d21c12d2c7bd387cd30f6f711d5a
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624322"
---
# <a name="move-operation-support-for-resources"></a>Művelet-támogatás áthelyezése az erőforrásokhoz
Ez a cikk azt mutatja be, hogy az Azure-erőforrástípus támogatja-e az áthelyezési műveletet. Emellett az erőforrások áthelyezésekor megfontolandó speciális feltételekkel kapcsolatos információkat is tartalmaz.

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft. backup](#microsoftbackup)
> - [Microsoft. batch](#microsoftbatch)
> - [Microsoft. BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| bérlők | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Igen | Igen |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kiszolgáló | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| szolgáltatás | Igen | Igen |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Nem | Nem |
| appidentities | Nem | Nem |
| átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Igen | Igen |
| automationaccounts/konfigurációk | Igen | Igen |
| automationaccounts/runbookok | Igen | Igen |

> [!IMPORTANT]
> A runbookok ugyanabban az erőforráscsoporthoz kell tartoznia, mint az Automation-fióknak.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| regisztrációk | Igen | Igen |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztonsági mentési tár | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fürtök | Nem | Nem |
| fileservers | Nem | Nem |
| feladatok | Nem | Nem |
| munkaterületek | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Igen | Igen |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Igen | Igen |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Redis | Igen | Igen |

> [!IMPORTANT]
> Ha az Azure cache for Redis-példány virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: a [virtuális hálózatok áthelyezésének korlátai](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Igen | Igen |
| profilok/végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tanúsítványrendelések | Igen | Igen |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tartománynevek | Igen | Nem |
| virtualmachines | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nem | Nem |
| reservedips | Nem | Nem |
| virtualnetworks | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Igen | Igen |
| lemezek | Igen | Igen |
| katalógusok | Nem | Nem |
| galériák/lemezképek | Nem | Nem |
| galériák/lemezképek/verziók | Nem | Nem |
| hostgroups | Nem | Nem |
| hostgroups/gazdagépek | Nem | Nem |
| rendszerképek | Igen | Igen |
| proximityplacementgroups | Nem | Nem |
| restorepointcollections | Nem | Nem |
| sharedvmimages | Nem | Nem |
| sharedvmimages/verziók | Nem | Nem |
| pillanatképek | Igen | Igen |
| virtualmachines | Igen | Igen |
| virtualmachines/bővítmények | Igen | Igen |
| virtualmachinescalesets | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Virtual Machines áthelyezési útmutató](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kibocsátásiegység | Igen | Igen |
| registries/buildtasks | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Igen |
| kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Nem | Nem |
| managedclusters | Nem | Nem |
| openshiftmanagedclusters | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Igen |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| összekötők | Igen | Igen |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Hubs | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| katalógusok | Igen | Igen |
| datacatalogs | Nem | Nem |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| csomagok | Nem | Nem |
| tervek | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Igen | Igen |
| előállítók | Igen | Igen |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Nem | Nem |
| szolgáltatások/projektek | Nem | Nem |
| bővítőhely | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Nem | Nem |
| kiszolgáló | Igen | Igen |
| serversv2 | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Igen | Igen |
| kibocsátások | Igen | Igen |
| servicetopologies | Igen | Igen |
| servicetopologies/szolgáltatások | Igen | Igen |
| servicetopologies/szolgáltatások/serviceunits | Igen | Igen |
| lépések | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Nem | Nem |
| elasticpools/iothubtenants | Nem | Nem |
| iothubs | Igen | Igen |
| provisioningservices | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tartományvezérlők | Nem | Nem |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Nem | Nem |
| Labs | Igen | Nem |
| Labs/környezetek | Igen | Igen |
| Labor/servicerunners | Igen | Igen |
| Labor/virtualmachines | Igen | Nem |
| menetrend | Igen | Igen |

## <a name="microsoftdns"></a>Microsoft. DNS
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Nem | Nem |
| dnszones/a | Nem | Nem |
| dnszones/aaaa | Nem | Nem |
| dnszones/cname | Nem | Nem |
| dnszones/mx | Nem | Nem |
| dnszones/PTR | Nem | Nem |
| dnszones/srv | Nem | Nem |
| dnszones/txt | Nem | Nem |
| trafficmanagerprofiles | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tartományok | Igen | Igen |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tartományok | Igen | Igen |
| témakörök | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fürtök | Igen | Igen |
| névterek | Igen | Igen |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fürtök | Igen | Igen |

> [!IMPORTANT]
> HDInsight-fürtök áthelyezheti egy új előfizetést, vagy az erőforráscsoportot. Azonban nem helyezhetők át a hálózati erőforrások (például a virtuális hálózathoz, a hálózati adapter vagy a terheléselosztó) a HDInsight-fürthöz társított előfizetésekben. Emellett nem helyezhető át egy új erőforráscsoportot egy hálózati Adaptert, amely a fürt egy virtuális géphez van csatolva.
>
> Amikor új előfizetésbe való áthelyezését egy HDInsight-fürtöt, először helyezze át más erőforrások (például a storage-fiók). Ezután helyezze át a HDInsight-fürt önmagában.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gép | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>Microsoft. bepillantások
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |
| actiongroups | Igen | Igen |
| activitylogalerts | Nem | Nem |
| alertrules | Igen | Igen |
| autoscalesettings | Igen | Igen |
| összetevők | Igen | Igen |
| guestdiagnosticsettings | Nem | Nem |
| metricalerts | Nem | Nem |
| notificationgroups | Nem | Nem |
| notificationrules | Nem | Nem |
| scheduledqueryrules | Igen | Igen |
| webteszteket | Igen | Igen |
| munkafüzetek | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](../azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Igen | Igen |
| graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Nem | Nem |
| boltívek | Igen | Igen |

> [!IMPORTANT]
> A lemezes titkosításhoz használt kulcstartók nem helyezhetők át ugyanabba az előfizetésbe vagy előfizetésbe tartozó erőforráscsoporthoz.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fürtök | Igen | Igen |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Nem | Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nem | Nem |
| integrationaccounts | Igen | Igen |
| integrationserviceenvironments | Nem | Nem |
| isolatedenvironments | Nem | Nem |
| munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Igen | Igen |
| WebServices | Igen | Nem |
| munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |
| fiókok/munkaterületek | Nem | Nem |
| fiókok/munkaterületek/projektek | Nem | Nem |
| teamaccounts | Nem | Nem |
| teamaccounts/munkaterületek | Nem | Nem |
| teamaccounts/munkaterületek/projektek | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| munkaterületek | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Mediaservices | Igen | Igen |
| Mediaservices/liveevents | Igen | Igen |
| Mediaservices/streamingendpoints | Igen | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nem | Nem |
| migrateprojects | Nem | Nem |
| projektek | Nem | Nem |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Nem | Nem |
| netappaccounts/capacitypools | Nem | Nem |
| netappaccounts/capacitypools/kötetek | Nem | Nem |
| netappaccounts/capacitypools/volumes/mounttargets | Nem | Nem |
| netappaccounts/capacitypools/volumes/snapshots | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Nem | Nem |
| applicationgatewaywebapplicationfirewallpolicies | Nem | Nem |
| applicationsecuritygroups | Igen | Igen |
| azurefirewalls | Igen | Igen |
| bastionhosts | Nem | Nem |
| kapcsolatok | Igen | Igen |
| ddoscustompolicies | Igen | Igen |
| ddosprotectionplans | Nem | Nem |
| dnszones | Igen | Igen |
| expressroutecircuits | Nem | Nem |
| expressroutecrossconnections | Nem | Nem |
| expressroutegateways | Nem | Nem |
| expressrouteports | Nem | Nem |
| frontdoorok | Nem | Nem |
| frontdoorwebapplicationfirewallpolicies | Nem | Nem |
| loadbalancers | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
| localnetworkgateways | Igen | Igen |
| natgateways | Igen | Igen |
| networkintentpolicies | Igen | Igen |
| networkinterfaces | Igen | Igen |
| networkprofiles | Nem | Nem |
| networksecuritygroups | Igen | Igen |
| networkwatchers | Igen | Igen |
| networkwatchers/connectionmonitors | Igen | Igen |
| networkwatchers/objektívek | Igen | Igen |
| networkwatchers/pingmeshes | Igen | Igen |
| p2svpngateways | Nem | Nem |
| privatednszones | Igen | Igen |
| privatednszones/virtualnetworklinks | Igen | Igen |
| privateendpoints | Nem | Nem |
| privatelinkservices | Nem | Nem |
| nyilvános IP | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
| publicipprefixes | Igen | Igen |
| routefilters | Nem | Nem |
| routetables | Igen | Igen |
| securegateways | Igen | Igen |
| serviceendpointpolicies | Igen | Igen |
| trafficmanagerprofiles | Igen | Igen |
| virtualhubs | Nem | Nem |
| virtualnetworkgateways | Igen | Igen |
| virtualnetworks | Igen | Igen |
| virtualnetworktaps | Nem | Nem |
| virtualwans | Nem | Nem |
| vpngateways | Nem | Nem |
| vpnsites | Nem | Nem |
| webapplicationfirewallpolicies | Igen | Igen |

> [!IMPORTANT]
> Lásd: [hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| névterek | Igen | Igen |
| névterek/notificationhubs | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| munkaterületek | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](../azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Igen | Igen |
| megoldások | Igen | Igen |
| megtekintés | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| társviszonyok | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| irányítópultok | Igen | Igen |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kapacitások | Igen | Igen |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| boltívek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| névterek | Igen | Igen |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| forgalom | Igen | Igen |
| feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Igen | Igen |

> [!IMPORTANT]
> Egy műveletben nem helyezhető át több keresési erőforrás különböző régiókban. Ehelyett külön műveletekben helyezze át őket.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Igen | Igen |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| átjárók | Nem | Nem |
| csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Nem | Nem |
| fürtök | Igen | Igen |
| containergroups | Nem | Nem |
| containergroupsets | Nem | Nem |
| edgeclusters | Nem | Nem |
| hálózatok | Nem | Nem |
| secretstores | Nem | Nem |
| kötetek | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Igen |
| containergroups | Nem | Nem |
| átjárók | Igen | Igen |
| hálózatok | Igen | Igen |
| titkos kódok | Igen | Igen |
| kötetek | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Igen | Igen |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nem | Nem |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nem | Nem |
| berendezések | Nem | Nem |
| applicationdefinitions | Nem | Nem |
| alkalmazások | Nem | Nem |
| jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Nem | Nem |
| managedinstances | Nem | Nem |
| managedinstances/adatbázisok | Nem | Nem |
| kiszolgáló | Igen | Igen |
| kiszolgálók/adatbázisok | Igen | Igen |
| servers/elasticpools | Igen | Igen |
| virtualclusters | Igen | Igen |

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoporthoz kell tartoznia. Ha áthelyezi SQL-kiszolgáló, az összes hozzá tartozó adatbázisok is kerülnek. Ez a viselkedés az Azure SQL Database és az Azure SQL Data Warehouse-adatbázisok vonatkozik.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Igen | Igen |
| sqlvirtualmachines | Igen | Igen |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Nem | Nem |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Igen | Igen |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gyorsítótárak | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Igen | Igen |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| kezelők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Igen | Igen |

> [!IMPORTANT]
> Stream Analytics feladatok futási állapotban nem helyezhetők át.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| környezetben | Nem | Nem |
| környezetek/eventsources | Nem | Nem |
| példányok | Nem | Nem |
| példányok/környezetek | Nem | Nem |
| példányok/környezetek/eventsources | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Nem | Nem |
| erőforrások | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| környezetben | Igen | Igen |
| környezetek/eventsources | Igen | Igen |
| környezetek/referencedatasets | Igen | Igen |

## <a name="microsofttoken"></a>Microsoft. token
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tárolja | Nem | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Nem | Nem |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiók | Igen | Igen |
| fiók/bővítmény | Igen | Igen |
| fiók/projekt | Igen | Igen |

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosításához tekintse meg [a számlázáshoz használt Azure-előfizetés módosítása](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)című témakört.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Igen | Igen |
| dedicatedcloudservices | Igen | Igen |
| virtualmachines | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tanúsítványok | Nem | Igen |
| connectiongateways | Igen | Igen |
| kapcsolatok | Igen | Igen |
| customapis | Igen | Igen |
| hostingenvironments | Nem | Nem |
| kiszolgálófarmok | Igen | Igen |
| webhelyek | Igen | Igen |
| helyek/premieraddons | Igen | Igen |
| helyek/bővítőhelyek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Nem | Nem |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Nem | Nem |
| hostpools | Nem | Nem |
| munkaterületek | Nem | Nem |

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>További lépések
Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy](resource-group-move-resources.md)előfizetésbe.

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)fájlt.
