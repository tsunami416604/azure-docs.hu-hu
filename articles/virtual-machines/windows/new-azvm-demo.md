---
title: Egyszerűsített New-AzVM parancsmag használatával az Azure Cloud Shellben Windows virtuális gép létrehozása |} A Microsoft Docs
description: Gyorsan megismerheti a Windows virtuális gépek létrehozása az Azure Cloud Shellben az egyszerűsített New-AzVM parancsmaggal.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 02f2bd78ca5656534b106c6f7f18c05165b4b9ff
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444502"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Windows virtuális gép létrehozása az egyszerűsített New-AzVM parancsmaggal, a Cloud Shellben 

A [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmaggal hozzá van adva egy egyszerűsített készletét paramétereket a PowerShell használatával új virtuális gép létrehozásához. Ez a témakör bemutatja, hogyan használhatja a Powershellt az Azure Cloud Shellben, a New-AzureVM parancsmag előtelepített, hozzon létre egy új virtuális Gépet a legújabb verzióra. Egyszerűsített paraméterkészlet, amely automatikusan létrehozza az intelligens alapértelmezett beállítások használatával az összes szükséges erőforrást használjuk. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>Virtuális gép létrehozása

A [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmaggal intelligens alapértelmezett beállítások segítségével (például az Azure Marketplace-ről származó Windows Server 2016 Datacenter-rendszerkép használatával) hozhat létre virtuális gépeket. Használhatja a New-azvm parancsmag csak a **-név** paramétert, és használja ezt az értéket az összes erőforrás neve. Ebben a példában a **-Name** paraméterhez a *myVM* beállítást használjuk. 

Győződjön meg arról, hogy a Cloud Shellben a **PowerShell** van kiválasztva, és gépelje be a következőt:

```azurepowershell-interactive
New-AzVm -Name myVM
```

A rendszer felkéri a virtuális gép felhasználónevének és jelszavának létrehozására, amelyeket a jelen témakör későbbi részében, a virtuális géphez történő csatlakozáskor fog használni. A jelszónak 12–123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter.

A virtuális gép és az ahhoz kapcsolódó erőforrások létrehozása nagyjából egy percet vesz igénybe. Amikor végzett, láthatja a használatával létrehozott erőforrásokat a [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) parancsmagot.

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

Használja a [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) parancsot a virtuális gép nyilvános IP-címének visszaadásához. Jegyezze fel az IP-címet.

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

A helyi gépen nyisson meg egy parancssort, és használja a **mstsc** parancsot az új virtuális gép egy távoli asztali munkamenet elindításához. Cserélje le a &lt;publicIPAddress&gt; címet a virtuális gépe IP-címére. Amikor a rendszer felkéri erre, adja meg a virtuális gép létrehozásakor beállított felhasználónevet és jelszót.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Adjon meg másik erőforrásnevet.

Azoknak az erőforrásoknak leíró nevét is megadhatja, és továbbra is fennáll, azokat automatikusan létrehozza. Íme egy példa, ahol a Microsoft rendelkezik nevű több erőforrást az új virtuális gép, többek között egy új erőforráscsoportot.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a témakörben egy egyszerű virtuális gépet helyezett üzembe a New-AzVM parancsmag használatával, majd RDP-n keresztül csatlakozott ahhoz. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
