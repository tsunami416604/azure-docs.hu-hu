---
title: Virtuális gép hálózati útválasztási probléma diagnosztizálása – Azure PowerShell-lel |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja, hogyan útválasztási problémáinak diagnosztizálása virtuális gépek hálózati használatával az Azure Network Watcher következő ugrás képességét.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f793a201b3fbf57ac2f420c4f4e57a230bc11468
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299024"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Virtuális gép hálózati útválasztási probléma diagnosztizálása – Azure PowerShell-lel

Ebben a cikkben üzembe helyezése egy virtuális gépet (VM), és ezután ellenőrizze a kommunikációt egy IP-cím és az URL-címe. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Ha a helyi telepítése és használata PowerShell választja, ehhez a cikkhez az AzureRM PowerShell-modul 5.4.1-es vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépések megtételét, amíg létre nem jön a virtuális gép, és a PowerShell vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

Hálózati kommunikáció és a Network Watcher teszteléséhez először engedélyezni a network watchert a régióban, a tesztelni kívánt virtuális gép, és a Network Watcher következő ugrás képesség segítségével kommunikáció tesztelése.

## <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már van egy hálózati figyelő engedélyezve az USA keleti régiójában, a [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) paranccsal kérheti le a hálózati figyelőt. A következő példa egy meglévő, *NetworkWatcher_eastus* nevű hálózati figyelőt kér le, amely a *NetworkWatcherRG* erőforráscsoportban található:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Ha még nincs hálózati figyelő engedélyezve az USA keleti régiójában, használja a [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) parancsot a hálózati figyelő USA keleti régiójában való létrehozására:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>A következő ugrás használata

Az Azure automatikusan létrehoz útvonalakat az alapértelmezett célokhoz. Egyéni útvonalakat is létrehozhat, amelyekkel felülírhatja az Azure alapértelmezett útvonalait. Bizonyos esetekben az egyéni útvonalak kommunikációs hibákat eredményezhetnek. Útválasztás a virtuális gép teszteléséhez használja a [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) határozható meg, hogy a következő útválasztási Ugrás-címhez tartozó van irányuló parancsot.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Néhány másodperc elteltével a kimeneti figyelmeztet, amely a **nexthoptype típusa** van **Internet**, és hogy a **RouteTableId** van **Rendszerútvonal**. Ez az eredmény jelzi, hogy nincs-e egy érvényes útvonalat a cél.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Kapott kimenetre figyelmeztet, amely **nincs** van a **nexthoptype típusa**, és hogy a **RouteTableId** is **Rendszerútvonal**. Ez az eredmény azt jelzi, hogy létezik érvényes rendszerútvonal a cél felé, de nincs következő ugrás, hogy a forgalmat a cél felé irányítsa.

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

Elemzéséhez további útválasztási, tekintse át a hálózati adapter az érvényes útvonalak a [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) parancsot:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

A következő szöveget tartalmazó kimeneti adja vissza:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Amint láthatja, hogy az előző kimeneti útvonal a **AaddressPrefix** , **0.0.0.0/0** belüli többi útvonal címelőtagokhoz, ahol egy következő ugrási típusú címeknemtartóösszesforgalmatirányítja**Internet**. Amint is látható a kimenetben, azonban van egy alapértelmezett útvonalat a 172.16.0.0/12 előtagot, amely tartalmazza a 172.31.0.100 címet, a **nextHopType** van **nincs**. Az Azure létrehoz egy alapértelmezett útvonalat a 172.16.0.0/12 címhez, de amíg nincs oka rá, nem határozza meg a következő ugrás típusát. Ha például a 172.16.0.0/12-címtartományt adott a virtuális hálózat címteréhez, Azure módosítja a **nextHopType** való **virtuális hálózati** útvonal. Egy ellenőrzés jelenik majd meg **virtuális hálózati** , a **nextHopType**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy virtuális Gépet, és felderítette a hálózati útválasztás a virtuális gépről. Megtudta, hogy az Azure számos alapértelmezett utat létrehoz, és tesztelte az útválasztást két különböző cél felé. További tudnivalók az [Azure-beli útválasztásról](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és az [egyéni útvonalak létrehozásáról](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Virtuális gép kimenő kapcsolatok esetén azt is meghatározhatja, a késés és engedélyezett és letiltott a virtuális gép és a egy végpontot a Network watcherrel közötti hálózati forgalom [kapcsolódási hibák elhárítása](network-watcher-connectivity-powershell.md) képesség. A Network Watcher kapcsolódási figyelő funkcióval idővel egy virtuális Gépet és a egy végpontot, például az IP-cím vagy URL-cím közötti kommunikáció követheti nyomon. További információ [hálózati kapcsolat figyeléséhez](connection-monitor.md).