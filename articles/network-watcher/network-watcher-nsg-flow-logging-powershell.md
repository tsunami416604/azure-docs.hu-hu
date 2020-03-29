---
title: NSG-folyamatnaplók kezelése – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez a lap bemutatja, hogyan kezelhetők a Hálózati biztonsági csoport folyamatnaplói az Azure Network Watcher ben a PowerShell segítségével
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 9612afdb63c6988c0027f003caeacd456b5e50e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840944"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Hálózati biztonsági csoportfolyamat-naplók konfigurálása a PowerShell használatával

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport folyamatnaplói a Network Watcher szolgáltatása, amely lehetővé teszi a hálózati biztonsági csoporton keresztül a be- és kilépésip-forgalommal kapcsolatos információkat. Ezek a folyamatnaplók json formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamat által alkalmazott hálózati adaptert, a folyamat 5-tuple információit (Forrás/cél IP, Forrás/cél port, Protokoll), és ha a forgalmat engedélyezték vagy megtagadták.

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

A folyamatnaplózás sikeres működéséhez a **Microsoft.Insights-szolgáltatót** regisztrálni kell. Ha nem biztos abban, hogy a **Microsoft.Insights-szolgáltató** regisztrálva van-e, futtassa a következő parancsfájlt.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Hálózati biztonsági csoportfolyamat-naplók és forgalomelemzés engedélyezése

A folyamatnaplók engedélyezésére a következő példa jelenik meg:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

A megadott tárfiókhoz nem lehetnek olyan hálózati szabályok, amelyek csak a Microsoft-szolgáltatásokra vagy adott virtuális hálózatokra korlátozzák a hálózati hozzáférést. A tárfiók lehet ugyanabban, vagy egy másik Azure-előfizetés, mint az NSG, amely engedélyezi a folyamatnaplót. Ha különböző előfizetéseket használ, mindkettőt ugyanahhoz az Azure Active Directory-bérlőhöz kell társítani. Az egyes előfizetésekhez használt fióknak rendelkeznie kell a [szükséges engedélyekkel.](required-rbac-permissions.md)

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Forgalomelemzési és hálózati biztonsági csoportfolyamat-naplók letiltása

A következő példa segítségével tiltsa le a forgalomelemzést és a folyamatnaplókat:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Flow-napló letöltése

A folyamatnapló tárolási helye a létrehozáskor van definiálva. A tárfiókba mentett folyamatnaplók elérésének kényelmes eszköze a Microsoft Azure Storage Explorer, amely itt tölthető le:https://storageexplorer.com/

Ha meg van adva tárfiók, a folyamatnapló-fájlok a következő helyen tárolódnak egy tárfiókba:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A napló szerkezetéről a [Hálózati biztonsági csoport folyamatnaplójának áttekintése](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Következő lépések

Az [NSG-folyamatnaplók visualualizálása a PowerBI-vel](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [jelenítheti meg az NSG-folyamatnaplókat nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
