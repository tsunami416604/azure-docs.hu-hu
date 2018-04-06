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
ms.openlocfilehash: 2490b96716519ef749dd1e3a1fbe6846c6b5d999
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítés PowerShell használatával

Kapcsolódás virtuális hálózatok egymástól a virtuális hálózati társviszony-létesítés. Virtuális hálózatok vannak társviszonyban, ha mindkét virtuális hálózat erőforrásainak képesek kommunikálnak egymással, ugyanahhoz késés és a sávszélesség, mintha az erőforrásokat ugyanabban a virtuális hálózatban. Ebből a cikkből megismerheti, hogyan:

* Két virtuális hálózatok létrehozása
* A virtuális hálózati társviszony-létesítés két virtuális hálózatok csatlakoztatása
* Virtuális gép (VM) telepítése minden virtuális hálózathoz
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

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

Az alhálózat-konfiguráció létrehozása [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Az alábbi példa létrehoz egy alhálózati konfigurációt 10.0.0.0/24 címet előtaggal kezdődik:

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

## <a name="peer-virtual-networks"></a>Társ virtuális hálózatok

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

Virtuális gép létrehozása minden egyes virtuális hálózatban, hogy egy későbbi lépésben közötti kommunikációt.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

A virtuális gép létrehozása [új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a virtuális gépek nevű *myVm1* a a *myVirtualNetwork1* virtuális hálózat. A `-AsJob` beállítás hoz létre a virtuális Gépet a háttérben, így továbbra is a következő lépéssel. Amikor a rendszer kéri, adja meg a felhasználónevet, és jelentkezzen be a virtuális Géphez a kívánt jelszót.

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

A virtuális gép létrehozásához néhány percet vesz igénybe. Ne folytassa a későbbi lépésekben mindaddig, amíg az Azure létrehozza a virtuális gép és a kimenetet visszaadja a PowerShell.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A virtuális gép nyilvános IP-cím csatlakozhat az internetről. Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza a nyilvános IP-címet a virtuális gépek. A következő példa a nyilvános IP-címét adja vissza a *myVm1* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVm1* VM a helyi számítógépről. Cserélje le `<publicIpAddress>` az előző parancs által visszaadott IP-címét.

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozott, a számítógép letölti és megnyitni. A felhasználónév és jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása), és kattintson a **OK** . A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

Az a *myVm1* VM engedélyezése az Internet Control Message Protocol (ICMP) keresztül a Windows tűzfal, akkor a ping paranccsal elérhető virtuális gép *myVm2* egy későbbi lépésben, a PowerShell használatával:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Bár a ping használatával Ez a cikk a virtuális gépek közötti kommunikáció, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

Csatlakozni a *myVm2* VM, adja meg a következő parancsot a parancssorba a a *myVm1* VM:

```
mstsc /v:10.1.0.4
```

Mivel a ping engedélyezve van a *myVm1*, most pingelhető az IP-cím a parancssorba a a *myVm2* VM:

```
ping 10.0.0.4
```

Négy választ kap. Válassza le az RDP-munkamenetekhez egyaránt *myVm1* és *myVm2*.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, azonos Azure-régióban, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés. Virtuális hálózatok, a másik partnert is meg is [támogató régiók](virtual-network-manage-peering.md#cross-region) és a [különböző Azure-előfizetések](create-peering-different-subscriptions.md#powershell), valamint létrehozása [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) rendelkező társviszony-létesítés. Virtuális hálózati társviszony-létesítés kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítési áttekintése](virtual-network-peering-overview.md) és [kezelheti a virtuális hálózati társviszony](virtual-network-manage-peering.md).

Is [saját számítógép csatlakoztatása egy virtuális hálózati](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy VPN-en keresztül és a virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok erőforrások. Tekintse meg a virtuális hálózati cikkekben ismertetett feladatok végrehajtásához, újrafelhasználható parancsfájlok [parancsfájl-mintában](powershell-samples.md).
