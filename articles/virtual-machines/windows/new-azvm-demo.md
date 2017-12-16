---
title: "Új-AzureRMVM parancsmaggal egyszerűsített a Azure Cloud rendszerhéj Windows virtuális gép létrehozása |} Microsoft Docs"
description: "Gyorsan ismerje meg az Azure felhőalapú rendszerhéj egyszerűsített New-AzureRMVM parancsmag Windows virtuális gépek létrehozásához."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 94eb6232cf59d502a9d70545785c3788398f4d27
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azurermvm-cmdlet-in-cloud-shell"></a>Az egyszerűsített New-AzureRMVM parancsmag felhő rendszerhéj Windows virtuális gép létrehozása 

A [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) parancsmag hozzá van adva olyan paramétereket a PowerShell használatával új virtuális gép létrehozásához egyszerűsített készlete. Ez a témakör bemutatja, hogyan PowerShell használata Azure-felhő rendszerhéj, a New-AzureVM parancsmag előtelepített, hozzon létre egy új virtuális Gépet a legújabb verzióra. Egy egyszerűsített paraméter halmaza, amelyet automatikusan létrehozza az összes szükséges erőforrást intelligens alapértelmezett beállításaival használjuk. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell telepítéséhez és használatához a helyi mellett dönt, ez az oktatóanyag igényel-e az Azure PowerShell modul verziója 5.1.1-es vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-the-vm"></a>Virtuális gép létrehozása

Használhatja a [New-AzureRMVM](/powershell/module/azurerm.resources/new-azurermvm) parancsmaggal hozzon létre egy virtuális Gépet, amely tartalmazza a Windows Server 2016 Datacenter lemezképet használja az Azure piactérről intelligens alapértékeivel. Használhatja a New-AzureRMVM csak a **-név** paraméter, és használja ezt az értéket az összes erőforrás nevét. Ebben a példában a **-Name** paraméterhez a *myVM* beállítást használjuk. 

Győződjön meg arról, hogy a Cloud Shellben a **PowerShell** van kiválasztva, és gépelje be a következőt:

```azurepowershell-interactive
New-AzureRMVm -Name myVM
```

A rendszer felkéri a virtuális gép felhasználónevének és jelszavának létrehozására, amelyeket a jelen témakör későbbi részében, a virtuális géphez történő csatlakozáskor fog használni. A jelszónak 12–123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter.

A virtuális gép és az ahhoz kapcsolódó erőforrások létrehozása nagyjából egy percet vesz igénybe. Ha elkészült a [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) parancsmag használatával tekintheti meg az összes létrehozott erőforrást.

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

A virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Jegyezze fel az IP-címet.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

A helyi számítógépen nyisson meg egy parancssort, és használja a **mstsc** parancsot az új virtuális gép elindítása egy távoli asztali munkamenetet. Cserélje le a &lt;publicIPAddress&gt; címet a virtuális gépe IP-címére. Amikor a rendszer felkéri erre, adja meg a virtuális gép létrehozásakor beállított felhasználónevet és jelszót.

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>Adja meg a különböző erőforrások neve

Az erőforrások több leíró neveket is nyújt, és továbbra is fennáll, azok automatikusan létrehozza. Íme egy példa, ahol azt rendelkezik több erőforrást a új virtuális Gépnek megfelelő névvel, beleértve az új erőforráscsoport.

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

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben egy egyszerű virtuális gépet helyezett üzembe a New-AzVM parancsmag használatával, majd RDP-n keresztül csatlakozott ahhoz. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
