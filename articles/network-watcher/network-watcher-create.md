---
title: Az Azure Network Watcher-példány létrehozása |} A Microsoft Docs
description: Útmutató Network Watcher engedélyezése az Azure-régióba.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 601a3f273a8da9100d24dfdbd13bd598b0e48884
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051563"
---
# <a name="create-an-azure-network-watcher-instance"></a>Az Azure Network Watcher-példány létrehozása

A Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet hálózati forgatókönyvek szintjén, a, és az Azure-ból. Forgatókönyv szintű figyelése lehetővé teszi egy teljes körű hálózati szintű áttekintés a problémák diagnosztizálásához. A hálózati diagnosztikai és vizualizációs eszközök a Network Watcherrel elérhető segítenek megérteni, diagnosztizálása és betekintést nyerhet az Azure-ban a hálózati. A Network Watcher engedélyezve van a Network Watcher-erőforrást kell létrehozni. Ehhez az erőforráshoz lehetővé teszi, hogy a Network Watcher képességek használatát.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>A Network Watcher automatikusan engedélyezve van
Ha hoz létre, vagy frissítse a virtuális hálózat az előfizetésben, Network Watcher a virtuális hálózati régióban automatikusan lesz engedélyezve. A Network Warcher automatikus engedélyezése semmilyen hatással sincs az Ön erőforrásaira, sem a díjaikra.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Lemondja a Network Watcher automatikus engedélyezés
Ha szeretné tilthatják le a Network Watcher automatikus lehetővé tétele, megteheti a következő parancsok futtatásával:

> [!WARNING]
> Engedélyezés meg a Network Watcher automatikus engedélyezését, a módosítás végleges. Ha Ön az elutasítás, nem vehetnek részt nélkül [ügyfélszolgálaton](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>A Network Watcher létrehozása a portálon

Navigáljon a **minden szolgáltatás** > **hálózatkezelés** > **Network Watcher**. Kiválaszthatja, hogy szeretné engedélyezni a Network Watcher, előfizetések. Ez a művelet létrehoz egy Network Watcher minden régióban elérhető.

![A network watcher létrehozásához](./media/network-watcher-create/figure1.png)

Ha engedélyezi a Network Watcher a portál használatával, a Network Watcher-példány nevét értéke automatikusan *NetworkWatcher_region_name* ahol *region_name* felel meg az Azure-régió Ha a példány engedélyezve van. Engedélyezve van az USA nyugati középső régiójában a Network Watchert neve például *NetworkWatcher_westcentralus*.

A Network Watcher-példány automatikusan létrejön egy nevű erőforráscsoportban *NetworkWatcherRG*. Az erőforráscsoport jön létre, ha azt nem létezik.

Ha szeretne egy Network Watcher-példány és az erőforráscsoport nevét testreszabja kerül be, használhatja a Powershell, az Azure CLI-vel, a REST API vagy ARMClient módszerek az az alábbi szakaszok ismertetik. Az egyes lehetőségek az erőforráscsoport, egy Network Watcher létrehozása előtt léteznie kell.  

## <a name="create-a-network-watcher-with-powershell"></a>A Network Watcher létrehozása a PowerShell használatával

A Network Watcher-példány létrehozásához futtassa az alábbi példában:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>A Network Watcher létrehozásához az Azure CLI-vel

A Network Watcher-példány létrehozásához futtassa az alábbi példában:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>A Network Watcher létrehozásához REST API-val

A ARMclient hívás a REST API, PowerShell-lel történik. A ARMClient megtalálható a chocolatey [ARMClient a chocolatey-t](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>A network watcher létrehozásához

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

Most, hogy a Network Watcher-példány, elérhető szolgáltatások megismerése:

* [Topológia](network-watcher-topology-overview.md)
* [Csomagrögzítés](network-watcher-packet-capture-overview.md)
* [IP-forgalom ellenőrzése](network-watcher-ip-flow-verify-overview.md)
* [Következő ugrás](network-watcher-next-hop-overview.md)
* [Biztonságicsoport-nézet](network-watcher-security-group-view-overview.md)
* [NSG-t csoportforgalom naplózása](network-watcher-nsg-flow-logging-overview.md)
* [Virtuális hálózati átjáró hibáinak elhárítása](network-watcher-troubleshoot-overview.md)

Ha egy Network Watcher-példány, csomagrögzítés virtual machines szolgáltatáson belül is engedélyezheti. További információ [hozzon létre egy aktivált riasztás csomagrögzítés](network-watcher-alert-triggered-packet-capture.md)
