---
title: "Hálózati forgalmat - PowerShell |} Microsoft Docs"
description: "Útmutató a hálózati forgalom irányításához egy PowerShell-lel útválasztási táblázathoz."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Hálózati forgalom egy útválasztási táblázathoz PowerShell használatával

Az Azure automatikusan útvonalak forgalom alapértelmezés szerint egy virtuális hálózatban található alhálózatok között. A saját Azure felülbírálására útvonalak létrehozása alapértelmezett útválasztási. Képes létrehozni az egyéni útvonalak akkor hasznos, ha például azt szeretné, a tűzfalon keresztül alhálózatok közötti forgalom irányítására. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Hozzon létre egy útválasztási táblázatot
> * Útvonal létrehozása
> * Társítsa a virtuális hálózati alhálózat egy útválasztási táblázatot
> * Útválasztás tesztelése
> * Útválasztás hibaelhárítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` telepített verziója található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-route-table"></a>Hozzon létre egy útválasztási táblázatot

Az Azure útvonalak forgalom virtuális hálózatban, alapértelmezés szerint minden alhálózatok között. Azure alapértelmezett útvonalak kapcsolatos további információkért lásd: [rendszerútvonalak](virtual-networks-udr-overview.md). Azure alapértelmezett útválasztási felülbírálását, létrehoz egy útválasztási táblázatot, amely tartalmazza az útvonalak, a virtuális hálózati alhálózat útválasztási táblázatot.

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

Egy útválasztási táblázatot nulla vagy több útvonalat tartalmazza. Hozzon létre egy útvonalat útvonal tábla objektum lekérésével [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), hozzon létre egy útvonalat [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), majd az útválasztó-konfiguráció írni az útvonaltáblában a [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

Az útvonal átirányítja egy hálózati 10.0.2.4 IP-címmel rendelkező virtuális készüléken keresztül 10.0.1.0/24 címelőtag irányuló teljes forgalomra. A hálózati virtuális készüléket és alhálózat a megadott címelőtag a későbbi lépésekben jönnek létre. Az útvonal Azure alapértelmezett útválasztási, amely irányítja a forgalmat közvetlen alhálózatba felülbírálja. Minden útvonal a következő ugrás típusa határozza meg. A következő ugrás típusa arra utasítja a Azure hogyan kell a forgalmat. Ebben a példában a következő ugrás típusa van *VirtualAppliance*. Azure-ban, és mikor érdemes használni azokat az összes elérhető következő ugrás típusok kapcsolatos további információkért lásd: [a következő ugrás típusa](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Társítson egy útválasztási táblázatot az alhálózathoz

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

A címelőtagokat a címelőtag a virtuális hálózathoz definiált tartományba kell esnie. Az alhálózati cím előtagokat nem lehetnek átfedésben egymással.

Az alhálózati beállítások írni a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely az alhálózatok hoz létre a virtuális hálózat:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

A nulla vagy több alhálózatból egy útválasztási táblázatot lehet társítani. Egy alhálózat állhat nulla vagy egy útválasztási táblázatot társítva. Kimenő forgalom alhálózatból származó Azure alapértelmezett útvonalakat, és minden alhálózathoz társít egy útvonaltábla hozzáadott egyéni útvonalak alapján történik. Társítsa a *myRouteTablePublic* útválasztási táblázatot, hogy a *nyilvános* alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) majd írja be az alhálózati konfigurációját, és a a virtuális hálózati [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Az útvonaltáblák üzemi használatra való telepítése előtt javasoljuk, hogy alaposan feltérképezése [az Azure útválasztási](virtual-networks-udr-overview.md) és [Azure korlátozását](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Útválasztás tesztelése

Útválasztás teszteléséhez beállításához hozzon létre virtuális gép számára, hogy az előző lépésben létrehozott útvonal keresztül továbbítja a hálózati virtuális készülék szolgál. Miután létrehozta a virtuális hálózati készüléknek, a virtuális gép telepítésekor a *nyilvános* és *titkos* alhálózatokat. Akkor lesz majd útvonal-forgalom a *nyilvános* alhálózatában ahhoz, hogy a *titkos* keresztül a virtuális hálózati berendezések.

### <a name="create-a-network-virtual-appliance"></a>A hálózati virtuális készülék létrehozása

Egy olyan virtuális géphez egy hálózati alkalmazás gyakran nevezik egy virtuális hálózati berendezések. Hálózati virtuális készülékek általában fogadni hálózati forgalmat, néhány műveletet végre, majd előre, vagy a hálózati alkalmazások konfigurált logikán alapuló hálózati adatforgalmat. 

#### <a name="create-a-network-interface"></a>A hálózati illesztő létrehozása

Az előző lépésben létrehozott egy olyan útvonalat, amely a hálózati virtuális készülék szerepel a következő ugrás típusa. Egy olyan virtuális géphez egy hálózati alkalmazás gyakran nevezik egy virtuális hálózati berendezések. Éles környezetben telepít a hálózati virtuális készülék legtöbbször egy előre konfigurált virtuális gépet. Számos hálózati virtuális készülékek érhetők el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Ez a cikk egy alapszintű a virtuális gép létrehozása.

A virtuális gépnek egy vagy több hálózati illesztőre csatlakozik, amelyek lehetővé teszik a virtuális gép és a hálózat kommunikációját. A hálózati adaptert továbbküldhetik hálózati forgalmat küldeni, nem a saját IP-cím, IP-továbbítás engedélyezni kell a hálózati adapter. Mielőtt létrehozna egy adott hálózati csatoló, kell beolvasni a virtuális hálózati azonosítót a [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), majd az alhálózati azonosító a [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Hozzon létre egy hálózati illesztő – [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) a a *DMZ* alhálózat IP-továbbítás engedélyezve:

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

#### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet, és a meglévő hálózati illesztő csatlakoztatni, létre kell hoznia a virtuálisgép-konfiguráció [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). A konfiguráció tartalmazza a hálózati illesztő az előző lépésben létrehozott. A felhasználónév és jelszó megadását kéri, válassza ki a felhasználónevet és jelentkezzen be a virtuális gép kívánt jelszót. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
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

A virtuálisgép-konfigurációnak a virtuális gép létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példa létrehoz egy virtuális gépet, nevű *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A `-AsJob` beállítás hoz létre a virtuális gép a háttérben, így továbbra is a következő lépéssel. Amikor a rendszer kéri, adja meg a felhasználónevet, és jelentkezzen be a virtuális gép kívánt jelszót. Éles környezetben telepít a hálózati virtuális készülék legtöbbször egy előre konfigurált virtuális gépet. Számos hálózati virtuális készülékek érhetők el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Azure 10.0.2.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.0.2.4 az első elérhető IP-cím a *DMZ* alhálózata *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Két virtuális gép létrehozása virtuális hálózatban, így ellenőrizheti, hogy a forgalom a *nyilvános* alhálózati annak biztosítására, hogy a *titkos* alhálózatot a hálózati virtuális készülék egy későbbi lépésben keresztül. 

A virtuális gép létrehozása a *nyilvános* alhálózat [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példa létrehoz egy virtuális gépet, nevű *myVmWeb* a a *nyilvános* alhálózata a *myVirtualNetwork* virtuális hálózat. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure 10.0.0.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.0.1.4 az első elérhető IP-cím a *nyilvános* alhálózata *myVirtualNetwork*.

A virtuális gép létrehozása a *titkos* alhálózat.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

A virtuális gép létrehozásához néhány percet vesz igénybe. Azure 10.0.1.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.0.1.4 az első elérhető IP-cím a *titkos* alhálózata *myVirtualNetwork*. 

A következő lépéssel nem folytatható, amíg a virtuális gép létrehozása Azure PowerShell kimeneti visszaadja.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>A hálózati virtuális készülék-útvonal forgalmát

Használjon [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) ICMP engedélyezése a bejövő a *myVmWeb* és *myVmMgmt* virtuális gépek tracert használatával történő tesztelése a Windows tűzfalon keresztül egy későbbi lépésben a virtuális gépek közötti kommunikáció:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Az előző parancsot is igénybe vehet néhány percet. Ne hajtsa végre a következő lépés a parancsok végezze el, amíg a kimenetet visszaadja PowerShell. Bár tracert ebben a cikkben útválasztási tesztelésére szolgál, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

Az internetről kapcsolódnak a virtuális gép nyilvános IP-cím. Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza egy virtuális gép nyilvános IP-címét. A következő példa a nyilvános IP-címét adja vissza a *myVmMgmt* virtuális gépet:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVmMgmt* virtuális gépet a helyi számítógépen. Cserélje le `<publicIpAddress>` az előző parancs által visszaadott IP-címét.

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozott, a számítógép letölti és megnyitni. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, ha a megadott hitelesítő adatok megadását, a virtuális gép létrehozása), majd válassza ki **OK**. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra. 

A virtuális gép hálózati adapter az Azure-ban az IP-továbbítást engedélyezte [engedélyezése IP fowarding](#enable-ip-forwarding). A virtuális gépekről az operációs rendszer és a virtuális gépen futó alkalmazások is kell tudni továbbítja a hálózati forgalmat. A hálózati virtuális készülék éles környezetben üzembe helyezésekor a készülék általában szűrők, naplózza, vagy forgalmat előtt néhány egyéb funkciót hajt végre. Ez a cikk azonban az operációs rendszer egyszerűen továbbítja kap az összes forgalom. Az operációs rendszerben az IP-továbbítás engedélyezése a *myVmNva*:

A parancssorból a *myVmMgmt* virtuális gépet, a távoli asztal a *myVmNva* virtuális gépet:

``` 
mstsc /v:myVmNva
```
    
Az operációs rendszerben az IP-továbbítás engedélyezése a *myVmNva* virtuális gépet, a következő parancsot a PowerShell írja be a *myVmNva* virtuális gép:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Indítsa újra a *myVmNva* virtuális gépet, mely is leválasztja a távoli asztali munkamenetgazda, így a távoli asztali munkamenetben indította el a *myVmMgmt* virtuális gépet.

Miután a *myVmNva* virtuális gép újraindul, ellenőrizze a hálózati forgalmat és az Útválasztás a következő parancs segítségével a *myVmWeb* virtuális gépek létrehozását a *myVmMgmt* virtuális gép.

```
tracert myvmweb
```

A rendszer a választ az alábbi példához hasonló:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Láthatja, hogy továbbítódik közvetlenül a *myVmMgmt* a virtuális gépet a *myVmWeb* virtuális gépet. Azure alapértelmezett útvonalak, közvetlen alhálózatba irányíthatja a forgalmat.

Használja a következő parancsot a távoli asztal a *myVmWeb* virtuális gépek létrehozását a *myVmMgmt* virtuális gépet:

``` 
mstsc /v:myVmWeb
```

A hálózati forgalom útválasztási tesztelése a *myVmMgmt* a virtuális gép a *myVmWeb* virtuális gépet, adja meg a következő parancsot a parancssorba:

```
tracert myvmmgmt
```

A rendszer a választ az alábbi példához hasonló:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Láthatja, hogy az első ugrásnál 10.0.2.4, amely a hálózati virtuális készülék magánhálózati IP-cím-e. A kétugrásos 10.0.1.4, a magánhálózati IP-címe a *myVmMgmt* virtuális gépet. Az útvonal hozzá a *myRouteTablePublic* útvonaltábla és társított a *nyilvános* alhálózati, hogy a forgalmat az NVA keresztül, és nem közvetlenül az Azure a *saját* alhálózat.

Zárja be a távoli asztali munkamenetekhez, mind a *myVmWeb* és *myVmMgmt* virtuális gépek.

## <a name="troubleshoot-routing"></a>Útválasztás hibaelhárítása

A korábbi lépésekben megismert Azure érvényes alapértelmezett útvonalakat, szükség esetén felülírható saját útvonalak. Egyes esetekben forgalom irányítása nem feltétlenül, nincs rá szükség. Használjon [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) engedélyezéséhez egy hálózati figyelőt EastUS régióban, ha még nem rendelkezik egy hálózati figyelőt az adott régióban:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Használjon [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) annak meghatározásához, hogy hogyan továbbítódik két virtuális gépek között. Például a következő parancsot a forgalom útválasztásához megvizsgálja-e a *myVmWeb* (10.0.0.4) a virtuális gépet a *myVmMgmt* (10.0.1.4) virtuális gépet:

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
A következő kimenetet visszaadja a rövid várakozás után:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

A kimeneti arról tájékoztatja, hogy a következőugrás-forgalmat az IP-cím *myVmWeb* való *myVmMgmt* 10.0.2.4 van (a *myVmNva* virtuális gép), ,hogykövetkezőugrás-típusértéke *VirtualAppliance*, és hogy van-e az útvonaltáblát az útválasztási okozó *myRouteTablePublic*.

A hatékony útvonalak mindegyik hálózati interfész olyan Azure alapértelmezett útvonalak és bármely útvonalakat adhat meg. Az összes útvonal hatékony, a hálózati illesztő a virtuális gép megjelenítéséhez [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Ahhoz például, hogy a hatékony útvonalait megjelenítése a *myVmWeb* a kapcsolat a *myVmWeb* virtuális gépet, adja meg a következő parancsot:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Vissza az összes alapértelmezett útvonalak és az előző lépésben felvett útvonal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy útválasztási táblázatot, és az alhálózathoz társított. A hálózati virtuális készülék titkos alhálózathoz nyilvános alhálózatból származó forgalmat portleképezéseit létrehozott. A virtuális hálózaton belül számos Azure-erőforrások telepítése során egyes Azure PaaS szolgáltatások erőforrás nem telepíthető virtuális hálózatba. Továbbra is korlátozzuk néhány Azure PaaS-szolgáltatást csak a virtuális hálózati alhálózat forgalom erőforrásaihoz, ha. Előzetes áttekintésével megismerheti, hogyan Azure PaaS erőforrásokhoz való hálózati hozzáférés korlátozása a következő cikket.

> [!div class="nextstepaction"]
> [Hálózati hozzáférés korlátozása PaaS erőforrások](virtual-network-service-endpoints-configure.md#azure-powershell)