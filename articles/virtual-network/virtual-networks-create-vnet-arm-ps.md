---
title: Hozzon létre egy Azure virtuális hálózatra, több alhálózattal - PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre virtuális hálózatot, több alhálózattal PowerShell használatával.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880562"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>Virtuális hálózat létrehozása a PowerShell használatával több alhálózattal

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások való kommunikációhoz az internetes és közvetlenül egymás mellett. Több alhálózattal a virtuális hálózat létrehozása lehetővé teszi, hogy a hálózati szegmenseket, így szűrheti vagy -vezérlőket a alhálózatok közötti forgalmat. A cikkben megismerheti, hogyan:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * Alhálózat létrehozása
> * Virtuális gépek közötti hálózati kommunikációhoz tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell telepítéséhez és használatához helyileg választja, az oktatóanyag az Azure PowerShell 3,6 vagy újabb verziója szükséges. Futtatás ` Get-Module -ListAvailable AzureRM` telepített verziója található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *EastUS* helyét.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

A **címelőtagja** CIDR-formátumban van megadva. A megadott címelőtag az IP-címek 10.0.0.0-10.0.255.254 tartalmazza.

## <a name="create-a-subnet"></a>Alhálózat létrehozása

Egy alhálózat először létrehoz egy alhálózati konfigurációt, majd frissítse a virtuális hálózati alhálózat konfigurációjával hozta létre. Az alhálózat-konfiguráció létrehozása [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Az alábbi példa létrehoz két alhálózat konfigurációi *nyilvános* és *titkos* alhálózatok:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

A **címelőtagja** megadott alhálózatot a virtuális hálózathoz definiált címelőtag belül kell lennie. Az Azure DHCP az alhálózati cím előtag IP-címet rendel egy alhálózaton üzembe helyezett erőforrás. Azure csak rendel a címek 10.0.0.4-10.0.0.254 belül üzembe helyezett erőforrás a **nyilvános** alhálózatot, mert Azure fenntartja az első négy címeket (10.0.0.0-10.0.0.3 az alhálózat ebben a példában) és a legutóbbi címek () az alhálózat ebben a példában 10.0.0.255-ig) minden alhálózatban.

Az alhálózati beállítások írni a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely az alhálózatok hoz létre a virtuális hálózat:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Az Azure virtuális hálózatok és alhálózatok üzemi használatra való telepítése előtt javasoljuk, hogy alaposan megismerje a címterület [szempontok](manage-virtual-network.md#create-a-virtual-network) és [virtuális hálózati korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Amennyiben az erőforrások telepítése alhálózatokra, virtuális hálózati és alhálózati módosításokat, például megváltoztatni a címtartományt, alhálózatok belül rendszerbe meglévő Azure-erőforrások újratelepítés lehet szükség.

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások való kommunikációhoz az internetes és közvetlenül egymás mellett. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet. Hozzon létre két virtuális gép a virtuális hálózat, amellyel tesztelheti őket, és az Internet közötti hálózati kommunikációhoz egy későbbi lépésben. 

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gép létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példa létrehoz egy virtuális gépet, nevű *myVmWeb* a a *nyilvános* alhálózata a *myVirtualNetwork* virtuális hálózat. A `-AsJob` beállítás hoz létre a virtuális gép a háttérben, így továbbra is a következő lépéssel. Amikor a rendszer kéri, adja meg a felhasználónevet, és jelentkezzen be a virtuális gép kívánt jelszót.

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

Azure automatikusan 10.0.0.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.0.0.4 az első elérhető IP-cím a *nyilvános* alhálózat. 

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

A virtuális gép létrehozásához néhány percet vesz igénybe. Bár a nem a visszaadott kimenetben Azure 10.0.1.4 beléptetésiprofil-oszlopként hozzárendelve a magánhálózati IP-cím a virtuális gép, mert 10.0.1.4 az első elérhető IP-cím a *titkos* alhálózata *myVirtualNetwork*. 

Ne folytassa további lépéseket addig, amíg létrejön a virtuális gép és a PowerShell-kimenet visszaadása.

Ez a cikk a létrehozott virtuális gépek rendelkezik ilyennel [hálózati illesztő](virtual-network-network-interface.md) dinamikusan hozzárendelt hálózati illesztő egy IP-címmel. A virtuális gép telepítése után is [több nyilvános és magánhálózati IP-címek hozzáadása vagy módosítása a IP-cím hozzárendelés metódus statikus](virtual-network-network-interface-addresses.md#add-ip-addresses). Is [hozzáadása a hálózati adapterek](virtual-network-network-interface-vm.md#vm-add-nic), akár által támogatott maximális a [Virtuálisgép-méretet](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) válassza, ha egy virtuális gépet hoz létre. Emellett [egygyökerű i/o-virtualizálás (SR-IOV) engedélyezése](create-vm-accelerated-networking-powershell.md) a virtuális gép, de csak akkor, ha egy virtuális gép létrehozásához, amely a funkció támogatja a Virtuálisgép-méretet.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>A virtuális gépek között, és az internetes kommunikáció

Csatlakozhat a virtuális gép nyilvános IP-cím, az internetről. Azure létrehozásakor a *myVmMgmt* virtuális gép, egy nyilvános IP-cím nevű *myVmMgmt* is létrejött, de a virtuális géphez rendelt. Bár a virtuális gép nem kell egy nyilvános IP-címet kap, Azure rendel hozzá egy nyilvános IP-cím minden virtuális gépet hoz létre, alapértelmezés szerint. Útján kommunikálnak az interneten a virtuális gép, egy nyilvános IP-címet kell rendelni a virtuális géphez. Minden virtuális gépek kommunikálhatnak az internettel kimenő, függetlenül attól, egy nyilvános IP-címet a virtuális géphez van rendelve. Az Azure-ban kimenő internetes kapcsolatok kapcsolatos további információkért lásd: [az Azure-ban kimenő kapcsolatok](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza egy virtuális gép nyilvános IP-címét. A következő példa a nyilvános IP-címét adja vissza a *myVmMgmt* virtuális gépet:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVmMgmt* virtuális gépet a helyi számítógépen. Cserélje le `<publicIpAddress>` az előző parancs által visszaadott IP-címét.

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozott, a számítógép letölti és megnyitni. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, ha a megadott hitelesítő adatok megadását, a virtuális gép létrehozása), és kattintson a **OK**. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra. 

Egy későbbi lépésben ping folytatott kommunikációhoz használandó a *myVmMgmt* virtuális gépek létrehozását a *myVmWeb* virtuális gépet. Ping az ICMP, amely alapértelmezés szerint a Windows tűzfalon keresztül megtagadva. A Windows tűzfalon keresztül ICMP engedélyezéséhez az alábbi parancs beírásával a parancssorba:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Bár a ping használatban van ebben a cikkben, lehetővé téve az ICMP az üzemi környezetek a Windows tűzfalon keresztül nem ajánlott.

Biztonsági okokból általában távolról csatlakozhat a virtuális hálózat virtuális gépek számának korlátozása. Ebben az oktatóanyagban a *myVmMgmt* történő felügyeletére szolgál a virtuális gép a *myVmWeb* virtuális gép virtuális hálózatban. Használja a következő parancsot a távoli asztal a *myVmWeb* virtuális gépek létrehozását a *myVmMgmt* virtuális gépet:

``` 
mstsc /v:myVmWeb
```

Való kommunikációra a *myVmMgmt* virtuális gépek létrehozását a *myVmWeb* virtuális gépet, adja meg a következő parancsot a parancssorba:

```
ping myvmmgmt
```

A következő egy példa a kimenetre hasonló kimenetet jelenhet meg:
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
Láthatja, hogy a cím a *myVmMgmt* virtuális gép 10.0.1.4. 10.0.1.4 volt az első elérhető IP-címek tartománya, a *titkos* alhálózati központilag telepített a *myVmMgmt* az előző lépésben a virtuális gép.  Azt látja, hogy a virtuális gép teljesen minősített tartománynevét *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Bár a *dar5p44cif3ulfq00wxznl3i3f* a tartománynév része nem egyezik a virtuális gép, a tartomány nevét az többi részének azonosak. Alapértelmezés szerint az összes Azure virtuális gép használja az alapértelmezett Azure DNS szolgáltatást. Az összes virtuális gép a virtuális hálózaton belül az azonos virtuális hálózatban, Azure alapértelmezett DNS-szolgáltatás segítségével más virtuális gépek képes névfeloldásra. Azure alapértelmezett DNS-szolgáltatás helyett használhatja a saját DNS-kiszolgáló vagy a saját szolgáltatása az Azure DNS-szolgáltatás. További információkért lásd: [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) vagy [titkos tartományok Azure DNS használatával](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Internet Information Services (IIS) telepítése a Windows Server a a *myVmWeb* virtuális gépet, adja meg a következő parancsot egy PowerShell-munkamenetben:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

A telepítés végeztével az IIS, válassza le a *myVmWeb* a távoli asztali munkamenetgazda, így a a *myVmMgmt* távoli asztali munkamenetet. Nyisson meg egy webböngészőt, és keresse meg a http://myvmweb. Az IIS üdvözlőlap láthatja.

Válassza le a *myVmMgmt* távoli asztali munkamenetet.

Az Azure rendelt nyilvános cím beszerzése a *myVmWeb* virtuális gépet:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

A saját számítógépen keresse meg a nyilvános IP-címét a *myVmWeb* virtuális gépet. A saját számítógépéről IIS-üdvözli a lapnak a megtekintésére irányuló kísérlet sikertelen lesz. A kísérlet meghiúsul, mert a virtuális gépek üzembe helyezése, amikor Azure alapértelmezés szerint minden egyes virtuális gép hálózati biztonsági csoport létrehozása. 

Hálózati biztonsági csoport biztonsági szabályokat, amelyek engedélyezik vagy megtagadják a bejövő és kimenő hálózati forgalom port és az IP-címet tartalmazza. Az alapértelmezett hálózati biztonsági csoport létrehozása Azure lehetővé teszi a kommunikációt az azonos virtuális hálózatban lévő erőforrások közötti összes portokon keresztül. Windows virtuális gépek esetén az alapértelmezett hálózati biztonsági csoport minden bejövő forgalom megtagadja az interneten összes portokon keresztül, fogadja el a TCP-port 3389-es (RDP). Ennek eredményeképpen alapértelmezés szerint is RDP közvetlenül a *myVmWeb* virtuális gép az internetről, annak ellenére, hogy nem érdemes port 3389 nyissa meg a webkiszolgálón. Webböngészés 80-as porton keresztül kommunikál, mivel kommunikációs az internetről sikertelen, mert nincs szabály az alapértelmezett hálózati biztonsági csoport átengedi a forgalmat a 80-as porton keresztül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) használatával távolítsa el az erőforráscsoport és a benne található erőforrásokat.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudta, hogyan telepíthet egy virtuális hálózatot, több alhálózattal. Is megtanulta, hogy egy Windows rendszerű virtuális gép létrehozásakor az Azure létrehoz egy hálózati adapteren, hogy azt a virtuális gép csatlakozik, és létrehoz egy hálózati biztonsági csoportot, amely csak forgalom 3389-es porton keresztül az internetről. A következő oktatóanyag áttekintésével megismerheti, hogyan alhálózatokra, és nem az egyes virtuális gépek hálózati forgalom szűrésére továbblépés.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrésére alhálózathoz](./virtual-networks-create-nsg-arm-ps.md)
