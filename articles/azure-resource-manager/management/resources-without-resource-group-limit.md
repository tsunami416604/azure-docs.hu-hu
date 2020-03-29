---
title: 800 darabszámkorlát nélküli erőforrások
description: Felsorolja az Azure-erőforrás-típusok, amelyek több mint 800 példányegy erőforráscsoportban.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 735cad0bfa936c41f603e42bdb9be77a1562cc1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937938"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Erőforrás-csoportokonként legfeljebb 800 példányra korlátozódó erőforrások

Alapértelmezés szerint minden erőforráscsoportban legfeljebb 800 erőforrástípus-példány telepíthető. Egyes erőforrástípusok azonban mentesülnek a 800 példánykorlát alól. Ez a cikk azokat az Azure-erőforrástípusokat sorolja fel, amelyek egy erőforráscsoportban több mint 800 példányt használhatnak. Az összes többi erőforrástípus 800 példányra korlátozódik.

Egyes erőforrástípusok esetén kapcsolatba kell lépnie az ügyfélszolgálattal a 800 példánykorlát eltávolításához. Ezeket az erőforrástípusokat ebben a cikkben ismertetjük.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* Regisztrációk
* regisztrációk/customerSubscriptions
* regisztrációk/termékek
* ellenőrző billentyűk

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Alapértelmezés szerint legfeljebb 800 példányban. Ez a korlát növelhető a támogatási szolgálathoz való felvételsel.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Lemezek
* images
* pillanatképek
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups (tárolócsoportok)

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* nyilvántartások/buildFeladatok
* beállításjegyzékek/buildTasks/listSourceRepositoryProperties
* nyilvántartások/buildFeladatok/lépések
* beállításjegyzékek/buildTasks/steps/listBuildArguments
* regisztika/eventGridFilters
* nyilvántartások/replikációk
* nyilvántartások/feladatok
* nyilvántartások/webhookok

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Szerverek

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* Szerverek

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* kiszolgálócsoportok
* Szerverek
* kiszolgálókv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Klaszterek
* Névterek

## <a name="microsoftexperimentation"></a>Microsoft.Kísérletezés

* experimentWorkspaces (kísérletezőmunkaterek)

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestKonfiguráció

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments (configurationProfileAssignments)
* guestConfigurationAssignments
* Szoftver
* softwareUpdateProfile
* szoftverfrissítések

## <a name="microsoftinsights"></a>Microsoft.Insights

* metrikus riasztások

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrációS fiókok
* Munkafolyamatok

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots

## <a name="microsoftnetwork"></a>Microsoft.Network

* alkalmazásGatewayWebApplicationFirewallPolicies
* alkalmazásSecurityGroups
* bástyaHázigazdák
* ddosProtectionPlans
* dnszones között
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
* dnszones/minden
* dnszones/rekordhalmazok
* hálózatiIntentPolicies
* hálózati interfészek
* privátDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses - Alapértelmezés szerint legfeljebb 800 példányban. Ez a korlát növelhető a támogatási szolgálathoz való felvételsel.
* serviceEndpointPolicies
* trafficmanagerprofiles (trafficmanagerprofiles)
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – Alapértelmezés szerint legfeljebb 800 példányban. Ez a korlát növelhető a támogatási szolgálathoz való felvételsel.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Névterek

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* munkagyűjtemények

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Névterek

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* alkalmazások
* containerGroups (tárolócsoportok)
* Átjárók
* Hálózatok
* Titkok
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* Helyek

## <a name="next-steps"></a>További lépések

A kvóták és korlátok teljes listáját az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](azure-subscription-service-limits.md)témakörben található.
