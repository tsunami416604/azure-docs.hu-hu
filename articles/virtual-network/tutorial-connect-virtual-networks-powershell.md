---
title: "Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony - PowerShell |} Microsoft Docs"
description: "Útmutató: virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítés."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/06/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: c7b3fa2b566ab02e7fb4a03055db83f1545895e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuális hálózatok csatlakoztatása a virtuális hálózati társviszony-létesítés PowerShell használatával

Kapcsolódás virtuális hálózatok egymástól a virtuális hálózati társviszony-létesítés. Virtuális hálózatok vannak társviszonyban, ha mindkét virtuális hálózat erőforrásainak képesek kommunikálnak egymással, ugyanahhoz késés és a sávszélesség, mintha az erőforrásokat ugyanabban a virtuális hálózatban. Ez a cikk ismerteti, létrehozása és a társviszony-létesítés két virtuális hálózatok. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Két virtuális hálózatok létrehozása
> * Virtuális hálózatok közötti társviszony létrehozása
> * Tesztelje a társviszony-létesítés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk az Azure PowerShell 3,6 vagy újabb verziója van szükség. Futtatás ` Get-Module -ListAvailable AzureRM` telepített verziója található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

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

A címelőtag a *myVirtualNetwork2* virtuális hálózat nem fedi át a címelőtagot a *myVirtualNetwork1* virtuális hálózat. Virtuális hálózatok átfedő címelőtagok nem partnert.

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

Társviszony virtuális hálózatok között, de nem tranzitív. Igen, például ha is egyenrangú *myVirtualNetwork2* való *myVirtualNetwork3*, kell létrehoznia a virtuális hálózatok közötti társviszony további *myVirtualNetwork2* és *myVirtualNetwork3*. Annak ellenére, hogy *myVirtualNetwork1* nincsenek társviszonyban, a *myVirtualNetwork2*, erőforrások *myVirtualNetwork1* csak hozzáférjen az erőforrásokhoz  *myVirtualNetwork3* Ha *myVirtualNetwork1* lett is társítottak, a *myVirtualNetwork3*. 

Társviszony-létesítés előtt éles virtuális hálózatok, javasoljuk, hogy alaposan feltérképezése a [társviszony-létesítési áttekintése](virtual-network-peering-overview.md), [kezelése a társviszony-létesítés](virtual-network-manage-peering.md), és [virtuális hálózati korlátok ](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Bár ez a cikk bemutatja, hogy társviszony-létesítés között két virtuális hálózat ugyanabban az előfizetésben és helyen, a virtuális hálózatok is partnert is [különböző régiókban](#register) és [különböző Azure-előfizetések](create-peering-different-subscriptions.md#powershell). Is létrehozhat [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) a társviszony-létesítés.

## <a name="test-peering"></a>Tesztelje a társviszony-létesítés

A társviszony-létesítés különböző virtuális hálózatokon lévő virtuális gépek közötti hálózati kommunikációhoz teszteléséhez virtuális gép telepítése minden egyes alhálózathoz, és majd a virtuális gépek közötti kommunikációra. 

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Virtuális gép létrehozása minden egyes virtuális hálózatban, hogy ellenőrizhesse a azokat egy későbbi lépésben közötti kommunikációt.

A virtuális gép létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példa létrehoz egy virtuális gépet, nevű *myVm1* a a *myVirtualNetwork1* virtuális hálózat. A `-AsJob` beállítás hoz létre a virtuális gép a háttérben, így továbbra is a következő lépéssel. Amikor a rendszer kéri, adja meg a felhasználónevet, és jelentkezzen be a virtuális gép kívánt jelszót.

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

Azure automatikusan hozzárendeli az 10.0.0.4 privát IP-címként a virtuális gép, mert 10.0.0.4 az első elérhető IP-cím *Alhalozat_1* a *myVirtualNetwork1*. 

A virtuális gép létrehozása a *myVirtualNetwork2* virtuális hálózat.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A virtuális gép létrehozásához néhány percet vesz igénybe. Bár a nem a visszaadott kimenetben Azure 10.1.0.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.1.0.4 az első elérhető IP-cím *Alhalozat_1* a *myVirtualNetwork2*. 

Ne folytassa a későbbi lépésekben mindaddig, amíg az Azure létrehozza a virtuális gépet, és a kimenetet visszaadja a PowerShell.

### <a name="test-virtual-machine-communication"></a>Teszt virtuális gép kommunikáció

Csatlakozhat a virtuális gép nyilvános IP-cím, az internetről. Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza egy virtuális gép nyilvános IP-címét. A következő példa a nyilvános IP-címét adja vissza a *myVm1* virtuális gépet:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVm1* virtuális gépet a helyi számítógépen. Cserélje le `<publicIpAddress>` az előző parancs által visszaadott IP-címét.

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozott, a számítógép letölti és megnyitni. A felhasználónév és jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása), és kattintson a  **OK**. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

A parancssorból, engedélyezze a Windows tűzfalon keresztül ping, akkor a ping paranccsal elérhető virtuális gép *myVm2* egy későbbi lépésben.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Bár a ping tesztelési a cikk ezt használja, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

Csatlakozni a *myVm2* virtuális gépet, adja meg a következő parancsot a parancssorba a a *myVm1* virtuális gépet:

```
mstsc /v:10.1.0.4
```

Mivel a ping engedélyezve van a *myVm1*, most pingelhető az IP-cím a parancssorból a a *myVm2* virtuális gép:

```
ping 10.0.0.4
```

Négy választ kap. Ha a virtuális gép neve pingelése (*myVm1*), az IP-cím helyett pingelés sikertelen, mert *myVm1* egy ismeretlen állomás neve. Azure alapértelmezett névfeloldás működik, az azonos virtuális hálózatban lévő virtuális gépek között, de nem a különböző virtuális hálózatokon lévő virtuális gépek között. Virtuális hálózatok közötti névfeloldás, kell [a saját DNS-kiszolgáló telepítése](virtual-networks-name-resolution-for-vms-and-role-instances.md) , vagy használjon [titkos tartományok Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Válassza le az RDP-munkamenetekhez egyaránt *myVm1* és *myVm2*.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

**<a name="register"></a>A globális virtuális hálózati társviszony-létesítési Preview regisztrálása**

Az azonos régiókban lévő virtuális hálózatok közötti társviszony kialakítása általánosan elérhető. Virtuális hálózatok különböző régiókban jelenleg előzetes verzióban érhetők társviszony. Lásd: [virtuális hálózati frissítések](https://azure.microsoft.com/updates/?product=virtual-network) az elérhető régiók. Virtuális hálózatok egyenrangú régiók között, először regisztrálnia kell az előzetes (belül minden partnert kívánt virtuális hálózat szerepel az előfizetés) az alábbi lépések végrehajtásával:

1. Regisztrálni az előfizetést, minden egyes virtuális hálózatot egyenrangú szeretné van a az előzetes kiadásban a következő parancsok beírásával:

    ```powershell-interactive
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
2. Győződjön meg arról, hogy be vannak jegyezve a az előzetes a következő parancs beírásával:

    ```powershell-interactive    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Virtuális hálózatok előtt különböző régiókban egyenrangú megkezdése a **RegistrationState** kimeneti kapja az előző parancs bevitele után **regisztrált** mindkét előfizetésekhez társviszony-létesítés sikertelen .

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, két hálózat kapcsolódás a virtuális hálózati társviszony-létesítés. Is [saját számítógép csatlakoztatása egy virtuális hálózati](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy VPN-en keresztül és a virtuális hálózatot, vagy nincsenek társviszonyban, virtuális hálózatok erőforrások.

Továbbra is a virtuális hálózati cikkekben ismertetett feladatok végrehajtásához újrafelhasználható parancsfájlok parancsfájl-példák.

> [!div class="nextstepaction"]
> [Virtuális hálózati parancsfájl minták](../networking/powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
