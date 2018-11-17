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
ms.openlocfilehash: 752370564c52513d59e99b18d5343b0575900463
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819355"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Hálózati biztonsági csoport Flow naplók konfigurálása a PowerShell-lel

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Hálózati biztonsági csoportok folyamatnaplóit érhetők el a Network Watcher, amely lehetővé teszi a bejövő és kimenő IP-forgalom hálózati biztonsági csoport használatával kapcsolatos információk megtekintéséhez. A folyamat-naplók json formátumban íródtak, és a kimenő és bejövő folyamatok megjelenítése / szabály történik, a hálózati Adapterhez, a folyamat vonatkozik, a folyamat (a forrás és cél IP-cím, forrás és a cél-Port, protokoll), 5-ször több információt, és ha a forgalom engedélyezett vagy tiltott.

> [!NOTE] 
> Flow 2-es naplók verzió oszlopkészletet csak az USA nyugati középső régiójában. Beállítási lehetőségek érhetők el az Azure Portal és a REST API-t. 2-es verzió engedélyezése egy nem támogatott régióban naplókat eredményez 1-es verziójú naplókat, a storage-fiókhoz használt kimeneti adattípus.

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamat sikeres működéséhez naplózása a **Microsoft.Insights** szolgáltató regisztrálva kell lennie. Ha nem, hogy ha a **Microsoft.Insights** szolgáltató regisztrálva van, futtassa a következő szkriptet.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Engedélyezze a hálózati biztonsági csoport folyamatnaplóit

A parancs a folyamat-naplók engedélyezésére az alábbi példában látható:

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

A tárfiók, megadhatja, hogy nem rendelkezik a hálózati hozzáférés korlátozása csak a Microsoft-szolgáltatások vagy a virtuális hálózatok adott beállított hálózati szabályokat. A tárfiók ugyanabban vagy egy másik Azure-előfizetéssel, mint az NSG-t, amely engedélyezi a flow naplójának lehet. Ha különböző előfizetésekhez használja, azokat is kell rendelni ugyanahhoz az Azure Active Directory-bérlőhöz. Az egyes előfizetésekhez használt fióknak rendelkeznie kell a [szükséges engedélyek](required-rbac-permissions.md).

## <a name="disable-network-security-group-flow-logs"></a>Tiltsa le a hálózati biztonsági csoportok folyamatnaplóit

Forgalmi naplók letiltásához használja a következő példát:

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Egy Flow-napló letöltése

A tárolási helye egy flow-log létrehozásakor van meghatározva. Ezen forgalmi naplók tárfiókba menti el az eszköz itt tölthető le a Microsoft Azure Storage Explorerben:  http://storageexplorer.com/

Ha egy storage-fiók van megadva, packet capture fájlok egy storage-fiókba, a következő helyen találhatóak meg:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A napló szerkezete kapcsolatos információk [hálózati biztonsági csoport Flow napló áttekintése](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [jelenítheti meg az NSG-Folyamatnaplók a powerbi használatával](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [jelenítheti meg az NSG-Folyamatnaplók nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
