---
title: Az Azure-erőforrások régiók közötti áthelyezésének támogatása
description: Az Azure-régiókközött áthelyezhető Azure-erőforrástípusok listája
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760708"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Az Azure-erőforrások régiók közötti áthelyezésének támogatása

Ez a cikk megerősíti, hogy egy Azure-erőforrás-típus támogatott-e egy másik Azure-régióba való áthelyezéshez. 

Ugrás erőforrásszolgáltatói névtérre:
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
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.Tanúsítványregisztráció](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerátor](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
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
> - [Microsoft.MachineLearningKísérletezation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Tavasz](#microsoftmicroservices4spring)
> - [Microsoft.Áttelepítés](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Társviszony-létesítés](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Keresés](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Megoldások](#microsoftsolutions)
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

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | tartományi szolgáltatások | Nem | 
> | domainservices / replikakészletek | Nem | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Bérlők | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | műveletszabályok | Nem | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Szerverek | Nem |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | szolgáltatás |  Igen | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | konfigurációs üzletek | Nem | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | apiapps | Nem | 
> | alkalmazásazonosítók | Nem | 
> | Átjárók | Nem | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | házirend-hozzárendelések | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | automationaccounts | Nem | 
> | automationaccounts / konfigurációk | Nem | 
> | automationaccounts / runbookok | Nem | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | b2ckönyvtárak | Nem | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | sqlserverregistrations | Nem |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Regisztrációk | Nem | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kötegszámlák | Nem |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Klaszterek | Nem | 
> | fájlkiszolgálók | Nem | 
> | Munkahelyek | Nem | 
> | munkaterületek | Nem | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | mapapis | Nem | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | biztalk | Nem | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blockchainmembers | Nem |
> | Watchers | Nem | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blueprintassignments | Nem | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | botservices (botservices) | Nem | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | redis között | Nem | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nem |
> | Profilok | Nem | 
> | profilok / végpontok | Nem | 

## <a name="microsoftcertificateregistration"></a>Microsoft.Tanúsítványregisztráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tanúsítványok sorrendjed | Nem | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartománynevek | Nem |  
> | virtuális gépek | Nem | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | networksecuritygroups | Nem |
> | tartalékdipok | Nem | 
> | virtuális hálózatok | Nem | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolók | Igen |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | elérhetőségi készletek | Nem | 
> | lemeztitkosítási készletek | Nem | 
> | Lemezek | Nem | 
> | Galériák | Nem | 
> | galériák / képek | Nem | 
> | galériák / képek / verziók | Nem | 
> | gazdacsoportok | Nem | 
> | gazdacsoportok / állomások | Nem | 
> | images | Nem | 
> | közelségbehelyezési csoportok | Nem | 
> | restorepointcollections (visszaállítási pontgyűjtemények) | Nem | 
> | sharedvmimages | Nem | 
> | sharedvmimages / verziók | Nem | 
> | pillanatképek | Nem | 
> | virtuális gépek | Igen | 
> | virtualmachines / kiterjesztések | Nem | 
> | virtualgép-mérlegek | Nem | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolócsoportok | Nem | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolócsoportok | Nem | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Nyilvántartások | Nem |  
> | nyilvántartások / buildfeladatok | Nem |  
> | nyilvántartások / replikációk | Nem | 
> | nyilvántartások / feladatok | Nem |  
> | nyilvántartások / webhookok | Nem | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containerservices (tárolószolgáltatások) | Nem | 
> | kezelt klaszterek | Nem | 
> | openshiftmanagedclusters | Nem | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerátor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | Nem | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | összekötők | Nem |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Csomópontok | Nem |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | erőforrás-szolgáltatók | Nem | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Munkahelyek | Nem | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | databoxedgedevices (databoxedgedevices) | Nem | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | Nem | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Katalógusok | Nem | 
> | adatkatalógusok | Nem | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kapcsolatkezelők | Nem | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Csomagok | Nem | 
> | Tervek | Nem | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | adatgyárak | Nem | 
> | Gyárak | Nem |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datalakeaccounts | Nem | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem | 
> | szolgáltatások / projektek | Nem | 
> | Bővítőhely | Nem | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Szerverek | Nem |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Szerverek | Nem |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálócsoportok | Nem | 
> | Szerverek | Nem |  
> | kiszolgálókv2 | Nem | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | műtermék-források | Nem | 
> | bevezetés | Nem |  
> | servicetopológiák | Nem | 
> | servicetopológiák / szolgáltatások | Nem |  
> | servicetopológiák / szolgáltatások / serviceunits | Nem | 
> | lépések | Nem | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | rugalmaskészletek | Nem | 
> | elasticpools / iothubtenants | Nem | 
> | iothubs | Igen | 
> | kiépítési szolgáltatások | Nem | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Vezérlők | Nem | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | laborközpontok | Nem | 
> | Labs | Nem | 
> | laborok / környezetek | Nem |  
> | laborok / servicerunners | Nem | 
> | laborok / virtualmachines | Nem |  
> | Menetrend | Nem |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | adatbázisfiókok | Nem | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Tartományok | Nem | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Tartományok | Nem |  
> | Témák | Nem | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Klaszterek | Nem |  
> | Névterek | Nem | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hanainstances (hanainstances) | Nem | 
> | sapmonitorok | Nem |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Klaszterek | Nem | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Gépek | Nem | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | adatkezelők |  Nem | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Munkahelyek |  Nem | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | műveletcsoportok |  Nem | 
> | activitylogalerts | Nem | 
> | riasztási szabályok |  Nem | 
> | automatikus méretezhető beállítások |  Nem | 
> | Összetevők |  Nem |  
> | vendégdiagnosztikai beállítások | Nem | 
> | metrikus riasztások | Nem | 
> | értesítési csoportok | Nem | 
> | értesítési szabályok | Nem | 
> | scheduledqueryrules |  Nem | 
> | webtesztek |  Nem | 
> | munkafüzetek |  Nem |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | iotapps |  Nem |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | checknameavailability |  Nem |  
> | Grafikon |  Nem | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hsmpoolok | Nem | 
> | Boltívek |  Nem | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Klaszterek |  Nem |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | laborszámlák | Nem | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | üzemeltetési környezetek | Nem | 
> | integrációs számlák |  Nem |  
> | integrációsszolgáltatási környezetek | Nem | 
> | elszigetelt környezetek | Nem | 
> | Munkafolyamatok |  Nem |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kötelezettségvállalási tervek |  Nem | 
> | webszolgáltatások |  Nem | 
> | munkaterületek |  Nem | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | operationalizationklaszterek |  Nem | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningKísérletezation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | számlák / munkaterületek | Nem | 
> | számlák / munkaterületek / projektek | Nem | 
> | csapatszámlák | Nem | 
> | teamaccounts / munkaterületek | Nem | 
> | teamaccounts / munkaterületek / projektek | Nem | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárhelyszámlák | Nem | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | Nem | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | userassignedidentities | Nem | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok |  Nem |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | classicdevservices | Nem | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | médiaszolgáltatások |  Nem | 
> | mediaservices / élő események |  Nem | 
> | mediaservices / streamingendpoints |  Nem | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Tavasz

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazásfürtök | Nem | 

## <a name="microsoftmigrate"></a>Microsoft.Áttelepítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | értékelési projektek | Nem | 
> | projektek áttelepítése | Nem | 
> | Projektek | Nem | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | netappaccounts | Nem | 
> | netappaccounts / capacitypools | Nem | 
> | netappaccounts / capacitypools / kötetek | Nem | 
> | netappaccounts / capacitypools / kötetek / mounttargets | Nem | 
> | netappaccounts / capacitypools / kötetek / pillanatképek | Nem | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazásátjárók | Nem | 
> | applicationgatewaywebapplicationfirewallpolicies | Nem | 
> | alkalmazásbiztonsági csoportok |  Nem |  
> | azuretűzfalak |  Nem |  
> | bástyaházigazdák | Nem | 
> | Kapcsolatok |  Nem | 
> | ddoscustompolicies |  Nem | 
> | ddosprotectionplans (ddosprotectionplans) | Nem | 
> | dnszones között |  Nem | 
> | expresszroute-áramkörök | Nem | 
> | expressroutecrossconnections (expresszroutecrossconnections) | Nem | 
> | expresszroute-átjárók | Nem | 
> | expresszrouteportok | Nem | 
> | elülső ajtók | Nem | 
> | frontdoorwebapplicationfirewallpolicies | Nem | 
> | terheléselosztók | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat | Igen - Alapvető Termékváltozat<br> -Igen Standard SKU |
> | localnetworkgateways (helyihálózati átjárók) |  Nem | 
> | natgateways |  Nem | 
> | hálózati szándékházirendek |  Nem | 
> | hálózati interfészek | Igen | 
> | hálózati profilok | Nem | 
> | networksecuritygroups | Igen | 
> | networkwatchers (hálózati figyelők) |  Nem |  
> | networkwatchers / connectionmonitors |  Nem | 
> | networkwatchers / objektívek |  Nem | 
> | networkwatchers / pingmeshes |  Nem | 
> | p2svpngateways | Nem | 
> | privatednszones (magándnszones) |  Nem |  
> | privatednszones / virtualnetworklinks |  Nem |  
> | privateendpoints (magánvégpontok) | Nem | 
> | privatelinkservices | Nem | 
> | nyilvános ipcímek | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat |
> | nyilvános ipprefixek | Nem | 
> | útvonalszűrők | Nem | 
> | routetables között |  Nem | 
> | serviceendpointházirendek |  Nem | 
> | trafficmanagerprofiles (trafficmanagerprofiles) |  Nem | 
> | virtualhubok | Nem | 
> | virtuálishálózatok átjárói |  Nem |  
> | virtuális hálózatok |  Nem | 
> | virtuálishálózatok lehallgatása | Nem | 
> | virtualwans | Nem | 
> | vpngateways (Virtuális WAN) | Nem | 
> | vpnsites (Virtuális WAN) | Nem | 
> | webapplicationfirewallpolicies |  Nem | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Névterek |  Nem | 
> | névterek / értesítési központok |  Nem |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek |  Nem | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | felügyeleti konfigurációk |  Nem | 
> | Kilátás nyílik |  Nem | 

## <a name="microsoftpeering"></a>Microsoft.Társviszony-létesítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | társviszony | Nem | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | irányítópultok | Nem | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | rootresources (gyökérforrások) | Nem | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületi gyűjtemények |  Nem | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Kapacitások |  Nem | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Boltívek | Nem. [A tároló letiltása és](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) a webhely-helyreállítás újbóli létrehozása  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Névterek |  Nem | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Lekérdezések |  Nem |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  Nem | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | folyamatok |  Nem |  
> | munkagyűjtemények |  Nem | 

## <a name="microsoftsearch"></a>Microsoft.Keresés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | keresési szolgáltatások |  Nem | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Nem | 
> | forgatókönyvkonfigurációk | Nem | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Átjárók | Nem | 
> | Csomópontok | Nem | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Névterek |  Nem | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | Nem | 
> | Klaszterek |  Nem | 
> | fürtök / alkalmazások | Nem | 
> | tárolócsoportok | Nem | 
> | tárolócsoportok készletei | Nem | 
> | élhalmazok | Nem | 
> | Hálózatok | Nem | 
> | titkos üzletek | Nem | 
> | volumes | Nem | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  Nem | 
> | tárolócsoportok | Nem | 
> | Átjárók |  Nem | 
> | Hálózatok |  Nem | 
> | Titkok |  Nem | 
> | volumes |  Nem |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | jelző |  Nem |  

## <a name="microsoftsolutions"></a>Microsoft.Megoldások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appliancedefinitions | Nem | 
> | Készülékek | Nem | 
> | alkalmazásdefiníciók | Nem | 
> | alkalmazások | Nem | 
> | jitrequests | Nem | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | példánykészlet | Nem | 
> | kezelt példányok | Igen | 
> | managedinstances / adatbázisok | Igen | 
> | Szerverek | Igen | 
> | szerverek / adatbázisok | Igen | 
> | szerverek / elaszator | Igen | 
> | virtuális klaszterek | Igen | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nem |  
> | sqlvirtualmachines |  Nem |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | dwvm | Nem | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolók | Igen | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Elrejt | Nem | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices (tárolószinkronizálási szolgáltatások) |  Nem | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices (tárolószinkronizálási szolgáltatások) | Nem | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices (tárolószinkronizálási szolgáltatások) | Nem | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Vezetők | Nem | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | streamingjobs |  Nem |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Környezetben | Nem | 
> | környezetek / eseményforrások | Nem | 
> | Példányok | Nem | 
> | példányok / környezetek | Nem | 
> | példányok / környezetek / eseményforrások | Nem | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | szolgáltatóregisztrációk | Nem | 
> | resources | Nem | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Környezetben |  Nem | 
> | környezetek / eseményforrások |  Nem |  
> | környezetek / referenciaadatkészletek |  Nem | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Üzletek | Nem | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | képsablonok | Nem | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | account |  Nem | 
> | számla / kiterjesztés |  Nem | 
> | számla / projekt |  Nem | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | dedikált felhőnok | Nem | 
> | dedikáltfelhőszolgáltatások | Nem | 
> | virtuális gépek | Nem | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tanúsítványok | Nem | 
> | kapcsolatátjárók |  Nem |  
> | Kapcsolatok |  Nem |  
> | customapis (testreszabás) |  Nem | 
> | üzemeltetési környezetek | Nem | 
> | kiszolgálófarmok |  Nem |  
> | Helyek |  Nem | 
> | oldalak / premieraddons |  Nem |  
> | oldalak / bővítőhely |  Nem |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | eszközszolgáltatások | Nem | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazáscsoportok | Nem | 
> | hostpools | Nem | 
> | munkaterületek | Nem | 

## <a name="third-party-services"></a>Harmadik fél től származó szolgáltatások

A külső szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.
