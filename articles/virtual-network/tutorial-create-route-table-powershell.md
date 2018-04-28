---
title: Irányítható a hálózati forgalom Azure PowerShell |} Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hálózati forgalom irányítására egy PowerShell-lel útválasztási táblázathoz.
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
ms.openlocfilehash: 2aca1de567dbd4d37daf7f9dd7c407b669396a47
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Hálózati forgalom egy útválasztási táblázathoz PowerShell használatával

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

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Mielőtt létrehozna egy útválasztási táblázatot, hozzon létre egy erőforráscsoportot, a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* létre ebben a cikkben minden erőforráshoz. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy útvonaltábla [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). Az alábbi példa létrehoz egy útválasztási táblázatot nevű *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Útvonal létrehozása

Hozzon létre egy útvonalat útvonal tábla objektum lekérésével [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), hozzon létre egy útvonalat [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), majd az útválasztó-konfiguráció írni az útvonaltáblában a [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

Ahhoz, hogy társíthasson egy útválasztási táblázatot az alhálózathoz, akkor hozzon létre egy virtuális hálózati és alhálózati. Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre három alhálózatok hozzon létre három alhálózati konfigurációt [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Az alábbi példakód létrehozza a három alhálózati beállítások *nyilvános*, *titkos*, és *DMZ* alhálózatok:

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

Az alhálózati beállítások írni a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely az alhálózatok hoz létre a virtuális hálózat:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Társítsa a *myRouteTablePublic* útválasztási táblázatot, hogy a *nyilvános* alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) majd írja be az alhálózati konfigurációját, és a a virtuális hálózati [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

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

Virtuális gép létrehozása előtt hozzon létre egy adott hálózati csatoló.

### <a name="create-a-network-interface"></a>A hálózati illesztő létrehozása

Mielőtt létrehozna egy adott hálózati csatoló, kell beolvasni a virtuális hálózati azonosítót a [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), majd az alhálózati azonosító a [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Hozzon létre egy hálózati illesztő – [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) a a *DMZ* alhálózat IP-továbbítás engedélyezve:

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

Hozzon létre egy virtuális Gépet, és a meglévő hálózati illesztő csatlakoztatni, létre kell hoznia a Virtuálisgép-konfiguráció [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). A konfiguráció tartalmazza a hálózati illesztő az előző lépésben létrehozott. A felhasználónév és jelszó megadását kéri, válassza ki a felhasználónevet és jelentkezzen be a virtuális Géphez a kívánt jelszót. 

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

A virtuális gép a Virtuálisgép-konfiguráció létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a virtuális gépek nevű *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A `-AsJob` beállítás hoz létre a virtuális Gépet a háttérben, így továbbra is a következő lépéssel.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózat, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózati annak biztosítására, hogy a *titkos* alhálózatot a hálózati virtuális készülék egy későbbi lépésben keresztül. 

A virtuális gép létrehozása a *nyilvános* alhálózat [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a virtuális gépek nevű *myVmPublic* a a *nyilvános* alhálózata a *myVirtualNetwork* virtuális hálózat. 

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

A virtuális gép létrehozása a *titkos* alhálózat.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A következő lépéssel nem folytatható, amíg a virtuális gép létrehozása Azure PowerShell kimeneti visszaadja.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza a nyilvános IP-címét a *myVmPrivate* virtuális gép. A következő példa a nyilvános IP-címét adja vissza a *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVmPrivate* VM a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához. 

Egy későbbi lépésben a tracert.exe parancs segítségével útválasztási tesztelése. Tracert használja az Internet Control üzenet Protocol (ICMP), amely a Windows tűzfalon keresztül megtagadva. Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPrivate* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Bár a nyomkövetést ebben a cikkben útválasztási tesztelésére szolgál, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

Az Azure-ban a virtuális gép hálózati adapterében, az [IP-továbbítás engedélyezése](#enable-ip-forwarding) szakaszban engedélyezte az IP-továbbítást. A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Az operációs rendszerben az IP-továbbítás engedélyezése a *myVmNva*.

A parancssorból a *myVmPrivate* VM, a távoli asztal a *myVmNva*:

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

Ha már nincs szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és az alhálózathoz társított. Létrehozott egy egyszerű hálózati virtuális készülék portleképezéseit titkos alhálózathoz nyilvános alhálózatból származó forgalmat. Hálózati funkciókat, például a tűzfal és a WAN-optimalizálást végző előre konfigurált hálózati virtuális készülékeket különböző központi telepítése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. Megtudhatja, hogyan, lásd: [hálózati hozzáférés korlátozása PaaS erőforrások](tutorial-restrict-network-access-to-resources-powershell.md).
