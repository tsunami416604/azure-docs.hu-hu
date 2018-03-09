---
title: "Virtuális hálózat létrehozása az Azure - PowerShell |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot PowerShell használatával. A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással."
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
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 22fcdbda85f3ea336c3926e04d408935ed069c25
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Hozzon létre egy virtuális hálózatot PowerShell használatával

Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Miután létrehozta a virtuális hálózat, a két virtuális gép a köztük folyó kommunikációt magánhálózati tesztelése a virtuális hálózat telepít.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e a AzureRM PowerShell modul verziója 5.1.1-es vagy újabb. A telepített verzió megkereséséhez futtassa ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) parancsmaggal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen. Az összes Azure-erőforrások jönnek létre az Azure-beli hely (vagy területen).

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy alapértelmezett nevű virtuális hálózatot *myVirtualNetwork* a a *EastUS* helye:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

Minden virtuális hálózat rendelkezik egy vagy több címelőtagokat hozzájuk rendelve. A címterület CIDR-formátumban van megadva. A cím terület 10.0.0.0/24 10.0.0.0-10.0.0.254 magában foglalja. Virtuális hálózatok rajtuk nulla vagy több alhálózattal rendelkezik. Erőforrások telepítése egy virtuális hálózatban egy alhálózatba. 

Az alhálózat-konfiguráció létrehozása [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Minden alhálózat egy-címelőtagot, amelyen belül a virtuális hálózat címelőtagjához létezik rendelkezik. Ebben a példában egy alhálózat-konfiguráció ugyanazt a címelőtagot van, mint a virtuális hálózat címelőtagjához jön létre:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Bár az alhálózati cím előtag 10.0.0.0-10.0.0.254 foglal magában, csak a címek 10.0.0.4-10.0.0.254 érhetők el, mert Azure fenntartja az első négy címeket (0 – 3) és az utolsó cím minden alhálózatban. Mivel az alhálózati cím előtag ugyanaz, mint a virtuális hálózat címelőtagjához, csak egy alhálózatot a virtuális hálózat létezhet.

Az alhálózat beállítása írni a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely az IP-alhálózatot hoz:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="test-network-communication"></a>Tesztelje a hálózati kommunikációban

A virtuális hálózati lehetővé teszi, hogy számos különböző Azure-erőforrások közvetlenül kommunikálhatnak egymással. A virtuális hálózatba telepítése erőforrás egy típus egy virtuális gépet. Hozzon létre két virtuális gép a virtuális hálózat, hogy ellenőrizhesse a azokat egy későbbi lépésben titkos kommunikációját.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gép létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát. A helyre, amely a virtuális gép jön létre a virtuális hálózat szerepel ugyanazon a helyen kell lennie. A virtuális gép nem kell lennie, a virtuális gép ugyanabban az erőforráscsoportban, ha az ebben a cikkben. A `-AsJob` paraméter lehetővé teszi, hogy a parancs futtatása a háttérben, így a következő feladathoz is.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A következő egy példa a kimenetre hasonló kimenetet ad vissza, és Azure elindul a virtuális gép létrehozása a háttérben.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

Az Azure DHCP automatikusan rendel 10.0.0.4 a virtuális gép létrehozása, mert az első elérhető címe a *alapértelmezett* alhálózat.

Hozzon létre egy második virtuális gépet. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
A virtuális gép létrehozásához néhány percet vesz igénybe. A létrehozott virtuális géppel kapcsolatos létrehozás után, az Azure ad eredményül kimenetet. Bár a nem a visszaadott kimeneti Azure társítva *10.0.0.5* való a *myVm2* virtuális gép, mert ez volt a következő elérhető cím az alhálózat.

### <a name="connect-to-a-virtual-machine"></a>Csatlakozzon a virtuális géphez

Használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancs sikeresen lefut egy virtuális gép nyilvános IP-címét. Azure egy nyilvános, Internet irányítható IP-címet rendel alapértelmezés szerint a virtuális gépeken. A nyilvános IP-cím hozzá van rendelve a virtuális gép egy [rendelt egyes Azure-régiókban címkészletet](https://www.microsoft.com/download/details.aspx?id=41653). Azure tudja, melyik nyilvános IP-cím hozzá van rendelve egy virtuális gépet, miközben a virtuális gépen futó operációs rendszer van nincs tájékoztatási bármely nyilvános IP-cím hozzárendelve. A következő példa a nyilvános IP-címét adja vissza a *myVm1* virtuális gépet:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVm1* virtuális gépet a helyi számítógépen. Cserélje le `<publicIpAddress>` az előző parancs által visszaadott IP-címét.

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozott, a számítógép letölti és megnyitni. Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó, és kattintson **OK**. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

### <a name="validate-communication"></a>Kommunikációs ellenőrzése

Kísérlet történt Pingelje meg egy Windows virtuális gép leáll, mert ping keresztül nem engedélyezett a Windows tűzfal alapértelmezés szerint. Engedélyezi a ping *myVm1*, adja meg a következő parancsot a parancssorba:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Kommunikáció a érvényesítéséhez *myVm2*, adja meg a következő parancsot a parancssorba a a *myVm1* virtuális gépet. A virtuális gép létrehozásakor használt hitelesítő adatait, és fejezze be a kapcsolatot:

```
mstsc /v:myVm2
```

A távoli asztali kapcsolat nem sikeres, mert mindkét virtuális gép van rendelve a magánhálózati IP-címek a *alapértelmezett* alhálózat és a távoli asztal meg nyitva, alapértelmezés szerint a Windows tűzfalon keresztül. Biztosan csatlakozni tudjanak *myVm2* állomásnév szerint, mert az Azure DNS-névfeloldás automatikusan biztosít a virtuális hálózaton belül minden gazdagép. Egy parancs parancssori futtatásával pingelni a *myVm1*, a *myVm2*.

```
ping myvm1
```

A ping parancs sikeres, mert akkor engedélyezett, a Windows tűzfalon keresztül a *myVm1* az előző lépésben a virtuális gép. Erősítse meg a kimenő kommunikációt az interneten, adja meg a következő parancsot:

```
ping bing.com
```

Négy választ kap bing.com. Alapértelmezés szerint a virtuális gépek virtuális hálózatban képes kommunikálni a kimenő internetkapcsolat.

A távoli asztali munkamenet bezárásához. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, használhatja a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) parancs beírásával távolítsa el az erőforráscsoport és az összes olyan erőforrást tartalmaz:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe egy alapértelmezett virtuális hálózatot egyetlen alhálózattal. Annak megismerése, hogyan hozhat létre egyéni virtuális hálózatot, több alhálózattal, továbbra is az oktatóanyag egy egyéni virtuális hálózat létrehozásához.

> [!div class="nextstepaction"]
> [Egyéni virtuális hálózat létrehozása](virtual-networks-create-vnet-arm-ps.md)
