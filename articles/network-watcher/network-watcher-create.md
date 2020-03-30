---
title: Azure Network Watcher-példány létrehozása | Microsoft dokumentumok
description: Ismerje meg, hogyan engedélyezheti a Network Watcher t egy Azure-régióban.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 77812a3765a027152c957f6dbb7c9b3811a2278f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191177"
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher-példány létrehozása

A Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a feltételek figyelése és diagnosztizálása hálózati forgatókönyv szintjén az Azure-ban, az Azure-ban és az Azure-ból. A forgatókönyvszintű figyelés lehetővé teszi a problémák diagnosztizálását a hálózat végpontjának nézetben. A Network Watcher segítségével a Network Watcher segítségével megismerheti, diagnosztizálhatja és betekintést nyerhet a hálózatába az Azure-ban. A Network Watcher egy Network Watcher erőforrás létrehozásával engedélyezett. Ez az erőforrás lehetővé teszi a Network Watcher képességeinek kihasználását.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>A Hálózatfigyelő automatikusan engedélyezve van
Amikor virtuális hálózatot hoz létre vagy módosít az előfizetésben, a Network Watcher automatikusan engedélyezve lesz a virtuális hálózat régiójában. A Network Watcher automatikus engedélyezése az Ön erőforrásait nem érinti, és nem jár költségekkel.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Leiratkozás a Network Watcher automatikus engedélyezéséről
Ha le szeretné tiltani a Network Watcher automatikus engedélyezését, ezt a következő parancsok futtatásával teheti meg:

> [!WARNING]
> A Network Watcher automatikus engedélyezésének letiltása állandó változás. A leiratkozás után nem iratkozhat fel [anélkül, hogy kapcsolatba lépne az ügyfélszolgálattal](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Hálózati figyelő létrehozása a portálon

Keresse meg az **Összes szolgáltatás** > **hálózati** > **figyelőját.** Kiválaszthatja az összes olyan előfizetést, amelyhez engedélyezni szeretné a Network Watcher-t. Ez a művelet minden rendelkezésre álló régióban létrehoz egy hálózatfigyelőt.

![hálózati figyelő létrehozása](./media/network-watcher-create/figure1.png)

Ha engedélyezi a Network Watcher a portál használatával, a Network Watcher-példány neve automatikusan be van állítva *NetworkWatcher_region_name,* ahol *region_name* megfelel az Azure-régióban, ahol a példány engedélyezve van. Az USA nyugati középső régiójában engedélyezett hálózati figyelő neve például *NetworkWatcher_westcentralus.*

A Network Watcher példány automatikusan létrejön egy *NetworkWatcherRG*nevű erőforráscsoportban. Az erőforráscsoport akkor jön létre, ha még nem létezik.

Ha szeretné testreszabni a nevét egy Network Watcher-példány és az erőforráscsoport, amelybe kerül, használhatja a Powershell, az Azure CLI, a REST API-t, vagy ARMClient metódusok a következő szakaszokban leírt. Az erőforráscsoportnak minden beállításban léteznie kell, mielőtt létrehozna benne egy hálózatfigyelőt.  

## <a name="create-a-network-watcher-with-powershell"></a>Hálózati figyelő létrehozása a PowerShell segítségével

A Network Watcher egy példányának létrehozásához futtassa a következő példát:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Hozzon létre egy hálózati figyelőt az Azure CLI-vel

A Network Watcher egy példányának létrehozásához futtassa a következő példát:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Hálózati figyelő létrehozása a REST API-val

Az ARMclient a REST API-t a PowerShell használatával hívja meg. Az ARMClient található csokoládés [armclient a Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Bejelentkezés armclient nal

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>A hálózatfigyelő létrehozása

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

## <a name="delete-a-network-watcher-in-the-portal"></a>Hálózati figyelő törlése a portálon

Keresse meg az **Összes szolgáltatás** > **hálózati** > **figyelőját.**

Válassza az áttekintő lapot, ha még nem ért oda. A legördülő menüben válassza ki azt az előfizetést, amelyben le szeretné tiltani a hálózati figyelőt.
Bontsa ki a kiválasztott előfizetés régióinak listáját a nyílra kattintva. Minden adott, használja a 3 pont a jogot, hogy hozzáférjen a helyi menüben.
Kattintson a "Hálózati figyelő letiltása" gombra a letiltás megkezdéséhez. A rendszer megkéri, hogy erősítse meg ezt a lépést. A folytatáshoz kattintson az Igen lehetőségre.
A portálon ezt minden előfizetés minden régiójában külön-külön kell megtennie.


## <a name="delete-a-network-watcher-with-powershell"></a>Hálózati figyelő törlése a PowerShell használatával

A Network Watcher egy példányának törléséhez futtassa a következő példát:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik a Network Watcher egy példányával, ismerje meg a rendelkezésre álló funkciókat:

* [Topológia](network-watcher-topology-overview.md)
* [Csomagrögzítés](network-watcher-packet-capture-overview.md)
* [IP-forgalom ellenőrzése](network-watcher-ip-flow-verify-overview.md)
* [Következő ugrás](network-watcher-next-hop-overview.md)
* [Biztonsági csoport nézet](network-watcher-security-group-view-overview.md)
* [NSG-folyamatnaplózás](network-watcher-nsg-flow-logging-overview.md)
* [Virtuális hálózati átjáró – hibaelhárítás](network-watcher-troubleshoot-overview.md)
