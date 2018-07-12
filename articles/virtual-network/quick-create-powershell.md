---
title: Virtuális hálózat létrehozása – rövid útmutató – Azure PowerShell | Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek számára, hogy privát módon kommunikáljanak egymással és az internettel.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: b8b67b235b54fb5bde738ed5cc1605e08d182a69
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688085"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Gyors útmutató: Virtuális hálózat létrehozása a PowerShell használatával

A virtuális hálózatok lehetővé teszik az Azure-erőforrások, például a virtuális gépek (VM-ek) számára, hogy privát módon kommunikáljanak egymással és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután az internetről csatlakozni fog az egyik virtuális géphez, és privát kommunikációt kezdeményez a két virtuális gép között.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az útmutatóhoz az AzureRM PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot, amelynek a virtuális hálózat tagja lesz. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy *myVirtualNetwork* nevű alapértelmezett virtuális hálózatot az *USA keleti régiója* helyen.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Az Azure-erőforrások a virtuális hálózatok egy alhálózatán vannak üzembe helyezve, ezért létre kell hoznia egy alhálózatot. A [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancs használatával hozzon létre egy alhálózati konfigurációt. 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Írja az alhálózati konfigurációt a virtuális hálózatba a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) paranccsal, amely létrehozza az alhálózatot a virtuális hálózaton belül:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. A következő parancs futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Az alábbi példában található kimenethez hasonlót fog visszakapni, és az Azure elkezdi a virtuális gép létrehozását a háttérben.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása 

Írja be a következő parancsot:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne ugorjon a következő lépésre, amíg az előző parancs végrehajtása be nem fejeződik és a rendszer vissza nem adja a kimenetet a PowerShellnek.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Az alábbi példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot: 

```
mstsc /v:<publicIpAddress>
```

A rendszer létrehoz és letölt a számítógépre egy Remote Desktop Protocol (.rdp) fájlt. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. Kattintson az **OK** gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

A *myVm1* virtuális gépen adja meg a `ping myvm2` parancsot a PowerShellben. A pingelés sikertelen lesz, mert a pingelés az ICMP-t használja, és azt a Windows tűzfal alapértelmezés szerint nem engedi át.

Ahhoz, hogy a *myVm2* virtuális gép pingelhesse a *myVm1* virtuális gépet egy későbbi lépésben, írja be az alábbi parancsot PowerShellbe. Ez átengedi a beérkező ICMP-t a Windows tűzfalon:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot. 

Hajtsa ismét végre a [Kapcsolódás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet) című részben leírt lépéseket, de most csatlakozzon a *myVm2* virtuális géphez. 

A *myVm2* virtuális gépen írja be a `ping myvm1` parancsot egy parancssorba.

Mivel az előző lépésben átengedte az ICMP-t a Windows tűzfalon a *myVm1* virtuális gép esetében, válaszokat fog kapni a *myVm1* virtuális géptől.

Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Ezután csatlakozott az internetről az egyik virtuális géphez, és privát kommunikációt folytatott egy másik virtuális géppel. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md). 

Az Azure alapértelmezés szerint engedélyezi a virtuális gépek korlátlan privát kommunikációját, de az internetről bejövő távoli asztali kapcsolatokat csak a Windows rendszerű virtuális gépeken engedélyezi. Ha többet szeretne megtudni arról, hogyan lehet engedélyezni vagy korlátozni a virtuális gépek kimenő vagy bejövő hálózati kommunikációjának különböző típusait, lépjen a [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md) című oktatóanyagra.
