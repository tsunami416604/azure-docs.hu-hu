---
title: Azure-erőforrások 800-os számú korlát nélkül
description: Azokat az Azure-erőforrásokat sorolja fel, amelyek több mint 800 példányt tartalmazhatnak egy erőforráscsoporthoz.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: eec448dcbf1c550b6b0a0ee0f17505fd6ddd3c74
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194740"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Az erőforrások nem korlátozódnak 800 példányra az erőforráscsoport esetében

Alapértelmezés szerint az egyes erőforráscsoportok esetében akár 800 példányt is üzembe helyezhet. Bizonyos erőforrástípusok azonban mentesülnek az 800-es példány korlátján. Ez a cikk azokat az Azure-erőforrásokat sorolja fel, amelyek több mint 800 példányt tartalmazhatnak egy erőforráscsoporthoz. Az összes többi erőforrás-típus 800 példányra van korlátozva.

Egyes erőforrástípusok esetében kapcsolatba kell lépnie az ügyfélszolgálattal, hogy el lehessen távolítani az 800-es példány korlátját. Ezek az erőforrástípusok ebben a cikkben vannak feltüntetve.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* regisztrációk
* registrations/customerSubscriptions
* regisztrációk/termékek

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices – a korlát meghosszabbításához forduljon az ügyfélszolgálathoz.

## <a name="microsoftcompute"></a>Microsoft.Compute

* lemezek
* rendszerképek
* pillanatképek
* virtualMachines

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* kibocsátásiegység-forgalmi jegyzékek/buildTasks
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/listSourceRepositoryProperties
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések
* kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések/listBuildArguments
* registries/eventGridFilters
* kibocsátásiegység-forgalmi jegyzékek/replikálások
* kibocsátásiegység-forgalmi jegyzékek/feladatok
* kibocsátásiegység-forgalmi jegyzékek/webhookok

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* kiszolgáló

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* kiszolgáló

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* kiszolgáló
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* szoftver
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* munkafolyamatok

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
* dnszones/recordsets
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
* Nyilvános IP – a korlát meghosszabbításához forduljon az ügyfélszolgálathoz.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – a korlát meghosszabbításához forduljon az ügyfélszolgálathoz.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* feladatgyűjtemények

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* webhelyek

## <a name="next-steps"></a>További lépések

A kvóták és korlátok teljes listáját az Azure- [előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../azure-subscription-service-limits.md)című témakörben tekintheti meg.
