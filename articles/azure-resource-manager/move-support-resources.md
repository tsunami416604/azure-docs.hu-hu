---
title: A művelet támogatásának áthelyezése Azure-erőforrástípus szerint
description: Felsorolja az új erőforráscsoporthoz vagy előfizetésbe áthelyezhető Azure-erőforrástípusok listáját.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: tomfitz
ms.openlocfilehash: 3100ce897a09c2747c2b339662c699d197ba73d7
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194843"
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
> - [Microsoft. AzureData](#microsoftazuredata)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
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

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | domainservices | Nem | Nem |
> | domainservices / replicasets | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | bérlők | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | actionrules | Igen | Igen |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kiszolgáló | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | szolgáltatás | Igen | Igen |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | configurationstores | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | apiapps | Nem | Nem |
> | appidentities | Nem | Nem |
> | átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | policyassignments | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Igen | Igen |
> | automationaccounts/konfigurációk | Igen | Igen |
> | automationaccounts/runbookok | Igen | Igen |

> [!IMPORTANT]
> A runbookok ugyanabban az erőforráscsoporthoz kell tartoznia, mint az Automation-fióknak.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Igen | Igen |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlserverregistrations | Nem | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | regisztrációk | Igen | Igen |

## <a name="microsoftbackup"></a>Microsoft.Backup

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztonsági mentési tár | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fürtök | Nem | Nem |
> | fileservers | Nem | Nem |
> | feladatok | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | mapapis | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | biztalk | Igen | Igen |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Igen | Igen |
> | néző | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Redis | Igen | Igen |

> [!IMPORTANT]
> Ha az Azure cache for Redis-példány virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: [hálózati áthelyezési korlátozások](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Nem | Nem |
> | profiles | Igen | Igen |
> | profilok/végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tanúsítványrendelések | Igen | Igen |

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tartománynevek | Igen | Nem |
> | virtualmachines | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nem | Nem |
> | reservedips | Nem | Nem |
> | virtualnetworks | Nem | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Igen | Nem |

> [!IMPORTANT]
> Lásd: [klasszikus üzembe helyezési útmutató](./move-limitations/classic-model-move-limitations.md). A klasszikus üzembe helyezési erőforrások az adott forgatókönyvre jellemző művelettel helyezhetők át az előfizetések között.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Igen | Igen |
> | diskencryptionsets | Nem | Nem |
> | lemezek | Igen | Igen |
> | katalógusok | Nem | Nem |
> | galériák/lemezképek | Nem | Nem |
> | galériák/lemezképek/verziók | Nem | Nem |
> | hostgroups | Nem | Nem |
> | hostgroups/gazdagépek | Nem | Nem |
> | rendszerképek | Igen | Igen |
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

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containergroups | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kibocsátásiegység | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Igen | Igen |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | containerservices | Nem | Nem |
> | managedclusters | Nem | Nem |
> | openshiftmanagedclusters | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Igen |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | összekötők | Igen | Igen |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Hubs | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | katalógusok | Igen | Igen |
> | datacatalogs | Nem | Nem |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | csomagok | Nem | Nem |
> | tervek | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datafactories | Igen | Igen |
> | előállítók | Igen | Igen |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Nem | Nem |
> | szolgáltatások/projektek | Nem | Nem |
> | bővítőhely | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kiszolgáló | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kiszolgáló | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | servergroups | Nem | Nem |
> | kiszolgáló | Igen | Igen |
> | serversv2 | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | artifactsources | Igen | Igen |
> | kibocsátások | Igen | Igen |
> | servicetopologies | Igen | Igen |
> | servicetopologies/szolgáltatások | Igen | Igen |
> | servicetopologies/szolgáltatások/serviceunits | Igen | Igen |
> | lépések | Igen | Igen |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | elasticpools | Nem | Nem |
> | elasticpools / iothubtenants | Nem | Nem |
> | iothubs | Igen | Igen |
> | provisioningservices | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tartományvezérlők | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labcenters | Nem | Nem |
> | Labs | Igen | Nem |
> | Labs/környezetek | Igen | Igen |
> | Labor/servicerunners | Igen | Igen |
> | Labor/virtualmachines | Igen | Nem |
> | menetrend | Igen | Igen |

## <a name="microsoftdns"></a>Microsoft. DNS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dnszones | Nem | Nem |
> | dnszones/a | Nem | Nem |
> | dnszones/AAAA | Nem | Nem |
> | dnszones/CNAME | Nem | Nem |
> | dnszones/MX | Nem | Nem |
> | dnszones/PTR | Nem | Nem |
> | dnszones/SRV | Nem | Nem |
> | dnszones/txt | Nem | Nem |
> | trafficmanagerprofiles | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tartományok | Igen | Igen |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tartományok | Igen | Igen |
> | témakörök | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |
> | névterek | Igen | Igen |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hanainstances | Nem | Nem |
> | sapmonitors | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |

> [!IMPORTANT]
> HDInsight-fürtök áthelyezheti egy új előfizetést, vagy az erőforráscsoportot. Azonban nem helyezhetők át a hálózati erőforrások (például a virtuális hálózathoz, a hálózati adapter vagy a terheléselosztó) a HDInsight-fürthöz társított előfizetésekben. Emellett nem helyezhető át egy új erőforráscsoportot egy hálózati Adaptert, amely a fürt egy virtuális géphez van csatolva.
>
> Amikor új előfizetésbe való áthelyezését egy HDInsight-fürtöt, először helyezze át más erőforrások (például a storage-fiók). Ezután helyezze át a HDInsight-fürt önmagában.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gép | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | datamanagers | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | actiongroups | Igen | Igen |
> | activitylogalerts | Nem | Nem |
> | alertrules | Igen | Igen |
> | autoscalesettings | Igen | Igen |
> | összetevők | Igen | Igen |
> | guestdiagnosticsettings | Nem | Nem |
> | metricalerts | Nem | Nem |
> | notificationgroups | Nem | Nem |
> | notificationrules | Nem | Nem |
> | scheduledqueryrules | Igen | Igen |
> | webteszteket | Igen | Igen |
> | munkafüzetek | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](../azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Igen | Igen |
> | graph | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hsmpools | Nem | Nem |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> A lemezes titkosításhoz használt kulcstartók nem helyezhetők át ugyanabba az előfizetésbe vagy előfizetésbe tartozó erőforráscsoporthoz.

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fürtök | Igen | Igen |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | labaccounts | Nem | Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Nem | Nem |
> | integrationaccounts | Igen | Igen |
> | integrationserviceenvironments | Nem | Nem |
> | isolatedenvironments | Nem | Nem |
> | munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Igen | Igen |
> | WebServices | Igen | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | fiókok/munkaterületek | Nem | Nem |
> | fiókok/munkaterületek/projektek | Nem | Nem |
> | teamaccounts | Nem | Nem |
> | teamaccounts/munkaterületek | Nem | Nem |
> | teamaccounts/munkaterületek/projektek | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | hostingaccounts | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | userassignedidentities | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | Mediaservices | Igen | Igen |
> | Mediaservices/liveevents | Igen | Igen |
> | Mediaservices/streamingendpoints | Igen | Igen |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appclusters | Nem | Nem |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Nem | Nem |
> | migrateprojects | Nem | Nem |
> | projektek | Nem | Nem |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nem | Nem |
> | netappaccounts / capacitypools | Nem | Nem |
> | netappaccounts/capacitypools/kötetek | Nem | Nem |
> | netappaccounts/capacitypools/kötetek/mounttargets | Nem | Nem |
> | netappaccounts/capacitypools/kötetek/Pillanatképek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
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
> | expressroutecrossconnections | Nem | Nem |
> | expressroutegateways | Nem | Nem |
> | expressrouteports | Nem | Nem |
> | frontdoorok | Nem | Nem |
> | frontdoorwebapplicationfirewallpolicies | Nem | Nem |
> | loadbalancers | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | localnetworkgateways | Igen | Igen |
> | natgateways | Igen | Igen |
> | networkintentpolicies | Igen | Igen |
> | networkinterfaces | Igen | Igen |
> | networkprofiles | Nem | Nem |
> | networksecuritygroups | Igen | Igen |
> | networkwatchers | Igen | Igen |
> | networkwatchers / connectionmonitors | Igen | Igen |
> | networkwatchers/objektívek | Igen | Igen |
> | networkwatchers / pingmeshes | Igen | Igen |
> | p2svpngateways | Nem | Nem |
> | privatednszones | Igen | Igen |
> | privatednszones / virtualnetworklinks | Igen | Igen |
> | privateendpoints | Nem | Nem |
> | privatelinkservices | Nem | Nem |
> | nyilvános IP | Igen – alapszintű SKU<br>Nem szabványos SKU | Igen – alapszintű SKU<br>Nem szabványos SKU |
> | publicipprefixes | Igen | Igen |
> | routefilters | Nem | Nem |
> | routetables | Igen | Igen |
> | securegateways | Igen | Igen |
> | serviceendpointpolicies | Igen | Igen |
> | trafficmanagerprofiles | Igen | Igen |
> | virtualhubs | Nem | Nem |
> | virtualnetworkgateways | Igen | Igen |
> | virtualnetworks | Igen | Igen |
> | virtualnetworktaps | Nem | Nem |
> | virtualwans | Nem | Nem |
> | vpngateways (virtuális WAN) | Nem | Nem |
> | vpnsites (virtuális WAN) | Nem | Nem |
> | webapplicationfirewallpolicies | Igen | Igen |

> [!IMPORTANT]
> Lásd: [hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |
> | névterek/notificationhubs | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Igen | Igen |

> [!IMPORTANT]
> Ügyeljen arra, hogy az új előfizetésre való áttérés ne haladja meg az [előfizetési kvótákat](../azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | managementconfigurations | Igen | Igen |
> | megoldások | Igen | Igen |
> | megtekintés | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | társviszonyok | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | irányítópultok | Igen | Igen |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | rootresources | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kapacitások | Igen | Igen |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | boltívek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | lekérdezés | Igen | Igen |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | forgalom | Igen | Igen |
> | feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | searchservices | Igen | Igen |

> [!IMPORTANT]
> Egy műveletben nem helyezhető át több keresési erőforrás különböző régiókban. Ehelyett külön műveletekben helyezze át őket.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | iotsecuritysolutions | Igen | Igen |
> | playbookconfigurations | Nem | Nem |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | átjárók | Nem | Nem |
> | csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Nem | Nem |
> | fürtök | Igen | Igen |
> | fürtök/alkalmazások | Nem | Nem |
> | containergroups | Nem | Nem |
> | containergroupsets | Nem | Nem |
> | edgeclusters | Nem | Nem |
> | hálózatok | Nem | Nem |
> | secretstores | Nem | Nem |
> | kötetek | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Igen |
> | containergroups | Nem | Nem |
> | átjárók | Igen | Igen |
> | hálózatok | Igen | Igen |
> | titkos kódok | Igen | Igen |
> | kötetek | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | signalr | Igen | Igen |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | siterecoveryvault | Nem | Nem |

> [!IMPORTANT]
> Lásd: [Recovery Services áthelyezési útmutató](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | appliancedefinitions | Nem | Nem |
> | berendezések | Nem | Nem |
> | applicationdefinitions | Nem | Nem |
> | alkalmazások | Nem | Nem |
> | jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | instancepools | Nem | Nem |
> | managedinstances | Nem | Nem |
> | managedinstances/adatbázisok | Nem | Nem |
> | kiszolgáló | Igen | Igen |
> | kiszolgálók/adatbázisok | Igen | Igen |
> | kiszolgálók/elasticpools | Igen | Igen |
> | virtualclusters | Igen | Igen |

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoporthoz kell tartoznia. Ha áthelyezi SQL-kiszolgáló, az összes hozzá tartozó adatbázisok is kerülnek. Ez a viselkedés az Azure SQL Database és az Azure SQL Data Warehouse-adatbázisok vonatkozik.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Igen | Igen |
> | sqlvirtualmachines | Igen | Igen |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dwvm | Nem | Nem |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Igen | Igen |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | gyorsítótárak | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Igen | Igen |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | kezelők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Igen | Igen |

> [!IMPORTANT]
> Stream Analytics feladatok futási állapotban nem helyezhetők át.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | környezetben | Nem | Nem |
> | környezetek/eventsources | Nem | Nem |
> | példányok | Nem | Nem |
> | példányok/környezetek | Nem | Nem |
> | példányok/környezetek/eventsources | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | providerregistrations | Nem | Nem |
> | erőforrások | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | környezetben | Igen | Igen |
> | környezetek/eventsources | Igen | Igen |
> | környezetek/referencedatasets | Igen | Igen |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | tárolja | Nem | Nem |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Nem | Nem |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | fiók | Igen | Igen |
> | fiók/bővítmény | Igen | Igen |
> | fiók/projekt | Igen | Igen |

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosításához tekintse meg [a számlázáshoz használt Azure-előfizetés módosítása](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)című témakört.

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Igen | Igen |
> | dedicatedcloudservices | Igen | Igen |
> | virtualmachines | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
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

> [!IMPORTANT]
> Lásd: [app Service áthelyezési útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | deviceservices | Nem | Nem |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Resource group | Subscription |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Nem | Nem |
> | hostpools | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>További lépések
Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy](resource-group-move-resources.md)előfizetésbe.

Ha ugyanazokat az adatokkal szeretné lekérni a vesszővel tagolt értékeket, töltse le a [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)fájlt.
