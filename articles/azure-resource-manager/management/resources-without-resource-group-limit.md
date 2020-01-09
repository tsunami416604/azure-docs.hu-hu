---
title: 800-os korláttal nem rendelkező erőforrások száma
description: Azokat az Azure-erőforrásokat sorolja fel, amelyek több mint 800 példányt tartalmazhatnak egy erőforráscsoporthoz.
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: c4f452a13c2059c02bf675ca4fe80243257183d5
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659321"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Az erőforrások nem korlátozódnak 800 példányra az erőforráscsoport esetében

Alapértelmezés szerint az egyes erőforráscsoportok esetében akár 800 példányt is üzembe helyezhet. Bizonyos erőforrástípusok azonban mentesülnek az 800-es példány korlátján. Ez a cikk azokat az Azure-erőforrásokat sorolja fel, amelyek több mint 800 példányt tartalmazhatnak egy erőforráscsoporthoz. Az összes többi erőforrás-típus 800 példányra van korlátozva.

Egyes erőforrástípusok esetében kapcsolatba kell lépnie az ügyfélszolgálattal, hogy el lehessen távolítani az 800-es példány korlátját. Ezek az erőforrástípusok ebben a cikkben vannak feltüntetve.


## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* regisztrációk
* regisztrációk/customerSubscriptions
* regisztrációk/termékek

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.

## <a name="microsoftcompute"></a>Microsoft.Compute

* lemezek
* images
* pillanatképek
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* kibocsátásiegység-forgalmi jegyzékek/buildTasks
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/listSourceRepositoryProperties
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések/listBuildArguments
* kibocsátásiegység-forgalmi jegyzékek/eventGridFilters
* kibocsátásiegység-forgalmi jegyzékek/replikálások
* kibocsátásiegység-forgalmi jegyzékek/feladatok
* kibocsátásiegység-forgalmi jegyzékek/webhookok

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* Kiszolgálók

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* Kiszolgálók

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* serverGroups
* Kiszolgálók
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* fürtök
* névterek

## <a name="microsoftexperimentation"></a>Microsoft. kísérletezés

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* configurationProfileAssignments
* guestConfigurationAssignments
* szoftver
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* munkafolyamatok

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/kötetek
* netAppAccounts/capacityPools/kötetek/mountTargets
* netAppAccounts/capacityPools/kötetek/Pillanatképek

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/mind
* dnszones/rekordhalmazok
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/mind
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* Nyilvános IP – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections – alapértelmezés szerint 800 példányra korlátozódik. Ezt a korlátot a támogatási szolgálattal növelheti.

## <a name="microsoftrelay"></a>Microsoft. Relay

* névterek

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* feladatgyűjtemények

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* névterek

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

* alkalmazás
* containerGroups
* átjárók
* hálózatok
* titkok
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/API-k
* webhelyek

## <a name="next-steps"></a>Következő lépések

A kvóták és korlátok teljes listáját az Azure- [előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben tekintheti meg.
