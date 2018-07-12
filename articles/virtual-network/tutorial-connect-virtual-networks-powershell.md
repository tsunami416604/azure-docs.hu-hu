---
title: Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony létesítése – PowerShell |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja a virtuális hálózatok összekapcsolása virtuális hálózati társviszony-létesítés, az Azure PowerShell-lel.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3b4a67a06d628040d155a0fe2d78beb2eee25090
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477762"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony PowerShell-lel

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Előtt egy virtuális hálózatot hoz létre, akkor hozzon létre egy erőforráscsoportot a virtuális hálózatot és más ebben a cikkben létrehozott összes erőforrást. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot *myVirtualNetwork1* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

A [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancs használatával hozzon létre egy alhálózati konfigurációt. A következő példában létrehozunk egy alhálózati konfigurációt a 10.0.0.0/24 címelőtaggal:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Írja az alhálózati konfigurációt a virtuális hálózatba a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely létrehozza az alhálózatot:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Hozzon létre egy virtuális hálózatot egy 10.1.0.0/16 címelőtagot és a egy alhálózatot:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

A társviszony-létesítés [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). A következő példa társaknak *myVirtualNetwork1* való *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Vissza az előző parancs végrehajtása után a kimenetben láthatja a **PeeringState** van *kezdeményezve*. A társviszony-létesítési marad a *kezdeményezve* állapot, amíg nem hoz létre a társviszonyt *myVirtualNetwork2* való *myVirtualNetwork1*. A társviszony-létesítés *myVirtualNetwork2* való *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Vissza az előző parancs végrehajtása után a kimenetben láthatja a **PeeringState** van *csatlakoztatva*. Az Azure is módosította a társviszony-létesítés állapota a *myVirtualNetwork1 – myVirtualNetwork2* a társviszony-létesítési *csatlakoztatva*. Ellenőrizze, hogy a társviszony-létesítés állapota a *myVirtualNetwork1 – myVirtualNetwork2* társviszony-létesítés változott *csatlakoztatva* a [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Amíg a többi virtuális hálózatban lévő erőforrásokra erőforrásaikat egy virtuális hálózatot nem lehet kommunikálni a **PeeringState** tartozó mindkét virtuális hálózatok társviszony-létesítések *csatlakoztatva*. 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. A következő példában létrehozunk egy nevű virtuális Gépet *myVm1* a a *myVirtualNetwork1* virtuális hálózatot. A `-AsJob` lehetőség a háttérben létrehozza a virtuális Gépet, így a következő lépéssel is. Amikor a rendszer kéri, adja meg a felhasználónevet, és jelentkezzen be a virtuális géphez a kívánt jelszót.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Csak akkor folytassa a későbbi lépésekben Azure a virtuális Gépet hoz létre, és a kimenetet visszaadja a PowerShell.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

Virtuális gép nyilvános IP-cím csatlakozhat az internetről. Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Az alábbi példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A következő paranccsal hozzon létre egy távoli asztali munkamenetet a *myVm1* virtuális Gépet a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Egy Remote Desktop Protocol (.rdp) fájlt létrehozni, letölt a számítógépre, és megnyílik. Adja meg a felhasználónevet és jelszót (Előfordulhat, hogy ki kell választania **további lehetőségek**, majd **másik fiók használata**adja meg a virtuális gép létrehozásakor beírt hitelesítő adatok), és kattintson a **OK** . A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

Az a *myVm1* virtuális Gépet, így a virtuális gépről megpingelheti a az Internet Control Message Protocol (ICMP) keresztül a Windows tűzfal engedélyezése *myVm2* egy későbbi lépésben, a PowerShell használatával:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Habár ez a cikk a virtuális gépek közötti kommunikáció pingelést használunk, ICMP engedélyezése a Windows tűzfalon, éles környezetekben üzemelő példányok esetében nem ajánlott.

A *myVm2* virtuális géphez való csatlakozáshoz adja meg a következő parancsot egy parancssorban a *myVm1* virtuális gépen:

```
mstsc /v:10.1.0.4
```

Mivel engedélyezte a pingelést a *myVm1*, most már pingelheti azt IP-cím egy parancssorból a a *myVm2* virtuális Géphez:

```
ping 10.0.0.4
```

Ekkor négy választ kap. Válassza le az RDP-munkameneteket a *myVm1* és *myVm2* virtuális gépről egyaránt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan virtuális hálózatok közötti társviszony az azonos Azure-régióban, két hálózat kapcsolódni. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#powershell) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

Is [csatlakoztatni szeretné saját számítógépét egy virtuális hálózathoz](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VPN,-kapcsolaton keresztül, és lépjen kapcsolatba az erőforrásokkal egy virtuális hálózaton, vagy a társviszonyban álló virtuális hálózatba. Lásd: a virtuális hálózat cikkekben ismertetett feladatok végrehajtásához újrafelhasználható szkripteket [mintaszkriptjeire](powershell-samples.md).
