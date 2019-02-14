---
title: Művelet támogatási áthelyezése az Azure-erőforrástípus szerint
description: Az Azure-erőforrástípus, amely egy új erőforráscsoportot vagy előfizetést is áthelyezhető sorolja fel.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 2/13/2019
ms.author: tomfitz
ms.openlocfilehash: f82c3adc0049c7386206fb4be10d3e634627a25a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236889"
---
# <a name="move-operation-support-for-resources"></a>Művelet támogatási erőforrások áthelyezése
Ez a cikk felsorolja egy Azure-erőforrás az áthelyezési művelet támogatja-e. Bár egy erőforrás támogatja az áthelyezési művelet, előfordulhat, hogy feltételeket, amelyek megakadályozhatja, hogy az erőforrás áthelyezett lehet. Feltételeket, amelyek hatással vannak az áthelyezési műveleteket kapcsolatos részletekért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

Első ugyanazokat az adatokat egy vesszővel tagolt formátumú fájlt, töltse le a [move-támogatás – resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| domainservices | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Bérlők | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| kiszolgáló | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| szolgáltatás | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| apiapps | Nem | Nem |
| appidentities | Nem | Nem |
| Átjárók | Nem | Nem |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| policyassignments | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| automationaccounts | Igen | Igen |
| automationaccounts/konfigurációk | Igen | Igen |
| automationaccounts/runbooks | Igen | Igen |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| b2cdirectories | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| regisztrációk | Igen | Igen |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| biztonsági mentési tár | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| batchaccounts | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Fürtök | Nem | Nem |
| fileservers | Nem | Nem |
| feladatok | Nem | Nem |
| munkaterületek | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| mapapis | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| biztalk | Igen | Igen |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| blockchainmembers | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| blueprintassignments | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Redis Cache | Igen | Igen |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Profilok | Igen | Igen |
| profilok és végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tanúsítványrendelések | Igen | Igen |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| domainnames | Igen | Nem |
| virtuális gép | Igen | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Nem | Nem |
| reservedips | Nem | Nem |
| virtualnetworks | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tárfiókok | Igen | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| availabilitysets | Igen | Igen |
| Lemezek | Igen | Igen |
| katalógusok | Nem | Nem |
| galériák vagy-képekből | Nem | Nem |
| galériák/képek/verziója | Nem | Nem |
| images | Igen | Igen |
| proximityplacementgroups | Nem | Nem |
| restorepointcollections | Nem | Nem |
| sharedvmimages | Nem | Nem |
| sharedvmimages/verziója | Nem | Nem |
| pillanatképek | Igen | Igen |
| virtuális gép | Igen | Igen |
| virtuális gép/bővítmények | Igen | Igen |
| virtualmachinescalesets | Igen | Igen |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| containergroups | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| containergroups | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| beállításjegyzékek | Igen | Igen |
| registries/buildtasks | Igen | Igen |
| beállításjegyzékek/replikációk | Nem | Nem |
| beállításjegyzékek/feladatok | Igen | Igen |
| beállításjegyzékek és webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| containerservices | Nem | Nem |
| managedclusters | Nem | Nem |
| openshiftmanagedclusters | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Igen |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| összekötők | Igen | Igen |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| hubok | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| katalógusok | Igen | Igen |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| connectionmanagers | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Csomagok | Nem | Nem |
| Csomagok | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| datafactories | Igen | Igen |
| gyárak | Igen | Igen |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| services | Nem | Nem |
| Services-projektek | Nem | Nem |
| tárhelyek | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| servergroups | Nem | Nem |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| artifactsources | Nem | Nem |
| Kibocsátások | Nem | Nem |
| servicetopologies | Nem | Nem |
| servicetopologies/szolgáltatások | Nem | Nem |
| servicetopologies/services/serviceunits | Nem | Nem |
| lépések | Nem | Nem |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| elasticpools | Nem | Nem |
| elasticpools/iothubtenants | Nem | Nem |
| iothubs | Igen | Igen |
| provisioningservices | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Tartományvezérlők | Nem | Nem |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| labcenters | Nem | Nem |
| Laborgyakorlatok | Igen | Nem |
| labs/servicerunners | Igen | Igen |
| laborok/virtuális gép | Igen | Nem |
| Ütemezések | Nem | Nem |

## <a name="microsoftdns"></a>microsoft.dns
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
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
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tartományok | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tartományok | Igen | Igen |
| kapcsolatos témakörök | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Fürtök | Igen | Igen |
| névterek | Igen | Igen |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| hanainstances | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Fürtök | Igen | Igen |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| datamanagers | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>microsoft.insights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
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
| scheduledqueryrules | Nem | Nem |
| webteszt | Igen | Igen |
| munkafüzetek | Igen | Igen |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| checknameavailability | Igen | Igen |
| gráf | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| hsmpools | Nem | Nem |
| tárolók | Igen | Igen |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Fürtök | Igen | Igen |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| labaccounts | Igen | Igen |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| hostingenvironments | Nem | Nem |
| integrationaccounts | Igen | Igen |
| integrationserviceenvironments | Nem | Nem |
| isolatedenvironments | Nem | Nem |
| A munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| commitmentplans | Igen | Igen |
| problémák megoldásához segítséget | Igen | Nem |
| munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |
| fiókok és munkaterületek | Igen | Igen |
| fiókok/munkaterületeket és projekteket | Igen | Igen |
| teamaccounts | Igen | Igen |
| teamaccounts/munkaterületek | Igen | Igen |
| teamaccounts/munkaterületeket és projekteket | Igen | Igen |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| hostingaccounts | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| munkaterületek | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| userassignedidentities | Igen | Igen |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| classicdevservices | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| mediaservices | Igen | Igen |
| mediaservices/liveevents | Igen | Igen |
| mediaservices/streamvégpontok | Igen | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| assessmentprojects | Nem | Nem |
| migrateprojects | Nem | Nem |
| projektek | Nem | Nem |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| netappaccounts | Nem | Nem |
| netappaccounts/capacitypools | Nem | Nem |
| netappaccounts/capacitypools/volumes | Nem | Nem |
| netappaccounts/capacitypools/volumes/mounttargets | Nem | Nem |
| netappaccounts/capacitypools/volumes/snapshots | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| applicationgateways | Nem | Nem |
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
| frontdoors | Igen | Igen |
| frontdoorwebapplicationfirewallpolicies | Igen | Igen |
| interfaceendpoints | Nem | Nem |
| loadbalancers | Igen | Igen |
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
| privatelinkservices | Nem | Nem |
| publicipaddresses | Igen | Igen |
| publicipprefixes | Igen | Igen |
| routefilters | Nem | Nem |
| routetables | Igen | Igen |
| securegateways | Nem | Nem |
| serviceendpointpolicies | Igen | Igen |
| trafficmanagerprofiles | Igen | Igen |
| virtualhubs | Nem | Nem |
| virtualnetworkgateways | Igen | Igen |
| virtualnetworks | Igen | Igen |
| virtualnetworktaps | Nem | Nem |
| virtualwans | Nem | Nem |
| vpngateways | Nem | Nem |
| vpnsites | Igen | Igen |
| webapplicationfirewallpolicies | Igen | Igen |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| névterek | Igen | Igen |
| névtér/notificationhubs | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| munkaterületek | Igen | Igen |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| managementconfigurations | Igen | Igen |
| megoldások | Igen | Igen |
| megtekintés | Igen | Igen |

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Az irányítópultok | Igen | Igen |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| rootresources | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Kapacitások | Igen | Igen |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| fiókok | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tárolók | Igen | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| névterek | Igen | Igen |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| forgalom | Igen | Igen |
| feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| searchservices | Igen | Igen |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Átjárók | Nem | Nem |
| csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
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
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| alkalmazások | Igen | Igen |
| containergroups | Nem | Nem |
| Átjárók | Igen | Igen |
| Hálózatok | Igen | Igen |
| titkos kódok | Igen | Igen |
| volumes | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| a signalr | Igen | Igen |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Nem | Nem |
| készülékek | Nem | Nem |
| applicationdefinitions | Nem | Nem |
| alkalmazások | Nem | Nem |
| jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| managedinstances | Igen | Igen |
| managedinstances/adatbázis | Igen | Igen |
| kiszolgáló | Igen | Igen |
| kiszolgálók és adatbázisok | Igen | Igen |
| servers/elasticpools | Igen | Igen |
| kiszolgálók/jobaccounts | Nem | Nem |
| kiszolgálók/jobagents | Nem | Nem |
| virtualclusters | Igen | Igen |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Igen | Igen |
| sqlvirtualmachines | Igen | Igen |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| dwvm | Nem | Nem |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tárfiókok | Igen | Igen |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| storagesyncservices | Igen | Igen |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| storagesyncservices | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| vezetők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| streamingjobs | Igen | Igen |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Környezetek | Nem | Nem |
| környezetek/eventsources | Nem | Nem |
| példányok | Nem | Nem |
| példányok/környezetek | Nem | Nem |
| példányok/környezetek/eventsources | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| providerregistrations | Nem | Nem |
| erőforrások | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| Környezetek | Igen | Igen |
| környezetek/eventsources | Igen | Igen |
| környezetek/referencedatasets | Igen | Igen |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| imagetemplates | Nem | Nem |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| account | Igen | Igen |
| fiók/bővítmény | Igen | Igen |
| fiók és a projekt | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| tanúsítványok | Nem | Igen |
| connectiongateways | Igen | Igen |
| kapcsolatok | Igen | Igen |
| customapis | Igen | Igen |
| hostingenvironments | Nem | Nem |
| serverfarms | Igen | Igen |
| Helyek | Igen | Igen |
| webhelyek/premieraddons | Igen | Igen |
| helyek és tárhelyek | Igen | Igen |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | ----------- | ---------- |
| deviceservices | Igen | Igen |

## <a name="third-party-services"></a>Külső szolgáltatások

Külső szolgáltatások jelenleg nem támogatják az áthelyezési művelet.

## <a name="next-steps"></a>További lépések
Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
