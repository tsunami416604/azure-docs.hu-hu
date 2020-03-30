---
title: Virtuális hálózatok csatlakoztatása virtuális hálózati társviszony-létesítéssel – Azure PowerShell
description: Ebben a cikkben megtudhatja, hogyan csatlakozhat virtuális hálózatok virtuális hálózati társviszony-létesítés, az Azure PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: caa620c2389bb6e2387636bc262ceb2de99d8e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201288"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuális hálózatok csatlakoztatása virtuális hálózati társviszony-létesítéssel a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Két virtuális gép létrehozása
* Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
* Virtuális gép üzembe helyezése mindkét virtuális hálózaton
* Virtuális gépek közötti kommunikáció

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz és a cikkben létrehozott összes többi erőforráshoz. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példa létrehoz egy *myVirtualNetwork1* nevű virtuális hálózatot a *10.0.0.0/16*címelőtaggal.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)segítségével. A következő példa 10.0.0.0/24-es címelőtaggal hoz létre egy alhálózati konfigurációt:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Írja be az alhálózati konfigurációt a virtuális hálózatba a [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)eszközzel, amely létrehozza az alhálózatot:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Hozzon létre egy virtuális hálózatot 10.1.0.0/16-os címelőtaggal és egy alhálózattal:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

Hozzon létre társviszony-létesítést [az Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)segítségével. A következő példa a *myVirtualNetwork1-et* a *myVirtualNetwork2-re egyenrangúa.*

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **Társviszony-létesítés** *kezdeményezése*. A társviszony-létesítés *a kezdeményezett* állapotban marad, amíg létre nem hozza a társviszony-létesítést a *myVirtualNetwork2* és *a myVirtualNetwork1*között. Hozzon létre egy társviszony-létesítést a *myVirtualNetwork2* és a *myVirtualNetwork1*között.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Az előző parancs végrehajtása után visszaadott kimenetben láthatja, hogy a **Társviszony-létesítménnyel** *kapcsolat van csatlakoztatva.* Az Azure módosította a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési állapotát *is.* Ellenőrizze, hogy a *myVirtualNetwork1-myVirtualNetwork2* társviszony-létesítési állapota a [Get-AzVirtualNetworkPeering kapcsolatra változott-e.](/powershell/module/az.network/get-azvirtualnetworkpeering) *Connected*

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Az egyik virtuális hálózat erőforrásai nem kommunikálhatnak a másik virtuális hálózat erőforrásaival, amíg a **társviszony-létesítések állapota** mindkét virtuális hálózatban *nincs csatlakoztatva.*

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gép [a New-AzVM](/powershell/module/az.compute/new-azvm). A következő példa létrehoz egy *myVm1* nevű virtuális gépet a *myVirtualNetwork1* virtuális hálózatban. A `-AsJob` beállítás létrehozza a virtuális gép a háttérben, így továbbra is a következő lépés. Amikor a rendszer kéri, adja meg a felhasználónevet és a jelszót, amelyhez be szeretne jelentkezni a virtuális gépre.

```azurepowershell-interactive
New-AzVm `
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
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a későbbi lépésekkel, amíg az Azure létre nem hozza a virtuális gép, és adja vissza a kimenetet a PowerShell.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

Csatlakozhat a virtuális gép nyilvános IP-címéhez az internetről. A [Get-AzPublicIpAddress használatával](/powershell/module/az.network/get-azpublicipaddress) adja vissza a virtuális gép nyilvános IP-címét. Az alábbi példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A következő paranccsal távoli asztali munkamenetet hozhat létre a *myVm1* virtuális géppel a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Létrejön egy Távoli asztali protokoll (.rdp) fájl, amely letöltődik a számítógépre, és megnyílik. Adja meg a felhasználónevet és a jelszót (előfordulhat, hogy a További lehetőségek lehetőséget kell **választania,** majd **másik fiókkal**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat), majd kattintson az **OK**gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

A *myVm1* virtuális gépen engedélyezze az Internet Control Message Protocol (ICMP) protokollt a Windows tűzfalon keresztül, így egy későbbi lépésben pingelheti ezt a virtuális *gépamyVm2-ről* a PowerShell használatával:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Bár a ping a cikkben a virtuális gépek közötti kommunikációra szolgál, az ICMP engedélyezése a Windows tűzfalon keresztül éles telepítésekhez nem ajánlott.

A *myVm2* virtuális géphez való csatlakozáshoz adja meg a következő parancsot egy parancssorban a *myVm1* virtuális gépen:

```
mstsc /v:10.1.0.4
```

Mivel engedélyezte a ping a *myVm1,* akkor most ping, hogy az IP-címet egy parancssorból a *myVm2* VM:

```
ping 10.0.0.4
```

Négy választ kap. Válassza le az RDP-munkameneteket a *myVm1* és *myVm2* virtuális gépről egyaránt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan csatlakoztathat két hálózatot ugyanabban az Azure-régióban, virtuális hálózati társviszony-létesítéssel. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#powershell) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

A saját számítógépét VPN-en keresztül [csatlakoztathatja egy virtuális hálózathoz,](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és kommunikálhat a virtuális hálózat ban vagy társviszonyban lévő virtuális hálózatok erőforrásaival. Az újrafelhasználható parancsfájlok a virtuális hálózati cikkekben szereplő feladatok nagy részének elvégzéséhez lásd a [parancsfájlmintákat.](powershell-samples.md)