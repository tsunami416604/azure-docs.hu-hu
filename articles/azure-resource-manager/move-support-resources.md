---
title: Helyezze át művelet támogatás Azure-erőforrástípus |} A Microsoft Docs
description: Az Azure-erőforrástípus, amely egy új erőforráscsoportot vagy előfizetést is áthelyezhető sorolja fel.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 770f8261b3036e847111d2181a4c843374da7d60
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969736"
---
# <a name="move-operation-support-for-resources"></a>Művelet támogatási erőforrások áthelyezése

Ez a cikk felsorolja egy Azure-erőforrás az áthelyezési művelet támogatja-e. Bár egy erőforrás támogatja az áthelyezési művelet, előfordulhat, hogy feltételeket, amelyek megakadályozhatja, hogy az erőforrás áthelyezett lehet. Feltételeket, amelyek hatással vannak az áthelyezési műveleteket kapcsolatos részletekért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="find-resource-provider-and-resource-type"></a>Erőforrás-szolgáltató és az erőforrás típusa

Annak megállapításához, hogy ha egy erőforrás helyezhető, meg kell keresnie az erőforrás-szolgáltató és az erőforrástípus.

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzureRmResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

A következő formátumban adja vissza az erőforrástípus `<resource-provider>/<resource-type-name>`. Ezt az értéket `Microsoft.OperationalInsights/workspaces` rendelkezik az erőforrás-szolgáltató **Microsoft.OperationalInsights** és az erőforrástípus neve **munkaterületek**.

Miután megtalálta az erőforrás-szolgáltató és az erőforrás típusa, ebben a cikkben a táblázatok használatával határozza meg, hogy az erőforrás típusát támogatja-e az áthelyezési művelet.

## <a name="microsoftaad"></a>Microsoft.AAD

| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | --------------- | ----------- |
| domainservices | Nem | Nem |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| kiszolgáló | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | --------------- | ----------- |
| szolgáltatás | Igen | Igen |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | --------------- | ----------- |
| policyassignments | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| automationaccounts | Igen | Igen |
| automationaccounts/konfigurációk | Igen | Igen |
| automationaccounts/runbookok | Igen | Igen |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| b2cdirectories | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| regisztrációk | Igen | Igen |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| biztonsági mentési tár | Nem | Nem |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| batchaccounts | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Fürtök | Nem | Nem |
| fileservers | Nem | Nem |
| feladatok | Nem | Nem |
| munkaterületek | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| mapapis | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| biztalk | Igen | Igen |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| blueprintassignments | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| botservices | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft.Cache
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Redis Cache | Igen | Igen |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Profilok | Igen | Igen |
| profilok és végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tanúsítványrendelések | Igen | Igen |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| domainnames | Igen | Nem |
| virtuális gép | Igen | Nem |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| networksecuritygroups | Nem | Nem |
| keskenyeknek | Nem | Nem |
| virtualnetworks | Nem | Nem |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tárfiókok | Igen | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| availabilitysets | Igen | Igen |
| Lemezek | Igen | Igen |
| katalógusok | Nem | Nem |
| galériák vagy-képekből | Nem | Nem |
| galériák/képek/verziója | Nem | Nem |
| images | Igen | Igen |
| restorepointcollections | Nem | Nem |
| sharedvmimages | Nem | Nem |
| sharedvmimages/verziója | Nem | Nem |
| pillanatképek | Igen | Igen |
| virtuális gép | Igen | Igen |
| virtuális gép/bővítmények | Igen | Igen |
| virtualmachinescalesets | Igen | Igen |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| containergroups | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| containergroups | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| beállításjegyzékek | Igen | Igen |
| beállításjegyzékek/buildtasks | Igen | Igen |
| beállításjegyzékek/replikációk | Nem | Nem |
| beállításjegyzékek/feladatok | Igen | Igen |
| beállításjegyzékek és webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| containerservices | Nem | Nem |
| managedclusters | Nem | Nem |
| openshiftmanagedclusters | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| alkalmazások | Igen | Igen |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| összekötők | Igen | Igen |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| hubok | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| feladatok | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| databoxedgedevices | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| katalógusok | Igen | Igen |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| datafactories | Igen | Igen |
| gyárak | Igen | Igen |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| services | Nem | Nem |
| Services-projektek | Nem | Nem |
| tárhelyek | Nem | Nem |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| kiszolgáló | Nem | Nem |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| servergroups | Nem | Nem |
| kiszolgáló | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| artifactsources | Nem | Nem |
| Kibocsátások | Nem | Nem |
| servicetopologies | Nem | Nem |
| servicetopologies/szolgáltatások | Nem | Nem |
| servicetopologies/services/serviceunits | Nem | Nem |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| iothubs | Igen | Igen |
| provisioningservices | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| labcenters | Nem | Nem |
| Laborgyakorlatok | Igen | Nem |
| laborok/servicerunners | Igen | Igen |
| laborok/virtuális gép | Igen | Nem |
| Ütemezések | Nem | Nem |

## <a name="microsoftdns"></a>Microsoft.DNS
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| dnszones | Nem | Nem |
| dnszones/egy | Nem | Nem |
| dnszones/aaaa | Nem | Nem |
| dnszones/cname | Nem | Nem |
| dnszones/mx | Nem | Nem |
| dnszones/ptr | Nem | Nem |
| dnszones/srv | Nem | Nem |
| dnszones/txt | Nem | Nem |
| trafficmanagerprofiles | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| databaseaccounts | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tartományok | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| kapcsolatos témakörök | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Fürtök | Igen | Igen |
| Névterek | Igen | Igen |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| hanainstances | Igen | Igen |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| dedicatedhsms | Nem | Nem |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Fürtök | Igen | Igen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| feladatok | Igen | Igen |

## <a name="microsoftinsights"></a>Microsoft.insights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| actiongroups | Igen | Igen |
| activitylogalerts | Nem | Nem |
| alertrules | Igen | Igen |
| autoscalesettings beállítás | Igen | Igen |
| Összetevők | Igen | Igen |
| metricalerts | Nem | Nem |
| scheduledqueryrules | Igen | Igen |
| webteszt | Igen | Igen |
| munkafüzetek | Igen | Igen |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| iotapps | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tárolók | Igen | Igen |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| labaccounts | Igen | Igen |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| integrationaccounts | Igen | Igen |
| A munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| commitmentplans | Igen | Igen |
| problémák megoldásához segítséget | Igen | Nem |
| munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Igen | Igen |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |
| fiókok és munkaterületek | Igen | Igen |
| fiókok/munkaterületeket és projekteket | Igen | Igen |
| teamaccounts | Igen | Igen |
| teamaccounts/munkaterületek | Igen | Igen |
| teamaccounts/munkaterületeket és projekteket | Igen | Igen |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| munkaterületek | Igen | Igen |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| userassignedidentities | Igen | Igen |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| fiókok | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| classicdevservices | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| mediaservices | Igen | Igen |
| mediaservices/liveevents | Igen | Igen |
| mediaservices/streamvégpontok | Igen | Igen |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| projektek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| applicationgateways | Nem | Nem |
| applicationsecuritygroups | Igen | Igen |
| azurefirewalls | Igen | Igen |
| kapcsolatok | Igen | Igen |
| ddosprotectionplans | Nem | Nem |
| dnszones | Igen | Igen |
| expressroutecircuits | Nem | Nem |
| expressroutecrossconnections | Nem | Nem |
| expressroutegateways | Nem | Nem |
| expressrouteports | Nem | Nem |
| frontdoors | Igen | Igen |
| frontdoorwebapplicationfirewallpolicies | Igen | Igen |
| interfaceendpoints | Nem | Nem |
| használatát | Igen | Igen |
| localnetworkgateways | Igen | Igen |
| networkintentpolicies | Igen | Igen |
| hálózati | Igen | Igen |
| networkprofiles | Nem | Nem |
| networksecuritygroups | Igen | Igen |
| networkwatchers | Igen | Igen |
| networkwatchers/connectionmonitors | Igen | Igen |
| networkwatchers/átvilágított felülettel | Igen | Igen |
| networkwatchers/pingmeshes | Igen | Igen |
| publicipaddresses | Igen | Igen |
| publicipprefixes | Igen | Igen |
| routefilters | Nem | Nem |
| routetables | Igen | Igen |
| serviceendpointpolicies | Igen | Igen |
| trafficmanagerprofiles | Igen | Igen |
| virtualhubs | Igen | Igen |
| virtualnetworkgateways-hez | Igen | Igen |
| virtualnetworks | Igen | Igen |
| virtualnetworktaps | Nem | Nem |
| virtualwans | Igen | Igen |
| vpngateways | Igen | Igen |
| vpnsites | Igen | Igen |
| webapplicationfirewallpolicies | Igen | Igen |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Névterek | Igen | Igen |
| névtér/notificationhubs | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| munkaterületek | Igen | Igen |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| managementconfigurations | Igen | Igen |
| megoldások | Igen | Igen |
| megtekintés | Igen | Igen |

## <a name="microsoftportal"></a>Microsoft.Portal
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Az irányítópultok | Igen | Igen |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| workspacecollections | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Kapacitások | Igen | Igen |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tárolók | Igen | Igen |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Névterek | Igen | Igen |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| alkalmazások | Igen | Nem |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| forgalom | Igen | Igen |
| feladatgyűjtemények | Igen | Igen |

## <a name="microsoftsearch"></a>Microsoft.Search
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| searchservices | Igen | Igen |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Fürtök | Igen | Igen |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| alkalmazások | Igen | Igen |
| Hálózatok | Igen | Igen |
| volumes | Igen | Igen |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| a signalr | Igen | Igen |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| siterecoveryvault | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| appliancedefinitions | Nem | Nem |
| készülékek | Nem | Nem |
| applicationdefinitions | Nem | Nem |
| alkalmazások | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| managedinstances | Igen | Igen |
| managedinstances/adatbázis | Igen | Igen |
| kiszolgáló | Igen | Igen |
| kiszolgálók és adatbázisok | Igen | Igen |
| servers/elasticpools | Igen | Igen |
| virtualclusters | Igen | Igen |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tárfiókok | Igen | Igen |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| storagesyncservices | Igen | Igen |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| vezetők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| streamingjobs | Igen | Igen |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| Környezetek | Igen | Igen |
| környezetek/eventsources | Igen | Igen |
| környezetek/referencedatasets | Igen | Igen |

## <a name="microsoftvisualstudio"></a>Microsoft.visualstudio
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| account | Igen | Igen |
| fiók/bővítmény | Igen | Igen |
| fiók és a projekt | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| tanúsítványok | Nem | Igen |
| classicmobileservices | Nem | Nem |
| connectiongateways | Igen | Igen |
| kapcsolatok | Igen | Igen |
| customapis | Igen | Igen |
| hostingenvironments | Nem | Nem |
| kiszolgálófarmok | Igen | Igen |
| Helyek | Igen | Igen |
| webhelyek/premieraddons | Igen | Igen |
| helyek és tárhelyek | Igen | Igen |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Erőforrás típusa | Erőforráscsoport | Előfizetés |
| ------------- | -------------- | ------------ |
| deviceservices | Igen | Igen |


## <a name="third-party-services"></a>Külső szolgáltatások

Külső szolgáltatások jelenleg nem támogatják az áthelyezési művelet. Ezek az erőforrás-szolgáltatók a következők:

* 84codes. CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>További lépések

* Erőforrások áthelyezése parancsokért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
