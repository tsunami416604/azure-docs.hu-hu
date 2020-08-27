---
title: Azure-erőforrások régiók közötti áthelyezésének támogatása
description: Az Azure-régiók között áthelyezhető Azure-erőforrástípusok listája
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 8c18a4d2fa6e5bdb211b77d4d7bb28af7e5b1c1a
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948113"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Azure-erőforrások régiók közötti áthelyezésének támogatása

Ez a cikk megerősíti, hogy egy Azure-erőforrástípus támogatott-e egy másik Azure-régióba való áthelyezéshez. 

Ugrás erőforrás-szolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft. HRE](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. Blockchain](#microsoftblockchain)
> - [Microsoft. Blueprint](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
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
> - [Microsoft. DataShare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. segédösszetevője](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
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
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. Migrálás](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. Recoveryservices szolgáltatónál](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
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
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft. HRE

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- | 
> | domainservices | Nem | 
> | domainservices / replicasets | Nem | 

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | bérlők | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | actionrules | Nem | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Nem |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | szolgáltatás |  Igen (sablon használatával) <br/><br/> [API Management áthelyezése a régiók között](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | configurationstores | Nem | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | apiapps | Igen (sablon használatával)<br/><br/> [App Service alkalmazás áthelyezése másik régióba](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Nem | 
> | átjárók | Nem | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | policyassignments | Nem |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | automationaccounts | Igen (sablon használatával) <br/><br/> [Geo-replikáció használata](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurációk | Nem | 
> | automationaccounts/runbookok | Nem | 



## <a name="microsoftazureactivedirectory"></a>Microsoft. AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | b2cdirectories | Nem | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | sqlserverregistrations | Nem |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | regisztrációk | Nem | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | batchaccounts |  A Batch-fiókok nem helyezhetők át közvetlenül az egyik régióból a másikba, de sablon használatával exportálhat egy sablont, módosíthatja azt, és üzembe helyezheti a sablont az új régióban. <br/><br/> Tudnivalók [a Batch-fiókok régiók közötti áthelyezéséről](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem <br/><br/> A Azure Batch AI szolgáltatás ki van [vonva](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | fileservers | Nem | 
> | feladatok | Nem | 
> | munkaterületek | Nem | 

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | mapapis | Nem | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | biztalk | Nem | 

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blockchainmembers | Nem <br/><br/> A blockchain-hálózat nem rendelkezhet különböző régiókban található csomópontokkal. 
> | Watchers | Nem | 

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | blueprintassignments | Nem | 

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | botservices | Nem | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Redis | Nem | 


## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Nem |
> | profilok | Nem | 
> | profilok/végpontok | Nem | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tanúsítványrendelések | Nem | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartománynevek | A klasszikus szolgáltatások esetében nem terveznek munkát.
> | virtualmachines | Nem | 



## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | networksecuritygroups | A klasszikus szolgáltatások esetében nem terveznek munkát.
> | reservedips | Nem | 
> | virtualnetworks | Nem | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storageaccounts | Igen |  


## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | Cognitive Search | Manuális lépések esetén támogatott.<br/><br/> Ismerje meg [, hogyan helyezheti át Azure Cognitive Search szolgáltatását egy másik régióba](../../search/search-howto-move-across-regions.md)

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | availabilitysets | Nem | 
> | diskencryptionsets | Nem | 
> | lemezek | Nem | 
> | katalógusok | Nem | 
> | galériák/lemezképek | Nem | 
> | galériák/lemezképek/verziók | Nem | 
> | hostgroups | Nem | 
> | hostgroups/gazdagépek | Nem | 
> | images | Nem | 
> | proximityplacementgroups | Nem | 
> | restorepointcollections | Nem | 
> | sharedvmimages | Nem | 
> | sharedvmimages/verziók | Nem | 
> | pillanatképek | Nem | 
> | virtualmachines | Igen | 
> | virtualmachines/bővítmények | Nem | 
> | virtualmachinescalesets | Nem | 

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containergroups | Nem | 

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containergroups | Nem | 

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kibocsátásiegység | Nem |  
> | kibocsátásiegység-forgalmi jegyzékek/buildtasks | Nem |  
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | Nem | 
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | Nem |  
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | Nem | 

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | containerservices | Nem.<br/><br/> [A szolgáltatás](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)kivonásra kerül.
> | managedclusters | Nem | 
> | openshiftmanagedclusters | Nem | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | Nem | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | összekötők | Nem |  

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Hubs | Nem |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | resourceproviders | Nem | 

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | feladatok | Nem | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | databoxedgedevices | Nem | 

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | Nem | 

## <a name="microsoftdatacatalog"></a>Microsoft. DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | katalógusok | Nem | 
> | datacatalogs | Nem | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | connectionmanagers | Nem | 

## <a name="microsoftdataexchange"></a>Microsoft. DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | csomagok | Nem | 
> | tervek | Nem | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datafactories | Nem | 
> | előállítók | Nem |  

## <a name="microsoftdatalake"></a>Microsoft. DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datalakeaccounts | Nem | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem | 
> | szolgáltatások/projektek | Nem | 
> | bővítőhely | Nem | 

## <a name="microsoftdatashare"></a>Microsoft. DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../postgresql/howto-move-regions-portal.md).<br/><br/> Ha a szolgáltatás a Geo-redundáns biztonsági mentési tárolóval van kiépítve, a Geo-visszaállítás használatával más régiókban is visszaállítható. [További információ](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | servergroups | Nem | 
> | kiszolgálók | Egy meglévő kiszolgáló áthelyezéséhez a régiók közötti olvasási replikát használhat. [További információ](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Nem | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | artifactsources | Nem | 
> | kibocsátások | Nem |  
> | servicetopologies | Nem | 
> | servicetopologies/szolgáltatások | Nem |  
> | servicetopologies/szolgáltatások/serviceunits | Nem | 
> | lépések | Nem | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | elasticpools | Nem. Az erőforrás nincs kitéve.
> | elasticpools / iothubtenants | Nem. Az erőforrás nincs kitéve.
> | iothubs | Igen. [További információ](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Nem | 

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományvezérlők | Nem | 
> | AK-fürt | Nem<br/><br/> [További](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) információ a másik régióba való áttérésről.

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | labcenters | Nem | 
> | Labs | Nem | 
> | Labs/környezetek | Nem |  
> | Labor/servicerunners | Nem | 
> | Labor/virtualmachines | Nem |  
> | menetrend | Nem |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | databaseaccounts | Nem | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományok | Nem | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tartományok | Nem |  
> | témakörök | Nem | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem |  
> | névterek | Igen (sablonnal)<br/><br/> [Event hub-névtér áthelyezése másik régióba](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hanainstances | Nem | 
> | sapmonitors | Nem |  

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök | Nem | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | services | Nem |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | gépek | Nem | 

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | datamanagers |  Nem | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | feladatok |  Nem | 

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | actiongroups |  Nem | 
> | activitylogalerts | Nem | 
> | alertrules |  Nem | 
> | autoscalesettings |  Nem | 
> | összetevők |  Nem |  
> | guestdiagnosticsettings | Nem | 
> | metricalerts | Nem | 
> | notificationgroups | Nem | 
> | notificationrules | Nem | 
> | scheduledqueryrules |  Nem | 
> | webteszteket |  Nem | 
> | munkafüzetek |  Nem |  


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | checknameavailability |  Nem.<br/><br/> A IoT Central földrajzi és nem régiókkal működik.
> | Graph | Nem

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> |  iothub |  Igen (klónozási központ) <br/><br/> [IoT-központ klónozása egy másik régióba](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | checknameavailability |  Nem |  
> | Graph |  Nem | 

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hsmpools | Nem | 
> | boltívek |  Nem | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fürtök |  Nem |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | labaccounts | Nem | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem, ez egy globális szolgáltatás.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hostingenvironments | Nem | 
> | integrationaccounts |  Nem |  
> | integrationserviceenvironments | Nem | 
> | isolatedenvironments | Nem | 
> | munkafolyamatok |  Nem |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | commitmentplans |  Nem | 
> | WebServices |  Nem | 
> | munkaterületek |  Nem | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | operationalizationclusters |  Nem | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 
> | fiókok/munkaterületek | Nem | 
> | fiókok/munkaterületek/projektek | Nem | 
> | teamaccounts | Nem | 
> | teamaccounts/munkaterületek | Nem | 
> | teamaccounts/munkaterületek/projektek | Nem | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | hostingaccounts | Nem | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek | Nem | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | userassignedidentities | Nem | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok |  Nem, Azure Maps térinformatikai szolgáltatás. 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | classicdevservices | A klasszikus szolgáltatásokhoz nem terveztek munkát 

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | Mediaservices |  Nem | 
> | Mediaservices/liveevents |  Nem | 
> | Mediaservices/streamingendpoints |  Nem | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appclusters | Nem | 

## <a name="microsoftmigrate"></a>Microsoft. Migrálás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | assessmentprojects | Nem | 
> | migrateprojects | Nem | 
> | projektek | Nem | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | netappaccounts | Nem | 
> | netappaccounts / capacitypools | Nem | 
> | netappaccounts/capacitypools/kötetek | Nem | 
> | netappaccounts/capacitypools/kötetek/mounttargets | Nem | 
> | netappaccounts/capacitypools/kötetek/Pillanatképek | Nem | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | applicationgateways | Nem | 
> | applicationgatewaywebapplicationfirewallpolicies | Nem | 
> | applicationsecuritygroups |  Nem |  
> | azurefirewalls |  Nem |  
> | bastionhosts | Nem | 
> | kapcsolatok |  Nem | 
> | ddoscustompolicies |  Nem | 
> | ddosprotectionplans | Nem | 
> | dnszones |  Nem | 
> | expressroutecircuits | Nem | 
> | expressroutecrossconnections | Nem | 
> | expressroutegateways | Nem | 
> | expressrouteports | Nem | 
> | frontdoors | Nem | 
> | frontdoorwebapplicationfirewallpolicies | Nem | 
> | loadbalancers | Igen <br/><br/> Exportálhatja a meglévő konfigurációt sablonként, és üzembe helyezheti a sablont az új régióban. Ismerje meg, hogyan helyezhető át [külső](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) vagy [belső](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) terheléselosztó. |
> | localnetworkgateways |  Nem | 
> | natgateways |  Nem | 
> | networkintentpolicies |  Nem | 
> | networkinterfaces | Igen | 
> | networkprofiles | Nem | 
> | networksecuritygroups | Igen | 
> | networkwatchers |  Nem |  
> | networkwatchers / connectionmonitors |  Nem | 
> | networkwatchers/objektívek |  Nem | 
> | networkwatchers / pingmeshes |  Nem | 
> | p2svpngateways | Nem | 
> | privatednszones |  Nem |  
> | privatednszones / virtualnetworklinks |  Nem |  
> | privateendpoints | Nem | 
> | privatelinkservices | Nem | 
> | nyilvános IP | Igen<br/><br/> Exportálhatja a meglévő nyilvános IP-cím konfigurációját sablonként, és üzembe helyezheti a sablont az új régióban. [További](../../virtual-network/move-across-regions-publicip-portal.md) információ a nyilvános IP-címek áthelyezéséről. |
> | publicipprefixes | Nem | 
> | routefilters | Nem | 
> | routetables |  Nem | 
> | serviceendpointpolicies |  Nem | 
> | trafficmanagerprofiles |  Nem | 
> | virtualhubs | Nem | 
> | virtualnetworkgateways |  Nem |  
> | virtualnetworks |  Nem | 
> | virtualnetworktaps | Nem | 
> | virtualwans | Nem | 
> | vpngateways (virtuális WAN) | Nem | 
> | vpnsites (virtuális WAN) | Nem | 
> | webapplicationfirewallpolicies |  Nem | 


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  Nem | 
> | névterek/notificationhubs |  Nem |  

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | munkaterületek |  Nem | 



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | managementconfigurations |  Nem | 
> | kilátással |  Nem | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | társviszonyok | Nem | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | irányítópultok | Nem | 

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | rootresources | Nem | 

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | workspacecollections |  Nem | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kapacitások |  Nem | 

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | fiókok | Nem | 

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | boltívek | Nem.<br/><br/> Az Azure-régiók Azure Backup Recovery Services-tárolóinak áthelyezése nem támogatott.<br/><br/> Azure Site Recovery Recovery Services-tárolójában [letilthatja és újból létrehozhatja](../../site-recovery/move-vaults-across-regions.md) a tárolót a célhelyen. | 


## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  Nem | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | lekérdezések |  Nem |  

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése |
> | ------------- | ----------- |
> | deploymentScripts |  Igen<br/><br/>[A Microsoft. Resources erőforrásainak áthelyezése új régióba](microsoft-resources-move-regions.md) |
> | templateSpecs |  Igen<br/><br/>[A Microsoft. Resources erőforrásainak áthelyezése új régióba](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  Nem | 

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | folyamatok |  Nem |  
> | feladatgyűjtemények |  Nem | 

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | searchservices |  Nem | 


## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Nem | 
> | playbookconfigurations | Nem | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | átjárók | Nem | 
> | csomópontok | Nem | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | névterek |  Nem | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások | Nem | 
> | fürtök |  Nem | 
> | fürtök/alkalmazások | Nem | 
> | containergroups | Nem | 
> | containergroupsets | Nem | 
> | edgeclusters | Nem | 
> | hálózatok | Nem | 
> | secretstores | Nem | 
> | volumes | Nem | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | alkalmazások |  Nem | 
> | containergroups | Nem | 
> | átjárók |  Nem | 
> | hálózatok |  Nem | 
> | titkok |  Nem | 
> | volumes |  Nem |  

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | signalr |  Nem |  

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | appliancedefinitions | Nem | 
> | berendezések | Nem | 
> | applicationdefinitions | Nem | 
> | alkalmazások | Nem | 
> | jitrequests | Nem | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | instancepools | Nem | 
> | managedinstances | Igen | 
> | managedinstances/adatbázisok | Igen | 
> | kiszolgálók | Igen | 
> | kiszolgálók/adatbázisok | Igen | 
> | kiszolgálók/elasticpools | Igen | 
> | virtualclusters | Igen | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Nem |  
> | sqlvirtualmachines |  Nem |  

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | dwvm | Nem | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storageaccounts | Igen<br/><br/> [Azure Storage-fiók áthelyezése másik régióba](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | gyorsítótárak | Nem | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices |  Nem | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices | Nem | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | storagesyncservices | Nem | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | kezelők | Nem | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | streamingjobs |  Nem |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | környezetben | Nem | 
> | környezetek/eventsources | Nem | 
> | esetben | Nem | 
> | példányok/környezetek | Nem | 
> | példányok/környezetek/eventsources | Nem | 

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | providerregistrations | Nem | 
> | resources | Nem | 

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | környezetben |  Nem | 
> | környezetek/eventsources |  Nem |  
> | környezetek/referencedatasets |  Nem | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tárolja | Nem | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | imagetemplates | Nem | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | account |  Nem | 
> | fiók/bővítmény |  Nem | 
> | fiók/projekt |  Nem | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Nem | 
> | dedicatedcloudservices | Nem | 
> | virtualmachines | Nem | 

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | tanúsítványok | Nem | 
> | connectiongateways |  Nem |  
> | kapcsolatok |  Nem |  
> | customapis |  Nem | 
> | hostingenvironments | Nem | 
> | kiszolgálófarmok |  Nem |  
> | helyek |  Nem | 
> | helyek/premieraddons |  Nem |  
> | helyek/bővítőhelyek |  Nem |  


## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | deviceservices | Nem | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Régió áthelyezése | 
> | ------------- | ----------- |
> | applicationgroups | Nem | 
> | hostpools | Nem | 
> | munkaterületek | Nem | 

## <a name="third-party-services"></a>Harmadik féltől származó szolgáltatások

A harmadik féltől származó szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.
