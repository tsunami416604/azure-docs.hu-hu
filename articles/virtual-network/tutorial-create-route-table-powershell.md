---
title: Irányítható a hálózati forgalom Azure PowerShell-lel |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan irányíthatja a hálózati forgalom útválasztási táblázat PowerShell-lel.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: ad09d41b004fe2b8a4090dce16a7a70f9c57b1f3
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651499"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Hálózati forgalom továbbítása PowerShell-lel útválasztási táblázat

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Útválasztási táblázat létrehozása
* Útvonal létrehozása
* Több alhálózattal rendelkező virtuális hálózat létrehozása
* Útválasztási táblázat társítása alhálózattal
* Forgalmat irányító hálózati virtuális berendezés létrehozása
* Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
* Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata PowerShell választja, ehhez a cikkhez az Azure PowerShell-modul verzióját 1.0.0 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Mielőtt létrehozhatna egy útválasztási táblázatot, hozzon létre egy erőforráscsoportot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* az ebben a cikkben létrehozott összes erőforrást.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy útválasztási táblázatot az [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). Az alábbi példa létrehoz egy útvonaltáblát, nevű *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az útválasztási táblázat objektum lekérésével [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), hozzon létre egy útvonalat [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig), majd az útválasztó-konfiguráció írni az útvonaltáblában a [ Set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Mielőtt hozzárendelhetne egy útválasztási táblázatot egy alhálózathoz, akkor hozzon létre egy virtuális hálózatot és alhálózatot. A virtuális hálózat létrehozása [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy nevű virtuális hálózatot *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre három alhálózatot hoz létre a három alhálózat-konfigurációit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Az alábbi példa létrehoz három alhálózatkonfigurációinak *nyilvános*, *privát*, és *DMZ* alhálózatok:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

A virtuális hálózatot az alhálózat-konfigurációit írási [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), amely az alhálózatokat hoz létre a virtuális hálózat:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Társítsa a *myRouteTablePublic* útválasztási táblázatot az *nyilvános* alhálózat [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) majd írja az alhálózati konfigurációt a a virtuális hálózat [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

Virtuális gép létrehozása előtt hozzon létre egy hálózati adaptert.

### <a name="create-a-network-interface"></a>Hozzon létre egy hálózati adaptert

A hálózati illesztő létrehozása, előtt kell beolvasni a virtuális hálózati azonosító [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), majd az alhálózati azonosító a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) a a *DMZ* alhálózatot, amelyen az IP-továbbítás engedélyezve van:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális Gépet, és a egy meglévő hálózati adaptert csatlakoztatni, létre kell hoznia egy Virtuálisgép-konfigurációt a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A konfiguráció tartalmazza a hálózati illesztő az előző lépésben létrehozott. Amikor felhasználónév és jelszó megadására kéri, válassza ki a felhasználónevet, és jelentkezzen be a virtuális Gépet a kívánt jelszót.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

A Virtuálisgép-konfigurációt a virtuális gép létrehozása [New-azvm parancsmag](/powershell/module/az.compute/new-azvm). A következő példában létrehozunk egy nevű virtuális Gépet *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A `-AsJob` lehetőség a háttérben létrehozza a virtuális Gépet, így a következő lépéssel is.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Két virtuális gép létrehozása a virtuális hálózatban, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózatra irányítja a rendszer a *privát* alhálózat egy későbbi lépésben a hálózati virtuális készüléken keresztül.

A virtuális gép létrehozása a *nyilvános* alhálózat [New-azvm parancsmag](/powershell/module/az.compute/new-azvm). A következő példában létrehozunk egy nevű virtuális Gépet *myVmPublic* a a *nyilvános* alhálózatának a *myVirtualNetwork* virtuális hálózatot.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

A virtuális gép létrehozása a *privát* alhálózat.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépés a virtuális gép létrejött, és az Azure PowerShell kimeneti visszaadja.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

Használat [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) a nyilvános IP-címének visszaadásához a *myVmPrivate* virtuális Gépet. Az alábbi példában a nyilvános IP-címét adja vissza a *myVmPrivate* virtuális Géphez:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő paranccsal hozzon létre egy távoli asztali munkamenetet a *myVmPrivate* virtuális Gépet a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.

Egy későbbi lépésben a `tracert.exe` parancs útválasztásának teszteléséhez használható. Tracert használja az Internet Control Message Protocol (ICMP), amelyet a Windows tűzfalon keresztül. Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPrivate* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Habár ebben a cikkben útválasztásának teszteléséhez útvonalkövetést használunk, ICMP engedélyezése a Windows tűzfalon, éles környezetekben üzemelő példányok esetében nem ajánlott.

Engedélyezett IP-továbbítást a virtuális hálózati adapter IP-továbbítás engedélyezése az Azure-ban. A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Az operációs rendszer, az IP-továbbítást a *myVmNva*.

A parancsot a parancssorba a *myVmPrivate* virtuális Gépet, a távoli asztal a *myVmNva*:

``` 
mstsc /v:myvmnva
```

Az operációs rendszeren belüli IP-továbbítás engedélyezéséhez adja meg a következő parancsot a PowerShellben a *myVmNva* virtuális gépről:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Indítsa újra a *myVmNva* virtuális gépet – ez megszakítja a távoli asztali munkamenetet is.

Miközben továbbra is csatlakozik a *myVmPrivate* virtuális géphez, hozzon létre távoli asztali kapcsolatot a *myVmPublic* virtuális géppel, miután a *myVmNva* virtuális gép újraindult:

``` 
mstsc /v:myVmPublic
```

Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPublic* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

A *myVmPublic* virtuális gépről a *myVmPrivate* virtuális gépre irányuló hálózati forgalom útválasztásának teszteléséhez adja meg a következő parancsot a PowerShellben a *myVmPublic* virtuális gépen:

```
tracert myVmPrivate
```

A válasz a következő példához hasonló:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Láthatja, hogy az első ugrás a 10.0.2.4 cím, amely az NVA magánhálózati IP-címe. A második ugrás a 10.0.1.4 cím – ez a *myVmPrivate* virtuális gép magánhálózati IP-címe. A*myRouteTablePublic* útválasztási táblázathoz hozzáadott és a *Magánjellegű* alhálózathoz rendelt útvonal miatt az Azure az NVA-n keresztül továbbította a forgalmat ahelyett, hogy közvetlenül a *Privát* alhálózatra továbbította volna.

Zárja be a *myVmPublic* virtuális gépre irányuló távoli asztali munkamenetet. Ez nem bontja a *myVmPrivate* virtuális géppel való kapcsolatot.

A *myVmPrivate* virtuális gépről a *myVmPublic* virtuális gépre irányuló hálózati forgalom útválasztásának teszteléséhez adja meg a következő parancsot egy parancssorban a *myVmPrivate* virtuális gépen:

```
tracert myVmPublic
```

A válasz a következő példához hasonló:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Láthatja, hogy a rendszer a *myVmPrivate* virtuális gépről közvetlenül a *myVmPublic* virtuális gépre irányítja a forgalmat. Alapértelmezés szerint az Azure közvetlenül irányítja a forgalmat az alhálózatok között.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű hálózati virtuális berendezésre, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Különböző hálózati funkciók elvégzésére – például a tűzfal- és a WAN-optimalizálást végző előre konfigurált hálózati virtuális berendezések üzembe helyezése a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. További információ [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources-powershell.md).