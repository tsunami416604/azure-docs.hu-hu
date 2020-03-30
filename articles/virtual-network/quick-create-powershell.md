---
title: Virtuális hálózat létrehozása - rövid útmutató - Azure PowerShell
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. A virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek, kommunikáljanak egymással és az internettel.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 1d30b35264707c59c899cc3a224e4affa2a4696e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241443"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Gyors útmutató: Virtuális hálózat létrehozása a PowerShell használatával

A virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek), kommunikáljanak egymással és az internettel. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozik a virtuális gépekhez az internetről, és privát módon kommunikálanak a virtuális hálózaton keresztül.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy inkább helyileg telepíti és használja a PowerShellt, ez a rövid útmutató az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját kell használnia. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. A telepítési és frissítési információkaz [Azure PowerShell-modul telepítése](/powershell/azure/install-az-ps) című témakörben talál.

Végül, ha helyileg futtatja a PowerShellt, futtatnia is `Connect-AzAccount`kell. Ez a parancs kapcsolatot hoz létre az Azure-ral.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Erőforráscsoport és virtuális hálózat létrehozása

Van egy maroknyi lépés, amelyen végig kell mennie az erőforráscsoport és a virtuális hálózat konfigurálásához.

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)segítségével. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Ez a példa létrehoz egy alapértelmezett virtuális hálózatnevű *myVirtualNetwork* az *EastUS* helyen:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Az Azure erőforrásokat telepít egy virtuális hálózaton belüli alhálózatba, ezért létre kell hoznia egy alhálózatot. Hozzon létre egy *alapértelmezettnek* nevezett alhálózati konfigurációt [az Add-AzVirtualSubnetConfig segítségével:](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Az alhálózat társítása a virtuális hálózathoz

Az alhálózati konfigurációt a [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)segítségével írhatja a virtuális hálózatba. Ez a parancs létrehozza az alhálózatot:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Hozza létre az első virtuális gép [new-AzVM.](/powershell/module/az.compute/new-azvm) A következő parancs futtatásakor a rendszer hitelesítő adatokat kér. Adja meg a virtuális gép felhasználónevét és jelszavát:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A `-AsJob` beállítás létrehozza a virtuális gép a háttérben. Folytathatja a következő lépéssel.

Amikor az Azure a háttérben elkezdi létrehozni a virtuális gép létrehozását, a következőt fogja visszakapni:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

A második virtuális gép létrehozása ezzel a paranccsal:

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Létre kell hoznia egy másik felhasználót és jelszót. Az Azure-t néhány perc alatt létre kell hoznia a virtuális gép.

> [!IMPORTANT]
> Ne folytassa a következő lépéssel, amíg az Azure be nem fejeződik.  Tudni fogja, hogy kész, amikor visszaadja a kimenetet a PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

A [Get-AzPublicIpAddress használatával](/powershell/module/az.network/get-azpublicipaddress) adja vissza a virtuális gép nyilvános IP-címét. Ez a példa a *myVm1* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Nyisson meg egy parancssort a helyi számítógépen. Futtassa a következő parancsot: `mstsc`. Cserélje `<publicIpAddress>` le az utolsó lépésből visszaadott nyilvános IP-címet:

> [!NOTE]
> Ha ezeket a parancsokat egy PowerShell-parancssorból futtatta a helyi számítógépen, és az Az PowerShell-modul 1.0-s vagy újabb verzióját használja, folytathatja az adott felületen.

```cmd
mstsc /v:<publicIpAddress>
```
1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

1. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

    > [!NOTE]
    > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. Előfordulhat, hogy figyelmeztetést kap. Ha így tesz, válassza az **Igen** vagy **a Folytatás**lehetőséget.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A *myVm1*távoli asztalán nyissa meg a PowerShellt.

1. Írja be a `ping myVm2` (igen) kifejezést.

    Majd kap valami ilyesmi vissza:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A ping elromlik, mert az INTERNET Control Message Protocol (ICMP) protokollt használja. Alapértelmezés szerint az ICMP nem engedélyezett a Windows tűzfalon keresztül.

1. Ha azt szeretné, hogy *a myVm2* egy későbbi lépésben pingelhesse a *myVm1-et,* írja be ezt a parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Ez a parancs lehetővé teszi, hogy az ICMP a Windows tűzfalon keresztül befelé hathat.

1. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

1. Ismételje meg a lépéseket a [Csatlakozás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet). Ezúttal csatlakozzon a *myVm2-hez.*

1. A *myVm2* virtuális gépen írja be a `ping myvm1` parancsot egy parancssorba.

    Majd kap valami ilyesmi vissza:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Mivel az előző lépésben átengedte az ICMP-t a Windows tűzfalon a *myVm1* virtuális gép esetében, válaszokat fog kapni a *myVm1* virtuális géptől.

1. Zárja be a *myVm2* virtuális géppel létesített távoli asztali kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózattal és a virtuális gépekkel, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével távolítsa el az erőforráscsoportot és az összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Ezután csatlakozott az internetről az egyik virtuális géphez, és privát kommunikációt folytatott egy másik virtuális géppel. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Az Azure lehetővé teszi a virtuális gépek közötti korlátlan privát kommunikációt. Alapértelmezés szerint az Azure csak az internetről engedélyezi a Windows virtuális gépekhez érkező távoli asztali kapcsolatokat. Ha többet szeretne megtudni a különböző típusú virtuális gép hálózati kommunikáció konfigurálásáról, látogasson el a [Hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyag.
