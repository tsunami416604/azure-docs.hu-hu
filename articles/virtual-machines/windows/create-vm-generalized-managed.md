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
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 9157765afaa610d207a47e19b73f80ae3898fd68
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977558"
---
# <a name="create-a-vm-from-a-managed-image"></a>Virtuális gép létrehozása felügyelt rendszerképből

Több virtuális gép (VM) az Azure felügyelt virtuális gépből hozhat létre az Azure portal vagy a PowerShell használatával kép. Egy felügyelt Virtuálisgép-rendszerképet a virtuális gép, beleértve az operációs rendszer és az adatlemezek létrehozásához szükséges információkat tartalmazza. A virtuális merevlemezek (VHD) a lemezképet, beleértve az operációsrendszer-lemez és bármely adatlemez alkotó felügyelt lemezként tárolt. 

Egy új virtuális Gépet létrehozni, mielőtt kell [felügyelt Virtuálisgép-lemezkép létrehozása](capture-image-resource.md) forrásaként használandó. 


## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **összes erőforrás**. Rendezheti az erőforrásokat a **típus** használatával könnyedén megtalálhatja a lemezképeket.
3. Válassza ki a listából a használni kívánt kép. A kép **áttekintése** lap megnyitásakor.
4. Válassza ki **hozzon létre virtuális gép** a menüből.
5. Adja meg a virtuális gép adatait. A felhasználónév és jelszó itt megadott jelentkezzen be a virtuális gépen használható. Amikor végzett, válassza ki a **OK**. Az új virtuális gép létrehozása egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása** , hozzon létre egy új erőforráscsoportot a virtuális gép tárolásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **beállítások**, szükség esetén végezze el a módosításokat, és válassza ki **OK**. 
8. Az összefoglalás lapon kell megjelennie a rendszerkép neve szerepel egy **privát rendszerkép**. Válassza ki **Ok** a virtuális gép üzembe helyezésének megkezdéséhez.


## <a name="use-powershell"></a>A PowerShell használata

Virtuális gép létrehozása egy rendszerképből az egyszerűsített paraméter beállított használhatja a Powershellt a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmagot. A kép kell lennie, amelyeken kíván létrehozni a virtuális gép ugyanabban az erőforráscsoportban.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Az egyszerűsített paraméterkészlet a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) csak követeli meg, hogy biztosítson egy név, erőforráscsoport és rendszerkép neve hozhat létre virtuális Gépet egy rendszerképből. Új AzVm értékét fogja használni a **-név** paraméter az összes olyan erőforrást, amely automatikusan hoz létre nevet. Ebben a példában azt adja meg a részletes nevét az erőforrásokat, de lehetővé teszik a parancsmag automatikus létrehozása. Is létre erőforrásokat előzetesen, például a virtuális hálózathoz, és adja át az erőforrás neve a parancsmagba. Új-azvm parancsmag fogja használni a meglévő erőforrások, ha őket a nevük is talál.

A következő példában létrehozunk egy nevű virtuális Gépet *myVMFromImage*, a a *myResourceGroup* erőforráscsoport rendszerkép *myImage*. 


```azurepowershell-interactive
New-AzVm `
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
[Hozzon létre, és a Windows virtuális gépek kezelése az Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

