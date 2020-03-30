---
title: Virtuálisgép-hálózati útválasztási probléma diagnosztizálása – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ebben a cikkben megtudhatja, hogyan diagnosztizálhatja a virtuális gép hálózati útválasztási probléma az Azure Network Watcher következő ugrási képesség használatával.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: b5a636471eab188dc8648761afedd81694331953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834705"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Virtuálisgép-hálózati útválasztási probléma diagnosztizálása – Azure PowerShell

Ebben a cikkben üzembe helyez egy virtuális gépet (VM), majd ellenőrizze a kommunikáció t ip-címés URL-cím. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja `Az` a PowerShellt, ez a cikk az Azure PowerShell-modult igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.



## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)segítségével. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Hozza létre a virtuális gép [new-azvm.](/powershell/module/az.compute/new-azvm) Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépések megtételét, amíg létre nem jön a virtuális gép, és a PowerShell vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A hálózati kommunikáció teszteléséhez a Hálózati figyelővel először engedélyeznie kell egy hálózati figyelőt abban a régióban, amelyben a tesztelni kívánt virtuális gép található, majd a Hálózati figyelő következő ugrási képességét kell használnia a kommunikáció teszteléséhez.

## <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van a hálózatfigyelő az USA keleti régiójában, a [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) segítségével szerezze be a hálózatfigyelőt. A következő példa egy meglévő, *NetworkWatcher_eastus* nevű hálózati figyelőt kér le, amely a *NetworkWatcherRG* erőforráscsoportban található:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Ha még nincs engedélyezve a hálózatfigyelő az USA keleti régiójában, a [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) segítségével hozzon létre egy hálózatfigyelőt az USA keleti régiójában:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. A virtuális gépről történő útválasztás teszteléséhez használja a [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) parancsot a következő útválasztási ugrás meghatározásához, ha a forgalom egy adott címre irányul.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Néhány másodperc múlva a kimenet arról tájékoztat, hogy a **NextHopType** **az Internet**, és hogy a **RouteTableId** **rendszerútvonal.** Ez az eredmény tudatja Önvel, hogy van egy érvényes útvonal a célhoz.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

A visszaadott kimenet arról tájékoztat, hogy a **Nincs** a **NextHopType**, és hogy a **RouteTableId** egyben **rendszerútvonal**is. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Az útválasztás további elemzéséhez tekintse át a hálózati adapter hatékony útvonalait a [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) paranccsal:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

A következő szöveget tartalmazó kimenet et adja vissza:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Amint az előző kimenetben látható, a **0.0.0.0/0 AddressPrefix (0.0.0.0/0)** **címelőtaggal** rendelkező útvonal az internet következő ugrásával az **összes**olyan forgalmat irányítja, amely nem más útvonal címelőtagaiba tartozik. Ahogy a kimenetben is látható, bár van egy alapértelmezett útvonal a 172.16.0.0/12 előtaghoz, amely tartalmazza a 172.31.0.100 címet, a **nextHopType** **nincs**. Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például hozzáadta a 172.16.0.0/12 címtartományt a virtuális hálózat címteréhez, az Azure a **nextHopType-ot** **virtuális hálózatra** módosítja az útvonalhoz. Az ellenőrzés ekkor a **virtuális hálózatot** fogja megjeleníteni **nextHopType**néven.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével eltávolíthatja az erőforráscsoportot és az összes benne lévő erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy virtuális gép, és diagnosztizált hálózati útválasztás a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Kimenő virtuálisgép-kapcsolatok esetén is meghatározhatja a késést, és engedélyezett és megtagadta a hálózati forgalmat a virtuális gép és egy végpont között a Network Watcher [kapcsolathibaelhárítási](network-watcher-connectivity-powershell.md) képesség használatával. A virtuális gép és a végpont, például egy IP-cím vagy URL közötti kommunikáció figyelhető a Network Watcher kapcsolatfigyelő funkció használatával. Ebből, hogy miként, olvassa el [a Hálózati kapcsolat figyelése ..](connection-monitor.md)
