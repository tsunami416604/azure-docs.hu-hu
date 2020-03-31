---
title: NSG-folyamatnaplók kezelése – Azure REST API
titleSuffix: Azure Network Watcher
description: Ez a lap bemutatja, hogyan kezelhetők a hálózati biztonsági csoport folyamatnaplói az Azure Network Watcher ben rest API-val
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
ms.openlocfilehash: 7cc47414dc985f6fc2fff3c57d809f307b142e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840927"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Hálózati biztonsági csoport folyamatnaplóinak konfigurálása REST API használatával

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport folyamatnaplói a Network Watcher szolgáltatása, amely lehetővé teszi a hálózati biztonsági csoporton keresztül a be- és kilépésip-forgalommal kapcsolatos információkat. Ezek a folyamatnaplók json formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamat által alkalmazott hálózati adaptert, a folyamat 5-tuple információit (Forrás/cél IP, Forrás/cél port, Protokoll), és ha a forgalmat engedélyezték vagy megtagadták.

## <a name="before-you-begin"></a>Előkészületek

Az ARMclient a REST API-t a PowerShell használatával hívja meg. ARMClient található csokoládés [armclient a Chocolatey](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit.

> [!Important]
> A Network Watcher REST API meghívja az erőforráscsoport nevét a kérelem URI az erőforráscsoport, amely tartalmazza a Hálózati figyelő, nem az erőforrásokat, amelyeken a diagnosztikai műveleteket hajt végre.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv ebben a cikkben bemutatja, hogyan engedélyezheti, letilthatja és lekérdezési folyamatnaplók a REST API használatával. Ha többet szeretne megtudni a hálózati biztonsági csoport folyamatnaplózásáról, látogasson el a [Hálózati biztonsági csoport folyamatnaplózása – Áttekintés című témakörbe.](network-watcher-nsg-flow-logging-overview.md)

Ebben a forgatókönyvben a következőket fogja:

* Folyamatnaplók engedélyezése (2. verzió)
* Folyamatnaplók letiltása
* Lekérdezési folyamatnaplók állapota

## <a name="log-in-with-armclient"></a>Bejelentkezés armclient nal

Jelentkezzen be az Armclient-ba az Azure-hitelesítő adataival.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

A folyamatnaplózás sikeres működéséhez a **Microsoft.Insights-szolgáltatót** regisztrálni kell. Ha nem biztos abban, hogy a **Microsoft.Insights-szolgáltató** regisztrálva van-e, futtassa a következő parancsfájlt.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport folyamatnaplóinak engedélyezése

A folyamatnaplók 2-es verziójú engedélyezésére a következő példa jelenik meg. Az 1.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Az előző példából visszaadott válasz a következő:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport folyamatnaplóinak letiltása

A következő példában tiltsa le a folyamatnaplókat. A hívás megegyezik a folyamatnaplók engedélyezésével, kivéve, hogy **hamis** beállítás van beállítva az engedélyezett tulajdonsághoz.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Az előző példából visszaadott válasz a következő:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Lekérdezési folyamatnaplók

A következő REST-hívás lekérdezi a folyamatnaplók állapotát egy hálózati biztonsági csoporton.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

A következő példa a visszaadott válasz:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Folyamatnapló letöltése

A folyamatnapló tárolási helye a létrehozáskor van definiálva. A tárfiókba mentett folyamatnaplók elérésének kényelmes eszköze a Microsoft Azure Storage Explorer, amely itt tölthető le:https://storageexplorer.com/

Ha meg van adva tárfiók, a csomagrögzítő fájlok at a következő helyen menti a rendszer egy tárfiókba:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>További lépések

Az [NSG-folyamatnaplók visualualizálása a PowerBI-vel](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [jelenítheti meg az NSG-folyamatnaplókat nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
