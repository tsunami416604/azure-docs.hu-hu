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
ms.openlocfilehash: 0c2ff94d65e775a12fd76b3115aa8b69ae9a405f
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699108"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Hálózati forgalom továbbítása PowerShell-lel útválasztási táblázat

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

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Mielőtt létrehozhatna egy útválasztási táblázatot, hozzon létre egy erőforráscsoportot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* az ebben a cikkben létrehozott összes erőforrást. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy útválasztási táblázatot az [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). Az alábbi példa létrehoz egy útvonaltáblát, nevű *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat az útválasztási táblázat objektum lekérésével [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), hozzon létre egy útvonalat [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), majd az útválasztó-konfiguráció írni az útvonaltáblában a [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Mielőtt hozzárendelhetne egy útválasztási táblázatot egy alhálózathoz, akkor hozzon létre egy virtuális hálózatot és alhálózatot. Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre három alhálózatot hoz létre a három alhálózat-konfigurációit [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Az alábbi példa létrehoz három alhálózatkonfigurációinak *nyilvános*, *privát*, és *DMZ* alhálózatok:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

A virtuális hálózatot az alhálózat-konfigurációit írási [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely az alhálózatokat hoz létre a virtuális hálózat:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Társítsa a *myRouteTablePublic* útválasztási táblázatot az *nyilvános* alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) majd írja az alhálózati konfigurációt a a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>NVA létrehozása

Az NVA egy olyan virtuális gép, amely hálózati funkciót tölt be, például útválasztóként, tűzfalként vagy WAN-optimalizálóként működik.

Virtuális gép létrehozása előtt hozzon létre egy hálózati adaptert.

### <a name="create-a-network-interface"></a>Hozzon létre egy hálózati adaptert

A hálózati illesztő létrehozása, előtt kell beolvasni a virtuális hálózati azonosító [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), majd az alhálózati azonosító a [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Hozzon létre egy hálózati adaptert a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) a a *DMZ* alhálózatot, amelyen az IP-továbbítás engedélyezve van:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális Gépet, és a egy meglévő hálózati adaptert csatlakoztatni, létre kell hoznia egy Virtuálisgép-konfigurációt a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). A konfiguráció tartalmazza a hálózati illesztő az előző lépésben létrehozott. Amikor felhasználónév és jelszó megadására kéri, válassza ki a felhasználónevet, és jelentkezzen be a virtuális Gépet a kívánt jelszót. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

A Virtuálisgép-konfigurációt a virtuális gép létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A következő példában létrehozunk egy nevű virtuális Gépet *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A `-AsJob` lehetőség a háttérben létrehozza a virtuális Gépet, így a következő lépéssel is.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Két virtuális gép létrehozása a virtuális hálózatban, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózatra irányítja a rendszer a *privát* alhálózat egy későbbi lépésben a hálózati virtuális készüléken keresztül. 

A virtuális gép létrehozása a *nyilvános* alhálózat [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A következő példában létrehozunk egy nevű virtuális Gépet *myVmPublic* a a *nyilvános* alhálózatának a *myVirtualNetwork* virtuális hálózatot. 

```azurepowershell-interactive
New-AzureRmVm `
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
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépés a virtuális gép létrejött, és az Azure PowerShell kimeneti visszaadja.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

Használat [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) a nyilvános IP-címének visszaadásához a *myVmPrivate* virtuális Gépet. Az alábbi példában a nyilvános IP-címét adja vissza a *myVmPrivate* virtuális Géphez:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
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

Egy későbbi lépésben a tracert.exe parancs segítségével útválasztásának teszteléséhez. Tracert használja az Internet Control Message Protocol (ICMP), amelyet a Windows tűzfalon keresztül. Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPrivate* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Habár ebben a cikkben útválasztásának teszteléséhez útvonalkövetést használunk, ICMP engedélyezése a Windows tűzfalon, éles környezetekben üzemelő példányok esetében nem ajánlott.

Az Azure-ban a virtuális gép hálózati adapterében, az [IP-továbbítás engedélyezése](#enable-ip-forwarding) szakaszban engedélyezte az IP-továbbítást. A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Az operációs rendszer, az IP-továbbítást a *myVmNva*.

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

Ha már nincs rá szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) , távolítsa el az erőforráscsoportot és az összes benne található erőforrást.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és hozzárendelte egy alhálózathoz. Létrehozott egy egyszerű hálózati virtuális berendezésre, amely átirányította a forgalmat egy nyilvános alhálózatról egy privát alhálózatra. Különböző hálózati funkciók elvégzésére – például a tűzfal- és a WAN-optimalizálást végző előre konfigurált hálózati virtuális berendezések üzembe helyezése a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. További információ [PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása](tutorial-restrict-network-access-to-resources-powershell.md).
