---
title: Kezelheti a hálózati biztonsági csoport Flow naplók az Azure Network Watcher – PowerShell |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan kezelheti a hálózati biztonsági csoport folyamat-Naplók az Azure Network Watcher a PowerShell-lel
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 55a9e373893f6f46e6c109dde947b25a36ee93ed
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343000"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Hálózati biztonsági csoport Flow naplók konfigurálása a PowerShell-lel

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Hálózati biztonsági csoportok folyamatnaplóit érhetők el a Network Watcher, amely lehetővé teszi a bejövő és kimenő IP-forgalom hálózati biztonsági csoport használatával kapcsolatos információk megtekintéséhez. A folyamat-naplók json formátumban íródtak, és a kimenő és bejövő folyamatok megjelenítése / szabály történik, a hálózati Adapterhez, a folyamat vonatkozik, a folyamat (a forrás és cél IP-cím, forrás és a cél-Port, protokoll), 5-ször több információt, és ha a forgalom engedélyezett vagy tiltott.

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamat sikeres működéséhez naplózása a **Microsoft.Insights** szolgáltató regisztrálva kell lennie. Ha nem, hogy ha a **Microsoft.Insights** szolgáltató regisztrálva van, futtassa a következő szkriptet.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Engedélyezze a hálózati biztonsági csoport folyamatnaplóit, és a Traffic Analytics

A parancs a folyamat-naplók engedélyezésére az alábbi példában látható:

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
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

A tárfiók, megadhatja, hogy nem rendelkezik a hálózati hozzáférés korlátozása csak a Microsoft-szolgáltatások vagy a virtuális hálózatok adott beállított hálózati szabályokat. A tárfiók ugyanabban vagy egy másik Azure-előfizetéssel, mint az NSG-t, amely engedélyezi a flow naplójának lehet. Ha különböző előfizetésekhez használja, azokat is kell rendelni ugyanahhoz az Azure Active Directory-bérlőhöz. Az egyes előfizetésekhez használt fióknak rendelkeznie kell a [szükséges engedélyek](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>A Traffic Analytics és a hálózati biztonsági csoport Flow naplók letiltása

Az alábbi példát követve tiltsa le a traffic analytics és a rendszer:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Egy Flow-napló letöltése

A tárolási helye egy flow-log létrehozásakor van meghatározva. Ezen forgalmi naplók tárfiókba menti el az eszköz itt tölthető le a Microsoft Azure Storage Explorerben:  http://storageexplorer.com/

Ha egy storage-fiók van megadva, a folyamat naplófájlok mentése egy tárfiókba, a következő helyen:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A napló szerkezete kapcsolatos információk [hálózati biztonsági csoport Flow napló áttekintése](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [jelenítheti meg az NSG-Folyamatnaplók a powerbi használatával](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [jelenítheti meg az NSG-Folyamatnaplók nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
