---
title: Hálózati biztonsági csoport Flow naplók és Azure hálózati figyelőt - PowerShell kezelése |} Microsoft Docs
description: Ezen a lapon ismerteti, hogyan kezelheti a hálózati biztonsági csoport Flow-naplók Azure hálózati figyelőt a PowerShell használatával
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
ms.openlocfilehash: 8283955b58978e3a76456ec3eff63f4fba4e51f0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Hálózati biztonsági csoport Flow naplók konfigurálása a PowerShell segítségével

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Hálózati biztonsági csoport folyamat egyik funkciója, amely lehetővé teszi, hogy tekintse meg a hálózati biztonsági csoporton keresztül bemenő és kimenő IP-forgalom hálózati figyelőt feldolgozásra. A folyamat naplók json formátumban vannak megírva, és a kimenő és bejövő forgalom alapon egy szabályt, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP-, forrás vagy a cél Port protokoll), 5 rekordos információk megjelenítése, és ha a forgalom lett engedélyez vagy tilt.

## <a name="register-insights-provider"></a>Elemzések szolgáltató regisztrálása

Ahhoz, hogy sikeresen, működjön naplózás folyamata a **Microsoft.Insights** szolgáltató regisztrálva kell lennie. Ha nem tudja, ha a **Microsoft.Insights** szolgáltató regisztrálva van, futtassa a következő parancsfájlt.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport Flow naplók engedélyezése

A parancsot a folyamat naplók engedélyezéséhez az alábbi példában látható:

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG-Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

A tárfiók, megadhatja, hogy nem lehet a hálózati hozzáférés korlátozása csak a Microsoft-szolgáltatások vagy az adott virtuális hálózatok beállított hálózati szabályokat.

## <a name="disable-network-security-group-flow-logs"></a>Tiltsa le a hálózati biztonsági csoport folyamata naplók

Az alábbi példa használatával tiltsa le a naplók folyamata:

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>A folyamat napló letöltése

A folyamat napló tárolási helye a létrehozásakor van definiálva. A folyamat naplók tárfiókba menti eléréséhez eszköz a Microsoft Azure Tártallózó, amely innen tölthető le:  http://storageexplorer.com/

Ha egy tárfiókot meg van adva, csomag rögzítési fájlok kerülnek a storage-fiókok a következő helyen:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A napló szerkezete információt [hálózati biztonsági csoport Flow napló áttekintése](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [a powerbi-jal az NSG folyamata naplók megjelenítése](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Megtudhatja, hogyan [jelenítheti meg az NSG folyamata naplók nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
