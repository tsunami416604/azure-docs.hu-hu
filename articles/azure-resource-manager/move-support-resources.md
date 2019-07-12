---
title: Művelet támogatási áthelyezése az Azure-erőforrástípus szerint
description: Az Azure-erőforrástípus, amely egy új erőforráscsoportot vagy előfizetést is áthelyezhető sorolja fel.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 093c20407cb6210125106189f36566f539de0dcc
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721111"
---
# <a name="move-operation-support-for-resources"></a>Művelet támogatási erőforrások áthelyezése
Ez a cikk felsorolja egy Azure-erőforrás az áthelyezési művelet támogatja-e. Erőforrások áthelyezésekor megfontolandó szempontok speciális feltételekkel kapcsolatos információkat is biztosít.

Egy erőforrás-szolgáltató névtere ugorhat:
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
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
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
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
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
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Bérlők | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Igen | Igen |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Kiszolgálók | Igen | Igen |

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
| Átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [App Service-ben áthelyezése útmutatást](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Igen | Igen |
| automationaccounts/konfigurációk | Igen | Igen |
| automationaccounts/runbooks | Igen | Igen |

> [!IMPORTANT]
> Runbookok léteznie kell, az Automation-fióknak ugyanabban az erőforráscsoportban.

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
| Fürtök | Nem | Nem |
| fileservers | Nem | Nem |
| Feladatok | Nem | Nem |
| Munkaterületek | Nem | Nem |

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

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Redis Cache | Igen | Igen |

> [!IMPORTANT]
> Ha az Azure Cache Redis-példány és a virtuális hálózat van konfigurálva, a példány nem helyezhető át egy másik előfizetést. Lásd: [virtuális hálózatok áthelyezése korlátozások](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Igen | Igen |
| profilok és végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tanúsítványrendelések | Igen | Igen |

> [!IMPORTANT]
> Lásd: [App Service-ben áthelyezése útmutatást](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Igen | Nem |
| virtuális gép | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató áthelyezése](./move-limitations/classic-model-move-limitations.md). Klasszikus üzembe helyezési erőforrásokkal is áthelyezhető egy adott művelethez előfizetésekben forgatókönyvhöz.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nem | Nem |
| reservedips | Nem | Nem |
| virtualnetworks | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató áthelyezése](./move-limitations/classic-model-move-limitations.md). Klasszikus üzembe helyezési erőforrásokkal is áthelyezhető egy adott művelethez előfizetésekben forgatókönyvhöz.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tárfiókok | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató áthelyezése](./move-limitations/classic-model-move-limitations.md). Klasszikus üzembe helyezési erőforrásokkal is áthelyezhető egy adott művelethez előfizetésekben forgatókönyvhöz.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Igen | Igen |
| Lemezek | Igen | Igen |
| katalógusok | Nem | Nem |
| galériák vagy-képekből | Nem | Nem |
| galériák/képek/verziója | Nem | Nem |
| hostgroups | Nem | Nem |
| hostgroups /-gazdagépek | Nem | Nem |
| images | Igen | Igen |
| proximityplacementgroups | Nem | Nem |
| restorepointcollections | Nem | Nem |
| sharedvmimages | Nem | Nem |
| sharedvmimages/verziója | Nem | Nem |
| snapshots | Igen | Igen |
| virtuális gép | Igen | Igen |
| virtuális gép/bővítmények | Igen | Igen |
| virtualmachinescalesets | Igen | Igen |

> [!IMPORTANT]
> Lásd: [virtuális gépek áthelyezése útmutatást](./move-limitations/virtual-machines-move-limitations.md).

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
| beállításjegyzékek | Igen | Igen |
| registries/buildtasks | Igen | Igen |
| beállításjegyzékek/replikációk | Igen | Igen |
| beállításjegyzékek/feladatok | Igen | Igen |
| beállításjegyzékek és webhookok | Igen | Igen |

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
| hubok | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Munkaterületek | Nem | Nem |

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
| Csomagok | Nem | Nem |
| Csomagok | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datafactories | Igen | Igen |
| gyárak | Igen | Igen |

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
| Services-projektek | Nem | Nem |
| tárhelyek | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Kiszolgálók | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Kiszolgálók | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Nem | Nem |
| Kiszolgálók | Igen | Igen |
| serversv2 | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Igen | Igen |
| Kibocsátások | Igen | Igen |
| servicetopologies | Igen | Igen |
| servicetopologies/szolgáltatások | Igen | Igen |
| servicetopologies/services/serviceunits | Igen | Igen |
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
| Tartományvezérlők | Nem | Nem |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Nem | Nem |
| Laborgyakorlatok | Igen | Nem |
| laborok/környezetek | Igen | Igen |
| labs/servicerunners | Igen | Igen |
| laborok/virtuális gép | Igen | Nem |
| Ütemezések | Igen | Igen |

## <a name="microsoftdns"></a>microsoft.dns
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Nem | Nem |
| dnszones/a | Nem | Nem |
| dnszones/aaaa | Nem | Nem |
| dnszones/cname | Nem | Nem |
| dnszones/mx | Nem | Nem |
| dnszones/ptr | Nem | Nem |
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
| kapcsolatos témakörök | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Fürtök | Igen | Igen |
| Névterek | Igen | Igen |

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
| Fürtök | Igen | Igen |

> [!IMPORTANT]
> HDInsight-fürtök áthelyezheti egy új előfizetést, vagy az erőforráscsoportot. Azonban nem helyezhetők át a hálózati erőforrások (például a virtuális hálózathoz, a hálózati adapter vagy a terheléselosztó) a HDInsight-fürthöz társított előfizetésekben. Emellett nem helyezhető át egy új erőforráscsoportot egy hálózati Adaptert, amely a fürt egy virtuális géphez van csatolva.
>
> Amikor új előfizetésbe való áthelyezését egy HDInsight-fürtöt, először helyezze át más erőforrások (például a storage-fiók). Ezután helyezze át a HDInsight-fürt önmagában.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| gépek | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagers | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>microsoft.insights
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |
| actiongroups | Igen | Igen |
| activitylogalerts | Nem | Nem |
| alertrules | Igen | Igen |
| autoscalesettings beállítás | Igen | Igen |
| Összetevők | Igen | Igen |
| guestdiagnosticsettings | Nem | Nem |
| metricalerts | Nem | Nem |
| notificationgroups | Nem | Nem |
| notificationrules | Nem | Nem |
| scheduledqueryrules | Igen | Igen |
| webteszt | Igen | Igen |
| munkafüzetek | Igen | Igen |

> [!IMPORTANT]
> Ellenőrizze, hogy új előfizetésbe való áthelyezését nem haladja meg [előfizetési kvóták](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Igen | Igen |
| graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Nem | Nem |
| tárolók | Igen | Igen |

> [!IMPORTANT]
> Key vault-Kulcstartók lemez titkosításához használt erőforráscsoport az ugyanazon az előfizetésen belül vagy előfizetések között nem lehet áthelyezni.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Fürtök | Igen | Igen |

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
| A munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Igen | Igen |
| problémák megoldásához segítséget | Igen | Nem |
| Munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |
| fiókok és munkaterületek | Nem | Nem |
| fiókok/munkaterületeket és projekteket | Nem | Nem |
| teamaccounts | Nem | Nem |
| teamaccounts/munkaterületek | Nem | Nem |
| teamaccounts/munkaterületeket és projekteket | Nem | Nem |

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
| Munkaterületek | Nem | Nem |

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
| mediaservices | Igen | Igen |
| mediaservices/liveevents | Igen | Igen |
| mediaservices/streamvégpontok | Igen | Igen |

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
| netappaccounts/capacitypools/volumes | Nem | Nem |
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
| Kapcsolatok | Igen | Igen |
| ddoscustompolicies | Igen | Igen |
| ddosprotectionplans | Nem | Nem |
| dnszones | Igen | Igen |
| expressroutecircuits | Nem | Nem |
| expressroutecrossconnections | Nem | Nem |
| expressroutegateways | Nem | Nem |
| expressrouteports | Nem | Nem |
| frontdoors | Nem | Nem |
| frontdoorwebapplicationfirewallpolicies | Nem | Nem |
| loadbalancers | Igen – alapszintű Termékváltozat<br>Nem – a Standard Termékváltozat | Igen – alapszintű Termékváltozat<br>Nem – a Standard Termékváltozat |
| localnetworkgateways | Igen | Igen |
| natgateways | Igen | Igen |
| networkintentpolicies | Igen | Igen |
| hálózati | Igen | Igen |
| networkprofiles | Nem | Nem |
| networksecuritygroups | Igen | Igen |
| networkwatchers | Igen | Igen |
| networkwatchers/connectionmonitors | Igen | Igen |
| networkwatchers/átvilágított felülettel | Igen | Igen |
| networkwatchers/pingmeshes | Igen | Igen |
| p2svpngateways | Nem | Nem |
| privatednszones | Igen | Igen |
| privatednszones/virtualnetworklinks | Igen | Igen |
| privateendpoints | Nem | Nem |
| privatelinkservices | Nem | Nem |
| publicipaddresses | Igen – alapszintű Termékváltozat<br>Nem – a Standard Termékváltozat | Igen – alapszintű Termékváltozat<br>Nem – a Standard Termékváltozat |
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
> Lásd: [virtuális hálózatok áthelyezése útmutatást](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Névterek | Igen | Igen |
| névtér/notificationhubs | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Munkaterületek | Igen | Igen |

> [!IMPORTANT]
> Ellenőrizze, hogy új előfizetésbe való áthelyezését nem haladja meg [előfizetési kvóták](../azure-subscription-service-limits.md#azure-monitor-limits).

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Igen | Igen |
| megoldások | Igen | Igen |
| Nézetek | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| társviszony-létesítések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Az irányítópultok | Igen | Igen |

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
| Kapacitások | Igen | Igen |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tárolók | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezése útmutatást](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Névterek | Igen | Igen |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| folyamatok | Igen | Igen |
| feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Igen | Igen |

> [!IMPORTANT]
> Nem helyezhetők át erőforrások keresése több különböző régiókban lévő több műveletet. Helyezze át őket a különböző műveletek.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Igen | Igen |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Átjárók | Nem | Nem |
| csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Nem | Nem |
| Fürtök | Igen | Igen |
| containergroups | Nem | Nem |
| containergroupsets | Nem | Nem |
| edgeclusters | Nem | Nem |
| Hálózatok | Nem | Nem |
| secretstores | Nem | Nem |
| volumes | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Igen |
| containergroups | Nem | Nem |
| Átjárók | Igen | Igen |
| Hálózatok | Igen | Igen |
| Titkos kulcsok | Igen | Igen |
| volumes | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| a signalr | Igen | Igen |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nem | Nem |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezése útmutatást](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nem | Nem |
| készülékek | Nem | Nem |
| applicationdefinitions | Nem | Nem |
| alkalmazások | Nem | Nem |
| jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Igen | Igen |
| managedinstances | Igen | Igen |
| managedinstances/adatbázis | Igen | Igen |
| Kiszolgálók | Igen | Igen |
| kiszolgálók és adatbázisok | Igen | Igen |
| servers/elasticpools | Igen | Igen |
| virtualclusters | Igen | Igen |

> [!IMPORTANT]
> Egy adatbázis és a kiszolgáló ugyanabban az erőforráscsoportban kell lennie. Ha áthelyezi SQL-kiszolgáló, az összes hozzá tartozó adatbázisok is kerülnek. Ez a viselkedés az Azure SQL Database és az Azure SQL Data Warehouse-adatbázisok vonatkozik.

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
| tárfiókok | Igen | Igen |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Gyorsítótárak | Nem | Nem |

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
| vezetők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Igen | Igen |

> [!IMPORTANT]
> Stream Analytics-feladatok nem lehet áthelyezni, ha a futó állapotban.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Környezetek | Nem | Nem |
| környezetek/eventsources | Nem | Nem |
| példányok | Nem | Nem |
| példányok/környezetek | Nem | Nem |
| példányok/környezetek/eventsources | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Nem | Nem |
| Erőforrások | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Környezetek | Igen | Igen |
| környezetek/eventsources | Igen | Igen |
| környezetek/referencedatasets | Igen | Igen |

## <a name="microsofttoken"></a>Microsoft.Token
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tárolók | Nem | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Nem | Nem |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| account | Igen | Igen |
| fiók/bővítmény | Igen | Igen |
| fiók és a projekt | Igen | Igen |

> [!IMPORTANT]
> Ha módosítani szeretné az előfizetést az Azure DevOps, lásd: [számlázáshoz használható Azure-előfizetést](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Igen | Igen |
| dedicatedcloudservices | Igen | Igen |
| virtuális gép | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Tanúsítványok | Nem | Igen |
| connectiongateways | Igen | Igen |
| Kapcsolatok | Igen | Igen |
| customapis | Igen | Igen |
| hostingenvironments | Nem | Nem |
| serverfarms | Igen | Igen |
| Helyek | Igen | Igen |
| webhelyek/premieraddons | Igen | Igen |
| helyek és tárhelyek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [App Service-ben áthelyezése útmutatást](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Nem | Nem |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Erőforrás típusa | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Nem | Nem |
| hostpools | Nem | Nem |
| Munkaterületek | Nem | Nem |

## <a name="third-party-services"></a>Külső szolgáltatások

Külső szolgáltatások jelenleg nem támogatják az áthelyezési művelet.

## <a name="next-steps"></a>További lépések
Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

Első ugyanazokat az adatokat egy vesszővel tagolt formátumú fájlt, töltse le a [move-támogatás – resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).
