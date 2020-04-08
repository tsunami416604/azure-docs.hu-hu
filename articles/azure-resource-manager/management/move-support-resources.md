---
title: A művelettámogatás áthelyezése erőforrástípus szerint
description: Felsorolja az Azure-erőforrás-típusok, amelyek áthelyezhetők egy új erőforráscsoportba vagy előfizetésbe.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90fbec4dc076feb1fee8c38cf9757d3c5ddbafaf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804792"
---
# <a name="move-operation-support-for-resources"></a>Erőforrás-áthelyezési műveletek támogatása
Ez a cikk felsorolja, hogy egy Azure-erőforrástípus támogatja-e az áthelyezési műveletet. Az erőforrások áthelyezésekekéna során figyelembe vehetővé kell tenni a különleges feltételekkel kapcsolatos információkat is.

Ugrás erőforrásszolgáltatói névtérre:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.Számlázás](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.Tanúsítványregisztráció](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.Megismerés](#microsoftcognition)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Fogyasztás](#microsoftconsumption)
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
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Kísérletezés](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestKonfiguráció](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
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
> - [Microsoft.Karbantartás](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Tavasz](#microsoftmicroservices4spring)
> - [Microsoft.Áttelepítés](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Társviszony-létesítés](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Keresés](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
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
> - [Microsoft.Subscription](#microsoftsubscription)
> - [microsoft.support](#microsoftsupport)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadFigyelő](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartományi szolgáltatások | Nem | Nem |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Bérlők | Nem | Nem |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Konfigurációk | Nem | Nem |
> | Ajánlások | Nem | Nem |
> | elnyomások | Nem | Nem |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletszabályok | Igen | Igen |
> | riasztások | Nem | Nem |
> | riasztások összegzése | Nem | Nem |
> | smartdetectoralertrules | Igen | Igen |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Szerverek | Igen | Igen |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | szolgáltatás | Igen | Igen |

> [!IMPORTANT]
> Egy API Management szolgáltatás, amely be van állítva a felhasználás termékváltozat nem lehet áthelyezni.

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | konfigurációs üzletek | Igen | Igen |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Tavaszi | Igen | Igen |

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | apiapps | Nem | Nem |
> | alkalmazásazonosítók | Nem | Nem |
> | Átjárók | Nem | Nem |

> [!IMPORTANT]
> Lásd: [App Service move útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checkaccess (hozzáférés) | Nem | Nem |
> | denyassignments | Nem | Nem |
> | findorphanrolehozzárendelések | Nem | Nem |
> | Zárak | Nem | Nem |
> | Engedélyek | Nem | Nem |
> | házirend-hozzárendelések | Nem | Nem |
> | politikai definíciók | Nem | Nem |
> | policysetdefinitions | Nem | Nem |
> | szerepkör-hozzárendelések | Nem | Nem |
> | roleassignmentsusagemetrics | Nem | Nem |
> | szerepkör-meghatározások | Nem | Nem |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Igen | Igen |
> | automationaccounts / konfigurációk | Igen | Igen |
> | automationaccounts / runbookok | Igen | Igen |

> [!IMPORTANT]
> A Runbookoknak ugyanabban az erőforráscsoportban kell létezniük, mint az Automation-fióknak.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | b2ckönyvtárak | Igen | Igen |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hibridadat-kezelők | Nem | Nem |
> | postgresinstances (postgresinstances) | Nem | Nem |
> | sqlinstances | Nem | Nem |
> | sqlserverregistrations | Igen | Igen |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Regisztrációk | Igen | Igen |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kötegszámlák | Igen | Igen |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Klaszterek | Nem | Nem |
> | fájlkiszolgálók | Nem | Nem |
> | Munkahelyek | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftbilling"></a>Microsoft.Számlázás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | számlázási időszakok | Nem | Nem |
> | számlázási engedélyek | Nem | Nem |
> | billingrolehozzárendelések | Nem | Nem |
> | billingroledefinitions | Nem | Nem |
> | createbillingroleassignment | Nem | Nem |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | mapapis | Nem | Nem |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | biztalk | Nem | Nem |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Nem | Nem |
> | kordonok | Nem | Nem |
> | Watchers | Nem | Nem |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tokenszolgáltatások | Nem | Nem |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Nem | Nem |
> | Tervrajz | Nem | Nem |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | botservices (botservices) | Igen | Igen |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | redis között | Igen | Igen |

> [!IMPORTANT]
> Ha az Azure Cache for Redis-példány egy virtuális hálózattal van konfigurálva, a példány nem helyezhető át egy másik előfizetésbe. Lásd: [Hálózati áthelyezési korlátozások](./move-limitations/networking-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallpolicies | Igen | Igen |
> | Profilok | Igen | Igen |
> | profilok / végpontok | Igen | Igen |

## <a name="microsoftcertificateregistration"></a>Microsoft.Tanúsítványregisztráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tanúsítványok sorrendjed | Igen | Igen |

> [!IMPORTANT]
> Lásd: [App Service move útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tartománynevek | Igen | Nem |
> | virtuális gépek | Igen | Nem |

> [!IMPORTANT]
> Lásd: [Klasszikus üzembe helyezési útmutató.](./move-limitations/classic-model-move-limitations.md) Klasszikus üzembe helyezési erőforrások áthelyezhetők az előfizetések között az adott forgatókönyvre jellemző művelettel.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | networksecuritygroups | Nem | Nem |
> | tartalékdipok | Nem | Nem |
> | virtuális hálózatok | Nem | Nem |

> [!IMPORTANT]
> Lásd: [Klasszikus üzembe helyezési útmutató.](./move-limitations/classic-model-move-limitations.md) Klasszikus üzembe helyezési erőforrások áthelyezhetők az előfizetések között az adott forgatókönyvre jellemző művelettel.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárolók | Igen | Nem |

> [!IMPORTANT]
> Lásd: [Klasszikus üzembe helyezési útmutató.](./move-limitations/classic-model-move-limitations.md) Klasszikus üzembe helyezési erőforrások áthelyezhetők az előfizetések között az adott forgatókönyvre jellemző művelettel.

## <a name="microsoftcognition"></a>Microsoft.Megismerés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | syntheticsaccounts | Nem | Nem |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | elérhetőségi készletek | Igen | Igen |
> | lemeztitkosítási készletek | Nem | Nem |
> | Lemezek | Igen | Igen |
> | Galériák | Nem | Nem |
> | galériák / képek | Nem | Nem |
> | galériák / képek / verziók | Nem | Nem |
> | gazdacsoportok | Nem | Nem |
> | gazdacsoportok / állomások | Nem | Nem |
> | images | Igen | Igen |
> | közelségbehelyezési csoportok | Igen | Igen |
> | restorepointcollections (visszaállítási pontgyűjtemények) | Nem | Nem |
> | sharedvmextensions | Nem | Nem |
> | sharedvmimages | Nem | Nem |
> | sharedvmimages / verziók | Nem | Nem |
> | pillanatképek | Igen | Igen |
> | ssh nyilvános kulcsok | Nem | Nem |
> | virtuális gépek | Igen | Igen |
> | virtualmachines / kiterjesztések | Igen | Igen |
> | virtualgép-mérlegek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Virtuális gépek áthelyezési útmutató](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftconsumption"></a>Microsoft.Fogyasztás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | összesített költség | Nem | Nem |
> | Egyenlegek | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | Díjak | Nem | Nem |
> | költségcímkék | Nem | Nem |
> | Hitelek | Nem | Nem |
> | események | Nem | Nem |
> | Előrejelzések | Nem | Nem |
> | Sok | Nem | Nem |
> | Piacokon | Nem | Nem |
> | műveleteredmények | Nem | Nem |
> | művelet állapota | Nem | Nem |
> | árlapok | Nem | Nem |
> | Termékek | Nem | Nem |
> | foglalásrészletei | Nem | Nem |
> | foglalási ajánlások | Nem | Nem |
> | fenntartásokummaries | Nem | Nem |
> | foglalási tranzakciók | Nem | Nem |
> | címkét | Nem | Nem |
> | Bérlők | Nem | Nem |
> | Feltételek | Nem | Nem |
> | használatrészletei | Nem | Nem |

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárolócsoportok | Nem | Nem |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárolócsoportok | Nem | Nem |
> | serviceassociationlinks | Nem | Nem |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Nyilvántartások | Igen | Igen |
> | nyilvántartások / ügynöki poolok | Nem | Nem |
> | nyilvántartások / buildfeladatok | Igen | Igen |
> | nyilvántartások / replikációk | Igen | Igen |
> | nyilvántartások / feladatfuttatások | Igen | Igen |
> | nyilvántartások / feladatok | Igen | Igen |
> | nyilvántartások / webhookok | Igen | Igen |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | containerservices (tárolószolgáltatások) | Nem | Nem |
> | kezelt klaszterek | Nem | Nem |
> | openshiftmanagedclusters | Nem | Nem |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerátor

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Nem | Nem |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | riasztások | Nem | Nem |
> | Költségvetések | Nem | Nem |
> | összekötők | Igen | Igen |
> | Méretek | Nem | Nem |
> | Export | Nem | Nem |
> | külső előfizetések | Nem | Nem |
> | forecast | Nem | Nem |
> | lekérdezés | Nem | Nem |
> | jelentési konfigurációk | Nem | Nem |
> | jelentések | Nem | Nem |
> | showbackrules | Nem | Nem |
> | Kilátás nyílik | Nem | Nem |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Csomópontok | Nem | Nem |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Egyesületek | Nem | Nem |
> | erőforrás-szolgáltatók | Igen | Igen |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Munkahelyek | Nem | Nem |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | databoxedgedevices (databoxedgedevices) | Nem | Nem |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Katalógusok | Igen | Igen |
> | adatkatalógusok | Nem | Nem |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kapcsolatkezelők | Nem | Nem |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Csomagok | Nem | Nem |
> | Tervek | Nem | Nem |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | adatgyárak | Igen | Igen |
> | Gyárak | Igen | Igen |

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Nem | Nem |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Nem | Nem |
> | szolgáltatások / projektek | Nem | Nem |
> | Bővítőhely | Nem | Nem |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | backupvaults | Nem | Nem |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Szerverek | Igen | Igen |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Szerverek | Igen | Igen |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kiszolgálócsoportok | Nem | Nem |
> | Szerverek | Igen | Igen |
> | kiszolgálókv2 | Igen | Igen |
> | singleservers (egykiszolgálós) | Igen | Igen |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műtermék-források | Igen | Igen |
> | bevezetés | Igen | Igen |
> | servicetopológiák | Igen | Igen |
> | servicetopológiák / szolgáltatások | Igen | Igen |
> | servicetopológiák / szolgáltatások / serviceunits | Igen | Igen |
> | lépések | Igen | Igen |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazáscsoportok | Nem | Nem |
> | hostpools | Nem | Nem |
> | munkaterületek | Nem | Nem |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | rugalmaskészletek | Nem | Nem |
> | elasticpools / iothubtenants | Nem | Nem |
> | iothubs | Igen | Igen |
> | kiépítési szolgáltatások | Igen | Igen |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Csővezetékek | Igen | Igen |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Vezérlők | Igen | Igen |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | laborközpontok | Nem | Nem |
> | Labs | Igen | Nem |
> | laborok / környezetek | Igen | Igen |
> | laborok / servicerunners | Igen | Igen |
> | laborok / virtualmachines | Igen | Nem |
> | Menetrend | Igen | Igen |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Nem | Nem |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | adatbázisfiókok | Igen | Igen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Tartományok | Igen | Igen |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Tartományok | Igen | Igen |
> | eventElőfizetések | Nem – nem helyezhető át egymástól függetlenül, de automatikusan áthelyezhető az előfizetett erőforrással. | Nem – nem helyezhető át egymástól függetlenül, de automatikusan áthelyezhető az előfizetett erőforrással. |
> | eseményre jelentkezőket | Nem – nem helyezhető át egymástól függetlenül, de automatikusan áthelyezhető az előfizetett erőforrással. | Nem – nem helyezhető át egymástól függetlenül, de automatikusan áthelyezhető az előfizetett erőforrással. |
> | extensiontopics (kiterjesztetttémakörök) | Nem | Nem |
> | partnernévterek | Igen | Igen |
> | partnerregisztrációk | Nem | Nem |
> | partnertémakörök | Igen | Igen |
> | rendszertémakörök | Igen | Igen |
> | Témák | Igen | Igen |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Klaszterek | Igen | Igen |
> | Névterek | Igen | Igen |

## <a name="microsoftexperimentation"></a>Microsoft.Kísérletezés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kísérleti munkaterek | Nem | Nem |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Névterek | Igen | Igen |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestKonfiguráció

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | automatikus annekt: | Nem | Nem |
> | automatikusan felügyeltvmkonfigurációs profilok | Nem | Nem |
> | vendégkonfigurációhozzárendelések | Nem | Nem |
> | Szoftver | Nem | Nem |
> | softwareupdateprofile | Nem | Nem |
> | szoftverfrissítések | Nem | Nem |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hanainstances (hanainstances) | Nem | Nem |
> | sapmonitorok | Igen | Igen |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Klaszterek | Igen | Igen |

> [!IMPORTANT]
> A HDInsight-fürtöket áthelyezheti egy új előfizetésbe vagy erőforráscsoportba. Azonban nem helyezheti át az előfizetések között a HDInsight-fürthöz (például a virtuális hálózathoz, a hálózati adapterhez vagy a terheléselosztóhoz) kapcsolódó hálózati erőforrásokat. Emellett nem helyezhet át egy új erőforráscsoportba a fürt virtuális gépéhez csatlakoztatott hálózati adaptert.
>
> Amikor egy HDInsight-fürtöt új előfizetésbe helyez át, először helyezze át az egyéb erőforrásokat (például a tárfiókot). Ezután helyezze át a HDInsight-fürtöt.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | services | Igen | Igen |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Gépek | Igen | Igen |
> | gépek / kiterjesztések | Nem | Nem |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | adatkezelők | Igen | Igen |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Összetevők | Nem | Nem |
> | hálózati hatókörök | Nem | Nem |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Munkahelyek | Igen | Igen |

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | műveletcsoportok | Igen | Igen |
> | activitylogalerts | Nem | Nem |
> | riasztási szabályok | Igen | Igen |
> | automatikus méretezhető beállítások | Igen | Igen |
> | Alapvonal | Nem | Nem |
> | kiszámítottakiindulási | Nem | Nem |
> | Összetevők | Igen | Igen |
> | adatgyűjteményi szabályok | Nem | Nem |
> | diagnosztikai beállítások | Nem | Nem |
> | diagnosztikai beállításokkategóriák | Nem | Nem |
> | eseménytípusok | Nem | Nem |
> | kiterjesztett diagnosztikai beállítások | Nem | Nem |
> | vendégdiagnosztikai beállítások | Nem | Nem |
> | logdefinitions (logdefinitions) | Nem | Nem |
> | naplók | Nem | Nem |
> | metrikus riasztások | Nem | Nem |
> | metricbaselines | Nem | Nem |
> | metrikus definíciók | Nem | Nem |
> | metricnamespaces | Nem | Nem |
> | metrics | Nem | Nem |
> | munkakönyvek | Nem | Nem |
> | értesítési csoportok | Nem | Nem |
> | privatelinkscopes | Igen | Igen |
> | scheduledqueryrules | Igen | Igen |
> | topology | Nem | Nem |
> | transactions | Nem | Nem |
> | vminsightsonboardingstatuses | Nem | Nem |
> | webtesztek | Igen | Igen |
> | munkafüzetek | Igen | Igen |
> | munkafüzetsablonok | Igen | Igen |

> [!IMPORTANT]
> Győződjön meg arról, hogy az új előfizetésre való áttérés nem haladja meg [az előfizetési kvótákat.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | iotapps | Igen | Igen |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | checknameavailability | Igen | Igen |
> | Grafikon | Igen | Igen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hsmpoolok | Nem | Nem |
> | Boltívek | Igen | Igen |

> [!IMPORTANT]
> A lemeztitkosításhoz használt kulcstárolók nem helyezhetők át egy erőforráscsoportba ugyanabban az előfizetésben vagy előfizetések között.

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | csatlakoztatott klaszterek | Nem | Nem |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Klaszterek | Igen | Igen |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | laborszámlák | Nem | Nem |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | üzemeltetési környezetek | Nem | Nem |
> | integrációs számlák | Igen | Igen |
> | integrációsszolgáltatási környezetek | Igen | Nem |
> | integrationserviceenvironments / managedapis | Igen | Nem |
> | elszigetelt környezetek | Nem | Nem |
> | Munkafolyamatok | Igen | Igen |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | kötelezettségvállalási tervek | Nem | Nem |
> | webszolgáltatások | Igen | Nem |
> | munkaterületek | Igen | Igen |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | operationalizationklaszterek | Nem | Nem |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningKísérletezation

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |
> | számlák / munkaterületek | Nem | Nem |
> | számlák / munkaterületek / projektek | Nem | Nem |
> | csapatszámlák | Nem | Nem |
> | teamaccounts / munkaterületek | Nem | Nem |
> | teamaccounts / munkaterületek / projektek | Nem | Nem |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárhelyszámlák | Nem | Nem |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |
> | munkaterületek / számítások | Nem | Nem |

## <a name="microsoftmaintenance"></a>Microsoft.Karbantartás

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | frissítések alkalmazása | Nem | Nem |
> | konfigurációs hozzárendelések | Nem | Nem |
> | karbantartási konfigurációk | Igen | Igen |
> | Frissítések | Nem | Nem |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Identitások | Nem | Nem |
> | userassignedidentities | Nem | Nem |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | menedzselt hálózatok | Nem | Nem |
> | menedzselt hálózatok / menedzselt hálózati csoportok | Nem | Nem |
> | managednetworks / managednetworkpeering policies | Nem | Nem |
> | értesítés | Nem | Nem |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | regisztrációhozzárendelések | Nem | Nem |
> | regisztrációs definíciók | Nem | Nem |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | számlák / magánatlaszok | Igen | Igen |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Nem | Nem |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | médiaszolgáltatások | Igen | Igen |
> | mediaservices / élő események | Igen | Igen |
> | mediaservices / streamingendpoints | Igen | Igen |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Tavasz

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazásfürtök | Nem | Nem |

## <a name="microsoftmigrate"></a>Microsoft.Áttelepítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | értékelési projektek | Igen | Igen |
> | projektek áttelepítése | Igen | Igen |
> | movecollections (gyűjtők) | Nem | Nem |
> | Projektek | Nem | Nem |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Nem | Nem |
> | netappaccounts / backuppolicies | Nem | Nem |
> | netappaccounts / capacitypools | Nem | Nem |
> | netappaccounts / capacitypools / kötetek | Nem | Nem |
> | netappaccounts / capacitypools / kötetek / mounttargets | Nem | Nem |
> | netappaccounts / capacitypools / kötetek / pillanatképek | Nem | Nem |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazásátjárók | Nem | Nem |
> | applicationgatewaywebapplicationfirewallpolicies | Nem | Nem |
> | alkalmazásbiztonsági csoportok | Igen | Igen |
> | azuretűzfalak | Igen | Igen |
> | bástyaházigazdák | Nem | Nem |
> | Kapcsolatok | Igen | Igen |
> | ddoscustompolicies | Igen | Igen |
> | ddosprotectionplans (ddosprotectionplans) | Nem | Nem |
> | dnszones között | Igen | Igen |
> | expresszroute-áramkörök | Nem | Nem |
> | expresszroute-átjárók | Nem | Nem |
> | tűzfalházirendek | Igen | Igen |
> | elülső ajtók | Nem | Nem |
> | frontdoorwebapplicationfirewallpolicies | Nem | Nem |
> | ipcsoportok | Igen | Igen |
> | terheléselosztók | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat |
> | localnetworkgateways (helyihálózati átjárók) | Igen | Igen |
> | natgateways | Igen | Igen |
> | hálózati kísérletprofilok | Igen | Igen |
> | hálózati szándékházirendek | Igen | Igen |
> | hálózati interfészek | Igen | Igen |
> | hálózati profilok | Nem | Nem |
> | networksecuritygroups | Igen | Igen |
> | networkwatchers (hálózati figyelők) | Igen | Nem |
> | networkwatchers / connectionmonitors | Igen | Nem |
> | networkwatchers / flowlogs | Igen | Nem |
> | networkwatchers / pingmeshes | Igen | Nem |
> | p2svpngateways | Nem | Nem |
> | privatednszones (magándnszones) | Igen | Igen |
> | privatednszones / virtualnetworklinks | Igen | Igen |
> | privateendpointredirecttérképek | Nem | Nem |
> | privateendpoints (magánvégpontok) | Igen | Igen |
> | privatelinkservices | Nem | Nem |
> | nyilvános ipcímek | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat | Igen - Alapvető Termékváltozat<br>Nem - Szabványos Termékváltozat |
> | nyilvános ipprefixek | Igen | Igen |
> | útvonalszűrők | Nem | Nem |
> | routetables között | Igen | Igen |
> | serviceendpointházirendek | Igen | Igen |
> | trafficmanagerprofiles (trafficmanagerprofiles) | Igen | Igen |
> | virtualhubok | Nem | Nem |
> | virtuálishálózatok átjárói | Igen | Igen |
> | virtuális hálózatok | Igen | Igen |
> | virtuálishálózatok lehallgatása | Nem | Nem |
> | virtualrouterek | Igen | Igen |
> | virtualwans | Nem | Nem |
> | vpngateways (Virtuális WAN) | Nem | Nem |
> | vpnserverkonfigurációk | Nem | Nem |
> | vpnsites (Virtuális WAN) | Nem | Nem |
> | webapplicationfirewallpolicies | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Hálózati áthelyezési útmutató](./move-limitations/networking-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Névterek | Igen | Igen |
> | névterek / értesítési központok | Igen | Igen |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Igen | Igen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Klaszterek | Nem | Nem |
> | storageinsightconfigs (tárolóinsightconfigs) | Nem | Nem |
> | munkaterületek | Igen | Igen |

> [!IMPORTANT]
> Győződjön meg arról, hogy az új előfizetésre való áttérés nem haladja meg [az előfizetési kvótákat.](azure-subscription-service-limits.md#azure-monitor-limits)

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | managementegyesületek | Nem | Nem |
> | felügyeleti konfigurációk | Igen | Igen |
> | megoldások | Igen | Igen |
> | Kilátás nyílik | Igen | Igen |

## <a name="microsoftpeering"></a>Microsoft.Társviszony-létesítés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | társviszony | Igen | Igen |
> | társviszony-létesítési szolgáltatások | Nem | Nem |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | politikai események | Nem | Nem |
> | politikai állapotok | Nem | Nem |
> | irányelvkövetett erőforrások | Nem | Nem |
> | kármentesítések | Nem | Nem |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | irányítópultok | Igen | Igen |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | rootresources (gyökérforrások) | Nem | Nem |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületi gyűjtemények | Igen | Igen |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Kapacitások | Igen | Igen |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Nem | Nem |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | bevezetés | Nem | Nem |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | backupprotecteditems (biztonsági mentésvédett elemek) | Nem | Nem |
> | replikációra való jogosultság eredményei | Nem | Nem |
> | Boltívek | Igen | Igen |

> [!IMPORTANT]
> Lásd: [Recovery Services move guidance](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | openshiftklaszterek | Nem | Nem |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Névterek | Igen | Igen |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Lekérdezések | Igen | Igen |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | elérhetőségi állapotok | Nem | Nem |
> | gyermekelérhetőségek | Nem | Nem |
> | gyermekerőforrások | Nem | Nem |
> | események | Nem | Nem |
> | Értesítések | Nem | Nem |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | központi telepítési parancsfájlok | Nem | Nem |
> | Linkek | Nem | Nem |
> | címkét | Nem | Nem |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Nem |

## <a name="microsoftsearch"></a>Microsoft.Keresés

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | keresési szolgáltatások | Igen | Igen |

> [!IMPORTANT]
> Egy műveletben nem helyezhet át több keresési erőforrást különböző régiókban. Ehelyett helyezze át őket külön műveletekben.

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | adaptívhálózat-keményedések | Nem | Nem |
> | advancedthreatprotectionbeállítások | Nem | Nem |
> | assessmentmetadata | Nem | Nem |
> | értékelések | Nem | Nem |
> | automatizálások | Igen | Igen |
> | megfelelőségi eredmények | Nem | Nem |
> | megfelelőségek | Nem | Nem |
> | adatgyűjtő kataszka | Nem | Nem |
> | eszközbiztonsági csoportok | Nem | Nem |
> | információvédelmi politikák | Nem | Nem |
> | iotsecuritysolutions | Igen | Igen |
> | kiszolgálóbiztonsági értékelés | Nem | Nem |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Összesítései | Nem | Nem |
> | riasztási szabályok | Nem | Nem |
> | alertruletemplates (figyelmeztetési szabálysablonok) | Nem | Nem |
> | Könyvjelzők | Nem | Nem |
> | Esetekben | Nem | Nem |
> | adatcsatlakozók | Nem | Nem |
> | adatkapcsolat-ellenőrzési követelmények | Nem | Nem |
> | Entitások | Nem | Nem |
> | entitáslekérdezések | Nem | Nem |
> | Események | Nem | Nem |
> | hivatali beleegyezések | Nem | Nem |
> | beállítások | Nem | Nem |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Átjárók | Nem | Nem |
> | Csomópontok | Nem | Nem |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Névterek | Igen | Igen |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Nem | Nem |
> | Klaszterek | Igen | Igen |
> | fürtök / alkalmazások | Nem | Nem |
> | tárolócsoportok | Nem | Nem |
> | tárolócsoportok készletei | Nem | Nem |
> | élhalmazok | Nem | Nem |
> | kezelt klaszterek | Nem | Nem |
> | Hálózatok | Nem | Nem |
> | titkos üzletek | Nem | Nem |
> | volumes | Nem | Nem |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazások | Igen | Igen |
> | tárolócsoportok | Nem | Nem |
> | Átjárók | Igen | Igen |
> | Hálózatok | Igen | Igen |
> | Titkok | Igen | Igen |
> | volumes | Igen | Igen |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | bevezetés | Nem | Nem |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | jelző | Igen | Igen |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | hibridhasználat előnyei | Nem | Nem |

## <a name="microsoftsolutions"></a>Microsoft.Megoldások

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | alkalmazásdefiníciók | Nem | Nem |
> | alkalmazások | Nem | Nem |
> | jitrequests | Nem | Nem |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | példánykészlet | Nem | Nem |
> | kezelt példányok | Nem | Nem |
> | managedinstances / adatbázisok | Nem | Nem |
> | Szerverek | Igen | Igen |
> | szerverek / adatbázisok | Igen | Igen |
> | szerverek / elaszator | Igen | Igen |
> | szerverek / jobaccounts | Igen | Igen |
> | szerverek / jobagents | Igen | Igen |
> | virtuális klaszterek | Igen | Igen |

> [!IMPORTANT]
> Az adatbázisnak és a kiszolgálónak ugyanabban az erőforráscsoportban kell lennie. Sql-kiszolgáló áthelyezésekor az összes adatbázis átkerül. Ez a viselkedés az Azure SQL Database és az Azure SQL Data Warehouse adatbázisokra vonatkozik.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Igen | Igen |
> | sqlvirtualmachines | Igen | Igen |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | dwvm | Nem | Nem |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tárolók | Igen | Igen |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Elrejt | Nem | Nem |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storagesyncservices (tárolószinkronizálási szolgáltatások) | Igen | Igen |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storagesyncservices (tárolószinkronizálási szolgáltatások) | Nem | Nem |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | storagesyncservices (tárolószinkronizálási szolgáltatások) | Nem | Nem |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Vezetők | Nem | Nem |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | streamingjobs | Igen | Igen |

> [!IMPORTANT]
> A Stream Analytics-feladatok nem helyezhetők át futó állapotban.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Környezetben | Nem | Nem |
> | környezetek / eseményforrások | Nem | Nem |
> | Példányok | Nem | Nem |
> | példányok / környezetek | Nem | Nem |
> | példányok / környezetek / eseményforrások | Nem | Nem |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | létrehozási előfizetés | Nem | Nem |

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | supportjegyek | Nem | Nem |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | munkaterületek | Nem | Nem |
> | munkaterületek / bigdatapoolok | Nem | Nem |
> | munkaterületek / sqlpoolok | Nem | Nem |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | szolgáltatóregisztrációk | Nem | Nem |
> | resources | Nem | Nem |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Környezetben | Igen | Igen |
> | környezetek / eseményforrások | Igen | Igen |
> | környezetek / referenciaadatkészletek | Igen | Igen |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Üzletek | Igen | Igen |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | képsablonok | Nem | Nem |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | account | Nem | Nem |
> | számla / kiterjesztés | Igen | Igen |
> | számla / projekt | Igen | Igen |

> [!IMPORTANT]
> Az Azure DevOps-előfizetés módosításáról a [számlázáshoz használt Azure-előfizetés módosítása](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)című témakörben

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | dedikált felhőnok | Nem | Nem |
> | dedikáltfelhőszolgáltatások | Nem | Nem |
> | virtuális gépek | Nem | Nem |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | eszközök | Nem | Nem |
> | vnfs | Nem | Nem |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | fiókok | Igen | Igen |
> | Tervek | Igen | Igen |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | tanúsítványok | Nem | Igen |
> | kapcsolatátjárók | Igen | Igen |
> | Kapcsolatok | Igen | Igen |
> | customapis (testreszabás) | Igen | Igen |
> | üzemeltetési környezetek | Nem | Nem |
> | kubekörnyezet | Igen | Igen |
> | kiszolgálófarmok | Igen | Igen |
> | Helyek | Igen | Igen |
> | oldalak / premieraddons | Igen | Igen |
> | oldalak / bővítőhely | Igen | Igen |
> | statikus helyek | Nem | Nem |

> [!IMPORTANT]
> Lásd: [App Service move útmutató](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys (multipleactivationkeys) | Nem | Nem |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | eszközszolgáltatások | Nem | Nem |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadFigyelő

> [!div class="mx-tableFixed"]
> | Erőforrás típusa | Erőforráscsoport | Előfizetés |
> | ------------- | ----------- | ---------- |
> | Összetevők | Nem | Nem |
> | monitorinstances | Nem | Nem |
> | Monitorok | Nem | Nem |
> | értesítési beállítások | Nem | Nem |

## <a name="third-party-services"></a>Harmadik fél től származó szolgáltatások

A külső szolgáltatások jelenleg nem támogatják az áthelyezési műveletet.

## <a name="next-steps"></a>További lépések
Az erőforrások áthelyezésére vonatkozó parancsokról az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe (Erőforrások áthelyezése)](move-resource-group-and-subscription.md)témakörben található.

Ha ugyanazokat az adatokat szeretné beszerezni, mint a vesszővel tagolt értékek et, töltse le [a move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)fájlt.
