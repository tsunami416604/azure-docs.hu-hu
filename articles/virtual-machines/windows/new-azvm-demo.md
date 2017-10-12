---
title: "Windows rendszerű virtuális gép létrehozása New-AzVM parancsmag használatával az Azure Cloud Shellben | Microsoft Docs"
description: "Gyorsan megismerheti a Windows rendszerű virtuális gép New-AzVM parancsmaggal történő létrehozásának módját az Azure Cloud Shellben."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Windows rendszerű virtuális gép létrehozása New-AzVM parancsmag (előzetes verzió) használatával a Cloud Shellben 

A New-AzVM (előzetes verziójú) parancsmaggal egyszerűbben hozhat létre új virtuális gépet a PowerShell használatával. Ez az útmutató részletesen bemutatja a PowerShell használatát az Azure Cloud Shellben előre telepített New-AzVM parancsmaggal, illetve új, Windows Server 2016-ot futtató Azure virtuális gép létrehozásakor. Az üzembe helyezés végeztével RDP használatával csatlakozunk a kiszolgálóhoz.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Virtuális gép létrehozása

A **New-AzVM** parancsmaggal intelligens alapértelmezett beállítások segítségével (például az Azure Marketplace-ről származó Windows Server 2016 Datacenter-rendszerkép használatával) hozhat létre virtuális gépeket. Kizárólag a New-AzVM parancsmagot kell használnia, és az az erőforrások nevei esetében az alapértelmezett értékeket fogja alkalmazni. Ebben a példában a **-Name** paraméterhez a *myVM* beállítást használjuk. Az erőforrásnév előtagjaként a *myVM*-et használva, a parancsmag létrehozza az összes szükséges erőforrást. 

Győződjön meg arról, hogy a Cloud Shellben a **PowerShell** van kiválasztva, és gépelje be a következőt:

```azurepowershell-interactive
New-AzVm -Name myVM
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

A helyi gépen nyisson meg egy parancssort, és az **mstsc** parancs használatával indítson egy távoli asztali munkamenetet az új virtuális géppel. Cserélje le a &lt;publicIPAddress&gt; címet a virtuális gépe IP-címére. Amikor a rendszer felkéri erre, adja meg a virtuális gép létrehozásakor beállított felhasználónevet és jelszót.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben egy egyszerű virtuális gépet helyezett üzembe a New-AzVM parancsmag használatával, majd RDP-n keresztül csatlakozott ahhoz. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
