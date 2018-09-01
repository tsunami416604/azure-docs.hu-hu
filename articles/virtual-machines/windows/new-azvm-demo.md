---
title: Windows virtuális gép létrehozása a New-AzureRMVM parancsmag egyszerűsített az Azure Cloud Shellben |} A Microsoft Docs
description: Gyorsan megismerheti az Azure Cloud Shellben az egyszerűsített New-AzureRMVM parancsmag Windows virtuális gépek létrehozásához.
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
ms.openlocfilehash: defc871caed429eeda1f8672323b48a9c0007c8e
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371737"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Az egyszerűsített New-AzureRMVM parancsmag a Cloud Shellben egy Windows virtuális gép létrehozása 

A [New-AzureRMVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-6.8.1) parancsmaggal hozzá van adva egy egyszerűsített készletét paramétereket a PowerShell használatával új virtuális gép létrehozásához. Ez a témakör bemutatja, hogyan használhatja a Powershellt az Azure Cloud Shellben, a New-AzureVM parancsmag előtelepített, hozzon létre egy új virtuális Gépet a legújabb verzióra. Egyszerűsített paraméterkészlet, amely automatikusan létrehozza az intelligens alapértelmezett beállítások használatával az összes szükséges erőforrást használjuk. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Használhatja a [New-AzureRMVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-6.8.1) parancsmaggal hozzon létre egy virtuális Gépet, amely tartalmazza az Azure Marketplace-ről a Windows Server 2016 Datacenter rendszerképet használó intelligens alapértelmezett beállítások. Használhatja a New-AzureRMVM csak a **-név** paramétert, és használja ezt az értéket az összes erőforrás neve. Ebben a példában a **-Name** paraméterhez a *myVM* beállítást használjuk. 

Győződjön meg arról, hogy a Cloud Shellben a **PowerShell** van kiválasztva, és gépelje be a következőt:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

A rendszer felkéri a virtuális gép felhasználónevének és jelszavának létrehozására, amelyeket a jelen témakör későbbi részében, a virtuális géphez történő csatlakozáskor fog használni. A jelszónak 12–123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter.

A virtuális gép és az ahhoz kapcsolódó erőforrások létrehozása nagyjából egy percet vesz igénybe. Amikor végzett, láthatja a használatával létrehozott erőforrásokat a [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) parancsmagot.

```azurepowershell-interactive
Get-AzureRmResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

A virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Jegyezze fel az IP-címet.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

A helyi gépen nyisson meg egy parancssort, és használja a **mstsc** parancsot az új virtuális gép egy távoli asztali munkamenet elindításához. Cserélje le a &lt;publicIPAddress&gt; címet a virtuális gépe IP-címére. Amikor a rendszer felkéri erre, adja meg a virtuális gép létrehozásakor beállított felhasználónevet és jelszót.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Adjon meg másik erőforrásnevet.

Azoknak az erőforrásoknak leíró nevét is megadhatja, és továbbra is fennáll, azokat automatikusan létrehozza. Íme egy példa, ahol a Microsoft rendelkezik nevű több erőforrást az új virtuális gép, többek között egy új erőforráscsoportot.

```azurepowershell-interactive
New-AzureRmVm `
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

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVM
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a témakörben egy egyszerű virtuális gépet helyezett üzembe a New-AzVM parancsmag használatával, majd RDP-n keresztül csatlakozott ahhoz. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
