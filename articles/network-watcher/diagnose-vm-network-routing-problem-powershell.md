---
title: A virtuális gép hálózati útválasztási probléma diagnosztizálása - Azure PowerShell |} Microsoft Docs
description: Ebből a cikkből megtanulhatja a probléma diagnosztizálása érdekében a virtuális gép hálózati útválasztási a következő Ugrás funkcióval az Azure hálózati figyelőt.
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
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>A virtuális gép hálózati útválasztási probléma diagnosztizálása - Azure PowerShell

Ebben a cikkben egy virtuális gép (VM) telepítése, és ellenőrizze a kommunikáció egy IP-címet, és az URL-CÍMÉT. Kommunikációs hiba, és hogy hogyan oldható meg okának megállapításához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, a cikkben a AzureRM PowerShell modul verziója 5.4.1 szükséges vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

A virtuális gépek létrehozása előtt létre kell hoznia a virtuális Gépet tartalmazó erőforráscsoport. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

A virtuális gép létrehozása [új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót.

```azurepowershell-interactive
$vM = New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A további lépéseket nem folytatható, amíg a virtuális gép létrehozása és a PowerShell-kimenet visszaadása.

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

Hálózati kommunikáció a hálózati figyelőt teszteléséhez először engedélyezze a régióban, a tesztelni kívánt virtuális gép olyan hálózati figyelőt, és hálózati figyelőt a következő ugrás funkció segítségével tesztelheti a kommunikációs.

## <a name="enable-network-watcher"></a>Engedélyezze a hálózati figyelőt

Ha már van egy hálózati figyelőt engedélyezve az USA keleti régiójában, [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher) beolvasása a hálózati figyelőt. A következő példa egy meglévő hálózati figyelőt nevű lekéri *NetworkWatcher_eastus* , hogy a *NetworkWatcherRG* erőforráscsoport:

```azurepowershell-interactive
$networkWatcher = Get-AzureRmNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Ha még nem rendelkezik a hálózati figyelő engedélyezve van, az USA keleti régiójában, [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) egy hálózati figyelő létrehozásához az USA keleti régiójában:

```azurepowershell-interactive
$networkWatcher = New-AzureRmNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Használja a következő ugrás

Azure automatikusan létrehozza az alapértelmezett célhelyekre útvonalak. Létrehozhat egyéni útvonalakat, amelyek felülírják az alapértelmezett útvonalak. Egyes esetekben egyéni útvonalak okozhat kommunikáció sikertelen lesz. A virtuális gép útválasztási teszteléséhez használja a [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) parancs használatával határozható meg, hogy a következő útválasztási Ugrás irányuló-címhez tartozó induló adatforgalmat.

Tesztelje a virtuális gép kimenő kommunikáció www.bing.com IP-címek egyikére:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Néhány másodpercen belül a kimeneti figyelmeztet, amely a **NextHopType** van **Internet**, és hogy a **RouteTableId** van **Rendszerútvonal**. Ez az eredmény lehetővé teszi, hogy van-e a cél egy érvényes útvonalat.

A virtuális gép 172.31.0.100 kimenő kommunikáció teszteléséhez:

```azurepowershell-interactive
Get-AzureRmNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Adott vissza a kimeneti figyelmeztet, amely **nincs** van a **NextHopType**, és hogy a **RouteTableId** is **Rendszerútvonal**. Ez az eredmény tudatja, míg a cél érvényes rendszerútvonal, nincs irányíthatja a forgalmat a cél nem következő ugrás.

## <a name="view-details-of-a-route"></a>Egy útvonal részleteinek megtekintése

Elemzéséhez további útválasztási, tekintse át a hálózati illesztő – a hatékony útvonalait a [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) parancs:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Kimeneti, amely tartalmazza a következő szöveget ad vissza:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Ahogyan az előző kimeneti útvonal láthatja a **AaddressPrefix** a **0.0.0.0/0** továbbítja a nem szánt belüli más útvonal címelőtagokat akövetkezőugrásacímekforgalom**Internet**. Ahogyan azt is láthatja a kimenetben, azonban nincs olyan alapértelmezett útvonalat a 172.16.0.0/12 előtagot, amely tartalmazza a 172.31.0.100 cím, a **nextHopType** van **nincs**. Azure létrehoz egy alapértelmezett útvonalat 172.16.0.0/12, de nem adja meg a következő ugrás típusa, amíg nem ok arra, hogy. Ha például a 172.16.0.0/12 címtartomány hozzáadni a virtuális hálózat a címtér, Azure módosítja a **nextHopType** való **virtuális hálózati** az útvonal. Egy ellenőrzés ezután volna meg lehet jeleníteni **virtuális hálózati** , a **nextHopType**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy virtuális gép létrehozása, és felderítette a hálózati útválasztást a virtuális gépről. Megtudta, hogy Azure több alapértelmezett útvonalak és tesztelt útválasztási két különböző helyekre. További információ [az Azure útválasztási](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és [hozhat létre egyéni útvonalakat](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Kimenő virtuális gép kapcsolatok, akkor is meghatározhatja, a késés és engedélyezett és letiltott hálózati forgalmat a virtuális gép és a hálózati figyelőt egy végpont között [kapcsolati hibáinak elhárítása](network-watcher-connectivity-powershell.md) funkció. A hálózati figyelőt kapcsolat figyelő funkcióval időbeli egy virtuális gép és egy végpontot, például az IP-címet vagy URL-címe, közötti kommunikáció figyelheti. Megtudhatja, hogyan, lásd: [a hálózati kapcsolat figyelése](connection-monitor.md).