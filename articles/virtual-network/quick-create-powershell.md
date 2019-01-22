---
title: Hozzon létre egy virtuális hálózatot – rövid útmutató – Azure PowerShell-lel
titlesuffix: Azure Virtual Network
description: Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával. Virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például virtuális gépeket, privát módon kommunikáljanak egymással, és az interneten.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: jdial
ms.openlocfilehash: ade8329e6e42fae9f3232617488a6d4a69f8ef1f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437385"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Gyors útmutató: Hozzon létre egy virtuális hálózathoz a PowerShell használatával

Virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások, például virtuális gépeket (VM), a privát módon kommunikáljanak egymással, és az interneten. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális hálózatokat. A létrehozott virtuális hálózatban két virtuális gépet fog üzembe helyezni. Ezután csatlakozhat a virtuális gépekhez az internetről, és privát módon kommunikáljanak a virtuális hálózaton keresztül.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Ha úgy dönt, hogy a helyi telepítése és használata PowerShell helyette, ehhez a rövid útmutatóhoz kell használnia az AzureRM PowerShell-modul 5.4.1-es vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Lásd: [Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) telepítési és frissítési adatai.

Végül, ha futtatja a Powershellt helyileg, is kell futtatni `Connect-AzureRmAccount`. Ez a parancs létrehoz egy kapcsolatot az Azure-ral.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Hozzon létre egy erőforráscsoportot és a egy virtuális hálózatot

Nincsenek minősített lépések végigvezetik a erőforráscsoportot és a virtuális hálózatot konfigurált.

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Mielőtt létrehozhatna egy virtuális hálózatot, akkor hozzon létre egy erőforráscsoportot a virtuális hálózat üzemeltetéséhez. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) paranccsal. Ez a példa létrehoz egy erőforráscsoportot, nevű *myResourceGroup* a a *eastus* helye:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Ez a példa létrehoz egy alapértelmezett nevű virtuális hálózatot *myVirtualNetwork* a a *EastUS* helye:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Így hozzon létre egy alhálózatot kell az Azure erőforrások egy alhálózatot egy virtuális hálózaton belül üzembe helyezi. Hozzon létre egy alhálózati konfigurációt nevű *alapértelmezett* a [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>A virtuális hálózatot az alhálózat társítása

Az alhálózati konfigurációt a virtuális hálózatba a írhat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork). Ez a parancs létrehozza az alhálózatot:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózaton.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

Az első virtuális gép létrehozása [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A következő parancs futtatásakor kéri a hitelesítő adatokat. Adjon meg egy felhasználónevet és jelszót a virtuális gép számára:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

A `-AsJob` kapcsoló a háttérben hozza létre a virtuális Gépet. A következő lépéssel folytathatja.

Amikor Azure elindul, a virtuális gép létrehozása a háttérben, fog kapni ehhez hasonló vissza:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM
```

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

A második virtuális gép létrehozása a következő paranccsal:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

Hozzon létre egy másik felhasználó és a jelszót kell. Azure virtuális gép létrehozása néhány percet vesz igénybe.

> [!IMPORTANT]
> Ne ugorjon a következő lépésre, amíg befejeződik az Azure.  Tudni fogja, ha azt a kimenetet visszaadja a PowerShell történik.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Ebben a példában a nyilvános IP-címét adja vissza a *myVm1* virtuális Géphez:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Nyisson meg egy parancssort a helyi számítógépen. Futtassa a következő parancsot: `mstsc`. Cserélje le `<publicIpAddress>` az utolsó lépés által visszaadott nyilvános IP-címét:

> [!NOTE]
> Ha futtatja, egy PowerShell-parancssort a helyi számítógépen, és ezek a parancsok az AzureRM PowerShell-modul 5.4.1-es vagy később folytathatja a felületen.

```cmd
mstsc /v:<publicIpAddress>
```

Egy Remote Desktop Protocol (*.rdp*) fájlt a számítógép letölti és a egy távoli asztali megnyílik.

1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

1. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

    > [!NOTE]
    > Előfordulhat, hogy ki kell választania **további lehetőségek** > **másik fiók használata**, a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához.

1. Kattintson az **OK** gombra.

1. A tanúsítvány figyelmeztetés jelenhet meg. Ha bejelöli, **Igen** vagy **Folytatás**.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. A távoli asztali, *myVm1*, nyissa meg a Powershellt.

1. Írja be a `ping myVm2` (igen) kifejezést.

    A biztonsági hasonlót fog kapni:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudap
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    A pingelés sikertelen, mert az Internet Control Message Protocol (ICMP) használ. Alapértelmezés szerint az ICMP nem engedélyezett, a Windows tűzfalon keresztül.

1. Engedélyezéséhez *myVm2* pingelni *myVm1* egy későbbi lépésben adja meg ezt a parancsot:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Hogy a parancs lehetővé teszi, hogy az ICMP bejövő, a Windows tűzfalon keresztül.

1. Zárja be a *myVm1* virtuális géppel létesített távoli asztali kapcsolatot.

1. Ismételje meg a lépéseket a [Csatlakozás virtuális géphez az internetről](#connect-to-a-vm-from-the-internet). Ez alkalommal csatlakozhat *myVm2*.

1. A *myVm2* virtuális gépen írja be a `ping myvm1` parancsot egy parancssorba.

    A biztonsági hasonlót fog kapni:

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

Amikor elkészült, a virtuális hálózat és a virtuális gépek, [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) , távolítsa el az erőforráscsoportot és az összes rendelkezik:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy alapértelmezett virtuális hálózatot és két virtuális gépet. Ezután csatlakozott az internetről az egyik virtuális géphez, és privát kommunikációt folytatott egy másik virtuális géppel. További információ a virtuális hálózatok beállításairól: [Virtuális hálózat kezelése](manage-virtual-network.md).

Az Azure lehetővé teszi, hogy a virtuális gépek korlátlan privát kommunikációját. Alapértelmezés szerint az Azure csak lehetővé teszi a bejövő távoli asztali kapcsolatok Windows virtuális gépekhez az internetről. Virtuális gép hálózati kommunikációt a különböző típusú konfigurálásával kapcsolatos további tudnivalókért keresse fel a [hálózati forgalom szűrése](tutorial-filter-network-traffic.md) oktatóanyag.
