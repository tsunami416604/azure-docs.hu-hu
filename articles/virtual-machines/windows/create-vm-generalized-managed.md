---
title: Virtuális gép létrehozása az Azure-ban kezelt lemezképéről |} Microsoft Docs
description: Windows virtuális gép létrehozása Azure PowerShell vagy az Azure-portál használatával a Resource Manager üzembe helyezési modellel általánosított felügyelt lemezképéről.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-vm-from-a-managed-image"></a>Egy felügyelt képre a virtuális gép létrehozása

A PowerShell vagy az Azure-portálon kezelt Virtuálisgép-lemezkép létrehozhat több virtuális géphez. Felügyelt Virtuálisgép-lemezképet a virtuális gépek, beleértve az operációsrendszer- és adatlemezek létrehozásához szükséges információkat tartalmazza. A virtuális merevlemezeket jött létre a lemezképet, beleértve az operációsrendszer-lemezek és adatok lemezzel, felügyelt lemezként tárolja. 

Már van szükség [felügyelt Virtuálisgép-lemezkép létrehozott](capture-image-resource.md) használandó új virtuális gép létrehozása. 

## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure portált](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **Minden erőforrás** elemet. Rendezheti az erőforrásokat a **típus** könnyen megtalálhatják a képeket.
3. Válassza ki a listából a használni kívánt lemezkép. A kép **áttekintése** lap megnyitásakor.
4. Kattintson a **+ létrehozás VM** a menüből.
5. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Amikor végzett, kattintson az **OK** gombra. Az új virtuális gép létrehozása egy meglévő Resrouce csoportba, vagy válasszon **hozzon létre új** tárolja a virtuális gép új erőforráscsoport létrehozásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **beállítások**, módosítsa szükség szerint, majd kattintson a **OK**. 
8. Az összefoglalás lapon kell megjelennie a lemezkép neve felsorolt **titkos kép**. Kattintson a **Ok** elindítani a virtuális gépek telepítése során.


## <a name="use-powershell"></a>A PowerShell használata

Használhatja a PowerShell használatával hozhat létre egy virtuális Gépet a képet a egyszerűsített paraméterkészlet, az a [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) parancsmag. A kép kell lennie a hol szeretne létrehozni a virtuális gép ugyanabban az erőforráscsoportban.

Ebben a példában a AzureRM 5.6.0 verziója szükséges, vagy később. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Az egyszerűsített paraméterkészlete New-AzureRmVm egyetlen követelménye, hogy a neve, a csoport- és képfájlok erőforrásnév lemezkép létrehozásához adja meg, de értékét fogja használni a **-név** paraméter nevével összes erőforrását, hogy az informatikai automatikusan létrehozza. Ebben a példában a rendszer az erőforrás részletesebb nevét adja meg, de automatikusan létrehozza a parancsmag segítségével. Hozhat létre az erőforrásokhoz, például a virtuális hálózat időben, és továbbítja a parancsmag neve. Ha azt a nevük megtalálja azokat a meglévő erőforrásokat fogja használni.

Az alábbi példakód létrehozza a virtuális gépek nevű *myVMFromImage*, a a *myResourceGroup* erőforráscsoport, a lemezképből nevű *myImage*. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>További lépések
Az új virtuális gépet az Azure PowerShell kezeléséhez, tekintse meg [létrehozása és kezelése Windows virtuális gépek az Azure PowerShell modulra](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

