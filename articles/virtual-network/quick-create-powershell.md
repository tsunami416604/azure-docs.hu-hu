---
title: "Hozzon létre egy Azure virtuális hálózatra - PowerShell |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy virtuális hálózatot PowerShell használatával. A virtuális hálózati lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépeket, közvetlenül kommunikálnak egymással, és az internetes."
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
ms.date: 03/09/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 13d36e6861a30473e6cb5d54d94a3c23a1e4cc59
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>Hozzon létre egy virtuális hálózatot PowerShell használatával

Virtuális hálózat közvetlenül kommunikálnak egymással, és az internetes Azure-erőforrások, például a virtuális gépek (VM), lehetővé. Ebből a cikkből megismerheti, hogyan hozhat létre virtuális hálózatot. Virtuális hálózat létrehozása után a két virtuális gépeket létrehozni a virtuális hálózatban telepít. Ezután csatlakoztassa az internetről egy virtuális Gépet, és közvetlenül a Microsoftnak a két virtuális gépek közötti kommunikációra.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, a cikkben a AzureRM PowerShell modul verziója 5.4.1 szükséges vagy újabb. A telepített verzió megkereséséhez futtassa ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózat tartalmaz az. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy alapértelmezett nevű virtuális hálózatot *myVirtualNetwork* a a *EastUS* helye:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Azure-erőforrások egy alhálózathoz, a virtuális hálózaton belül vannak telepítve, ezért egy alhálózat létrehozásához szükséges. Az alhálózat-konfiguráció létrehozása [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Az alhálózat beállítása írni a virtuális hálózat [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), amely hoz létre az alhálózatot a virtuális hálózaton belül:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépek a virtuális hálózat:

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

A virtuális gép létrehozása [új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A következő parancs futtatásakor a rendszer kéri a hitelesítő adatokat. A megadott értékek a virtuális Gépet, a felhasználónév és jelszó van állítva. A `-AsJob` beállítás hoz létre a virtuális gép a háttérben, hogy továbbra is a következő lépéssel.

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

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása 

Írja be a következő parancsot:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

A virtuális gép létrehozásához néhány percet vesz igénybe. Ne hajtsa végre a következő lépéssel mindaddig, amíg az előző parancs végrehajtása során, és a kimenetet visszaadja PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Csatlakoztassa a virtuális Gépet az internetről

Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza a nyilvános IP-címet a virtuális gépek. A következő példa a nyilvános IP-címét adja vissza a *myVm1* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Cserélje le `<publicIpAddress>` az alábbi parancs a nyilvános IP-cím az előző parancs által visszaadott, és írja be a következő parancsot: 

```
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozása és a számítógép letölti. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**. Adja meg a felhasználónevet és a virtuális gép létrehozásakor megadott jelszót. Válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása. Kattintson az **OK** gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a figyelmeztetést kap, válassza ki a **Igen** vagy **Folytatás**, hogy a kapcsolat.

## <a name="communicate-privately-between-vms"></a>Magánjellegű virtuális gépek közötti kommunikáció

A PowerShell használatával a *myVm1* VM, adja meg `ping myvm2`. Pingelés nem sikerül, mert az internet control message protocol (ICMP) és az ICMP ping keresztül nem engedélyezett a Windows tűzfal alapértelmezés szerint.

Hogy *myVm2* ping *myVm1* egy későbbi lépésben adja meg a következő parancsot a PowerShell, amely lehetővé teszi az ICMP bejövő a Windows tűzfalon keresztül:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Zárja be a távoli asztali kapcsolatot *myVm1*. 

Hajtsa végre a [csatlakozás a virtuális gépek az internetről](#connect-to-a-vm-from-the-internet) újra, de csatlakozni *myVm2*. 

A parancssorból a *myVm2* VM, adja meg `ping myvm1`.

A választ kap *myVm1*, mert a a Windows tűzfalon keresztül a ICMP engedélyezett. a *myVm1* VM az előző lépésben.

Zárja be a távoli asztali kapcsolatot *myVm2*.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) eltávolítása az erőforráscsoport és az összes olyan erőforrást tartalmaz:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy alapértelmezett virtuális hálózat és két virtuális gépeket. Egy virtuális gép csatlakozik az interneten, és közvetlenül a Microsoftnak továbbítani a virtuális gép és egy másik virtuális gép között. Virtuális hálózati beállításaival kapcsolatos további tudnivalókért lásd: [egy virtuális hálózat kezeléséhez](manage-virtual-network.md). 

Alapértelmezés szerint az Azure lehetővé teszi, hogy a virtuális gépek korlátlan titkos kommunikációját, de csak lehetővé teszi a bejövő távoli asztali kapcsolatokhoz a Windows virtuális gépek az internetről. Annak megismerése, hogyan engedélyezésére vagy korlátozására a különböző hálózati kommunikációs felé és felől a virtuális gépek, a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Hálózati forgalom szűrésére](virtual-networks-create-nsg-arm-ps.md)
