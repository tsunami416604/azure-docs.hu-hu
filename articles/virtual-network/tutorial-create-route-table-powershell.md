---
title: Hálózati forgalom irányítása Azure PowerShell | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan irányíthat hálózati forgalmat egy útvonaltáblával a PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 986371e479f7718fff2e1699401987cb0ca8f623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73163988"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Hálózati forgalom útvonaltáblával történő útvonalának irányítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy adott virtuális hálózaton belül az Azure alapértelmezés szerint automatikusan elosztja a forgalmat az összes alhálózat között. Az Azure alapértelmezett útválasztását felülírhatja saját maga által létrehozott útvonalakkal. Az egyéni útvonalak létrehozása akkor lehet hasznos, hálózati virtuális berendezésen (NVA) keresztül kívánja irányítani az alhálózatok közötti forgalmat. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Útválasztási táblázat létrehozása
* Útvonal létrehozása
* Több alhálózattal rendelkező virtuális hálózat létrehozása
* Útválasztási táblázat társítása alhálózattal
* Forgalmat irányító hálózati virtuális berendezés létrehozása
* Virtuális gépek (VM) üzembe helyezése különböző alhálózatokban
* Forgalom irányítása egyik alhálózatról hálózati virtuális berendezésen keresztül

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Útvonaltábla létrehozása előtt hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a cikkben létrehozott összes erőforráshoz.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy útvonaltáblát a [New-AzRouteTable segítségével.](/powershell/module/az.network/new-azroutetable) A következő példa létrehoz egy *myRouteTablePublic*nevű útvonaltáblát.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Útvonal létrehozása

Útvonal létrehozása az útvonaltábla-objektum [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)segítségével történő beolvasásával , hozzon létre egy útvonalat [az Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)segítségével, majd írja az útvonalkonfigurációt az útvonaltáblába [a Set-AzRouteTable segítségével.](/powershell/module/az.network/set-azroutetable)

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

Mielőtt egy útvonaltáblát egy alhálózathoz társíthatna, létre kell hoznia egy virtuális hálózatot és alhálózatot. Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példa létrehoz egy *myVirtualNetwork* nevű virtuális hálózatot a *10.0.0.0/16*címelőtaggal.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre három alhálózatot három alhálózati konfiguráció létrehozásával a [New-AzVirtualNetworkSubnetConfig segítségével.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) A következő példa három alhálózati konfigurációt hoz létre *a Nyilvános,* *Privát*és *DMZ* alhálózatokhoz:

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

Írja be az alhálózati konfigurációkat a virtuális hálózatba a [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)segítségével, amely létrehozza a virtuális hálózat alhálózatait:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Társítsa a *myRouteTablePublic* útvonaltáblát a [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) *alhálózathoz* a nyilvános alhálózathoz, majd írja az alhálózati konfigurációt a virtuális hálózatba a [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)segítségével.

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

### <a name="create-a-network-interface"></a>Hálózati adapter létrehozása

A hálózati csatoló létrehozása előtt be kell olvasnia a virtuális hálózati azonosítót a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)segítségével, majd az alhálózati azonosítót a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)segítségével. Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface-el](/powershell/module/az.network/new-aznetworkinterface) a *DMZ* alhálózatban, engedélyezve van az IP-továbbítás:

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

Virtuális gép létrehozásához és egy meglévő hálózati adapter csatlakoztatásához először létre kell hoznia egy virtuális gép konfigurációját a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)segítségével. A konfiguráció tartalmazza az előző lépésben létrehozott hálózati illesztőt. Amikor a rendszer felhasználónevet és jelszót kér, válassza ki azt a felhasználónevet és jelszót, amelyen be szeretne jelentkezni a virtuális gépbe.

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

Hozza létre a virtuális gép a virtuális gép [konfigurációja a New-AzVM.](/powershell/module/az.compute/new-azvm) A következő példa létrehoz egy *myVmNva*nevű virtuális gép.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A `-AsJob` beállítás létrehozza a virtuális gép a háttérben, így továbbra is a következő lépés.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózatban, így ellenőrizheti, hogy a *nyilvános* alhálózatból érkező forgalom egy későbbi lépésben a hálózati virtuális berendezésen keresztül a *magánalhálózathoz* kerüljön.

Hozzon létre egy virtuális gép a *nyilvános* alhálózatban a [New-AzVM.](/powershell/module/az.compute/new-azvm) A következő példa létrehoz egy *myVmPublic* nevű virtuális gépet a *myVirtualNetwork* virtuális hálózat *nyilvános* alhálózatában.

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

Hozzon létre egy virtuális gép a *privát* alhálózatban.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a következő lépéssel, amíg a virtuális gép létre nem jön, és az Azure visszaadja a kimenetet a PowerShellnek.

## <a name="route-traffic-through-an-nva"></a>Forgalom irányítása NVA-n keresztül

A [Get-AzPublicIpAddress használatával](/powershell/module/az.network/get-azpublicipaddress) adja vissza a *myVmPrivate* virtuális gép nyilvános IP-címét. A következő példa a *myVmPrivate* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő paranccsal távoli asztali munkamenetet hozhat létre a *myVmPrivate* virtuális géppel a helyi számítógépről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.

Egy későbbi lépésben `tracert.exe` a parancs az útválasztás tesztelésére szolgál. A Tracert az Internet Control Message Protocol (ICMP) protokollt használja, amelyet a Windows tűzfal on keresztül megtagad. Engedélyezze az ICMP-t a Windows tűzfalon. Ehhez adja meg a *myVmPrivate* virtuális gépen a következő PowerShell-parancsot:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Bár a nyomkövetési útvonal a jelen cikkben szereplő útválasztás tesztelésére szolgál, az ICMP engedélyezése a Windows tűzfalon keresztül éles telepítésekhez nem ajánlott.

Az Azure-ban a virtuális gép hálózati adapterében, az IP-továbbítás engedélyezése szakaszban engedélyezte az IP-továbbítást. A virtuális gépen, az operációs rendszeren, vagy egy a virtuális gépen futó alkalmazáson belül szintén működnie kell a hálózati forgalom továbbításának. Ip-továbbítás engedélyezése a *myVmNva*operációs rendszerén belül.

A *myVmPrivate* virtuális gép parancssorából a távoli asztalról a *myVmNva-ra:*

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
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
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

Ha már nincs rá szükség, az [Eltávolítás-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével távolítsa el az erőforráscsoportot és az összes benne lévő erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útvonaltáblát, és egy alhálózathoz társította. Létrehozott egy egyszerű hálózati virtuális berendezést, amely egy nyilvános alhálózatról egy privát alhálózatba irányította a forgalmat. Telepítsen számos előre konfigurált hálózati virtuális készüléket, amelyek hálózati funkciókat látnak el, például tűzfalat és WAN-optimalizálást az [Azure Piactérről.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) További információ az útválasztásról: [Az útválasztás áttekintése](virtual-networks-udr-overview.md); [Útválasztási táblázat kezelése](manage-route-table.md).

Egy virtuális hálózaton belül több Azure-erőforrást helyezhet üzembe, azonban egyes Azure PaaS-szolgáltatások erőforrásai nem helyezhetők üzembe virtuális hálózatban. Ennek ellenére korlátozhatja az egyes Azure PaaS-szolgáltatások erőforrásaihoz való hozzáférést, hogy csak egyetlen virtuális hálózati alhálózatról legyenek elérhetők. Ebből, hogy miként, olvassa [el a Hálózati hozzáférés korlátozása a PaaS-erőforrásokhoz](tutorial-restrict-network-access-to-resources-powershell.md).