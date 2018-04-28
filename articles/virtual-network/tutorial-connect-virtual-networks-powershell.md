---
title: Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony - PowerShell |} Microsoft Docs
description: Ebből a cikkből megtanulhatja a virtuális hálózatok virtuális hálózati társviszony-létesítést, Azure PowerShell használatával kapcsolódni.
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
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítés PowerShell használatával

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork1* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

A [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancs használatával hozzon létre egy alhálózati konfigurációt. Az alábbi példa létrehoz egy alhálózati konfigurációt 10.0.0.0/24 címet előtaggal kezdődik:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Az alhálózat beállítása írni a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely az IP-alhálózatot hoz:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Hozzon létre egy virtuális hálózatot egy 10.1.0.0/16 címelőtag és egy alhálózattal rendelkezik:

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

Hozzon létre egy társviszony-létesítés [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). A következő példa társak *myVirtualNetwork1* való *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Adott vissza a korábbi parancs futtatása után a kimenetben megjelenik az **PeeringState** van *kezdeményezett*. A társviszony-létesítési marad a *kezdeményezett* állapot, amíg nem hoz létre a társviszony *myVirtualNetwork2* való *myVirtualNetwork1*. Hozzon létre a társviszony *myVirtualNetwork2* való *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Adott vissza a korábbi parancs futtatása után a kimenetben megjelenik az **PeeringState** van *csatlakoztatva*. Azure társviszony-létesítési állapota megváltozik a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési való *csatlakoztatva*. Ellenőrizze, hogy a társviszony-létesítési állapota a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítés változott *csatlakoztatva* rendelkező [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Amíg a többi virtuális hálózatán lévő erőforrásokat az egyik nem tud kommunikálni a virtuális hálózati erőforrásokhoz a **PeeringState** mindkét virtuális hálózatok esetében: *csatlakoztatva*. 

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Az alábbi példakód létrehozza a virtuális gépek nevű *myVm1* a a *myVirtualNetwork1* virtuális hálózat. A `-AsJob` beállítás hoz létre a virtuális Gépet a háttérben, így továbbra is a következő lépéssel. Amikor a rendszer kéri, adja meg a felhasználónevet, és jelentkezzen be a virtuális Géphez a kívánt jelszót.

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

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a későbbi lépésekben mindaddig, amíg az Azure létrehozza a virtuális gép és a kimenetet visszaadja a PowerShell.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A virtuális gép nyilvános IP-cím csatlakozhat az internetről. Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Az alábbi példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVm1* VM a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozott, a számítógép letölti és megnyitni. A felhasználónév és jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása), és kattintson a **OK** . A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

Az a *myVm1* VM engedélyezése az Internet Control Message Protocol (ICMP) keresztül a Windows tűzfal, akkor a ping paranccsal elérhető virtuális gép *myVm2* egy későbbi lépésben, a PowerShell használatával:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Bár a ping használatával Ez a cikk a virtuális gépek közötti kommunikáció, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

A *myVm2* virtuális géphez való csatlakozáshoz adja meg a következő parancsot egy parancssorban a *myVm1* virtuális gépen:

```
mstsc /v:10.1.0.4
```

Mivel a ping engedélyezve van a *myVm1*, most pingelhető az IP-cím a parancssorba a a *myVm2* VM:

```
ping 10.0.0.4
```

Négy választ kap. Válassza le az RDP-munkameneteket a *myVm1* és *myVm2* virtuális gépről egyaránt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, azonos Azure-régióban, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#powershell) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

Is [saját számítógép csatlakoztatása egy virtuális hálózati](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy VPN-en keresztül és a virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok erőforrások. Tekintse meg a virtuális hálózati cikkekben ismertetett feladatok végrehajtásához, újrafelhasználható parancsfájlok [parancsfájl-mintában](powershell-samples.md).
