---
title: Virtuális gép létrehozása felügyelt rendszerképből az Azure-ban |} A Microsoft Docs
description: Hozzon létre egy Windows virtuális gép rendszerképéből felügyelt Azure PowerShell vagy az Azure portal használatával a Resource Manager-alapú üzemi modellben.
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
ms.openlocfilehash: 6baf784068b1fba0c35d2848b8d2dda4f1064a2d
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867980"
---
# <a name="create-a-vm-from-a-managed-image"></a>Virtuális gép létrehozása felügyelt rendszerképből

Több virtuális gépet létrehozhat egy felügyelt Virtuálisgép-rendszerképet, a PowerShell vagy az Azure portal használatával. Egy felügyelt Virtuálisgép-rendszerképet a virtuális gép, beleértve az operációs rendszer és az adatlemezek létrehozásához szükséges információkat tartalmazza. Győződjön meg a rendszerkép, beleértve az operációsrendszer-lemez és bármely adatlemez, felügyelt lemezként tárolt virtuális merevlemezek. 

Már rendelkeznie kell [felügyelt Virtuálisgép-rendszerkép létrehozása](capture-image-resource.md) az új virtuális gép létrehozásához használt. 

## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure portált](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **Minden erőforrás** elemet. Rendezheti az erőforrásokat a **típus** használatával könnyedén megtalálhatja a lemezképeket.
3. Válassza ki a listából a használni kívánt kép. A kép **áttekintése** lap megnyitásakor.
4. Kattintson a **+ virtuális gép létrehozása** a menüből.
5. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Amikor végzett, kattintson az **OK** gombra. Az új virtuális gép létrehozása egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása** , hozzon létre egy új erőforráscsoportot a virtuális gép tárolásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **beállítások**, szükség szerint módosítsa, majd kattintson a **OK**. 
8. Az összefoglalás lapon kell megjelennie a rendszerkép neve, a felsorolt **privát rendszerkép**. Kattintson a **Ok** a virtuális gép üzembe helyezésének megkezdéséhez.


## <a name="use-powershell"></a>A PowerShell használata

PowerShell segítségével hozzon létre egy virtuális Gépet egy rendszerképből, az egyszerűsített paraméter beállítása a [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) parancsmagot. A kép kell lennie a azonos erőforráscsoportban, ahol szeretné a virtuális gép létrehozása.

Ebben a példában van szükség az AzureRM-modul 5.6.0-s vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Az egyszerűsített paramétert csak a New-azurermvm parancsmaghoz szükséges, adja meg a nevét, a csoport- és képfájlok erőforrásnév hozhat létre virtuális Gépet egy rendszerképből, de az értékét fogja használni a **-név** paramétert, az összes erőforrás nevét, hogy az informatikai automatikusan létrehozza. Ebben a példában azt adja meg az erőforrás részletesebb nevét, de automatikus létrehozása a parancsmag segítségével. Hozhat létre erőforrásokat, például a virtuális hálózathoz, időben, és írjon be a nevet a parancsmagba. Ha azt a nevük találja azokat a meglévő erőforrások fogja használni.

A következő példában létrehozunk egy nevű virtuális Gépet *myVMFromImage*, a a *myResourceGroup* erőforráscsoport rendszerkép *myImage*. 


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
Az új virtuális gépet az Azure PowerShell-lel kezelhető, lásd: [létrehozása és a Windows virtuális gépek kezelése az Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

