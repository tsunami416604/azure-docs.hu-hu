---
title: "Hozzon létre egy Azure hálózati figyelőt példányt |} Microsoft Docs"
description: "Ezen a lapon ismerteti a hálózati figyelőt példányának létrehozása a portál és az Azure REST API használatával"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fb1e3f9a93d18e949cb42ac0a4e09129cfe414f6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-an-azure-network-watcher-instance"></a>Hozzon létre egy Azure hálózati figyelőt példányt

Hálózati figyelőt olyan regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása szintjén feltételek egy hálózati forgatókönyv, hogy, és az Azure-ból. Forgatókönyv szintű figyelését teszi lehetővé egy végpontok közötti hálózati szintű áttekintés a problémák diagnosztizálásához. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban.

> [!NOTE]
> Hálózati figyelőt jelenleg csak támogatja CLI 1.0, CLI 1.0 biztosított új hálózati figyelőt példány létrehozásához nyújt útmutatást.

## <a name="create-a-network-watcher-in-the-portal"></a>Hozzon létre egy hálózati figyelőt a portálon

Navigáljon a **minden szolgáltatás** > **hálózati** > **hálózati figyelő**. Kiválaszthatja, hogy a hálózati figyelőt engedélyezni szeretné az előfizetéseket. Ez a művelet létrehoz egy hálózati figyelőt minden régióban elérhető.

![Hozzon létre egy hálózati figyelőt][1]

Ha engedélyezi a hálózati figyelőt a portál használatával, a hálózati figyelőt példányának neve automatikusan állítja be NetworkWatcher_region_name, ahol region_name felel meg az Azure-régióban, ahol a példány volt engedélyezve.  Például egy hálózati figyelőt nyugati középső Régiójában régióban engedélyezve lesznek elnevezve NetworkWatcher_westcentralus

Emellett a hálózati figyelőt példány automatikusan hozzáadja a NetworkWatcherRG nevű erőforráscsoport.  Ez az erőforráscsoport létrejön, ha még nem létezik.

Ha egy hálózati figyelőt példány és az erőforráscsoport nevét testreszabja az el van helyezve, az alábbiakban Powershell, a REST API vagy ARMClient módszerekkel.  Az egyes lehetőségek az erőforráscsoport már léteznie kell a hálózati figyelőt helyezzen azt.  

## <a name="create-a-network-watcher-with-powershell"></a>A hálózati figyelő létrehozása a PowerShell használatával

Hálózati figyelőt példányának létrehozásához futtassa az alábbi példa:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Hozzon létre egy hálózati figyelőt REST API-val

A PowerShell használatával REST API hívása ARMclient szolgál. ARMClient verziója van telepítve, chocolatey [a Chocolatey ARMClient](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>A hálózati figyelő létrehozása

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>További lépések

Most, hogy hálózati figyelőt példányának, megismerése funkciók érhetők el:

* [Topológia](network-watcher-topology-overview.md)
* [Csomagrögzítéssel](network-watcher-packet-capture-overview.md)
* [IP-forgalom ellenőrzése](network-watcher-ip-flow-verify-overview.md)
* [Következő ugrás](network-watcher-next-hop-overview.md)
* [Biztonsági csoport nézet](network-watcher-security-group-view-overview.md)
* [NSG folyamata naplózás](network-watcher-nsg-flow-logging-overview.md)
* [Virtuális hálózati átjáró hibaelhárítása](network-watcher-troubleshoot-overview.md)

Ha a hálózati figyelőt példánya létrejött, csomag rögzítési konfigurálhatja a cikk a következő: [riasztási kiváltott csomagrögzítéssel létrehozása](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-create/figure1.png











