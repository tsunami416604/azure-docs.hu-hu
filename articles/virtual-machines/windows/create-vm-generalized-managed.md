---
title: Virtuális gép létrehozása felügyelt lemezképből az Azure-ban
description: Hozzon létre egy Windows-virtuális gépet egy általános felügyelt lemezképből az Azure PowerShell vagy az Azure Portal használatával, az Erőforrás-kezelő telepítési modelljében.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: de59edc2e2c702993efd6187a590264d9aac16a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74841931"
---
# <a name="create-a-vm-from-a-managed-image"></a>Virtuális gép létrehozása felügyelt rendszerképből

Az Azure Portalvagy a PowerShell használatával több virtuális gépet (VM-et) hozhat létre egy Azure által felügyelt virtuális géplemezképből. A felügyelt virtuálisgép-lemezkép tartalmazza a virtuális gép létrehozásához szükséges információkat, beleértve az operációs rendszert és az adatlemezeket. A lemezképet felállító virtuális merevlemezek (VHD-k), beleértve az operációs rendszer lemezeket és az adatlemezeket is, felügyelt lemezként tárolódnak. 

Új virtuális gép létrehozása előtt létre kell [hoznia egy felügyelt virtuálisgép-lemezképet,](capture-image-resource.md) amelyet forráslemezként használhat, és olvasási hozzáférést kell biztosítania a lemezképen minden olyan felhasználószámára, akinek hozzáféréssel kell rendelkeznie a lemezképhez. 


## <a name="use-the-portal"></a>A portál használata

1. A felügyelt lemezkép megkereséséhez keresse meg az [Azure Portalon.](https://portal.azure.com) Keresse meg és válassza a **Képek**lehetőséget.
3. Jelölje ki a listában a használni kívánt képet. Megnyílik a kép **áttekintése** lap.
4. Válassza a menü **Virtuálisgép létrehozása** parancsát.
5. Adja meg a virtuális gép adatait. Az itt megadott felhasználónév és jelszó a virtuális gépbe való bejelentkezéshez lesz használva. Ha elkészült, válassza **az OK gombot.** Létrehozhatja az új virtuális gép egy meglévő erőforráscsoportban, vagy válassza **az Új létrehozása** a virtuális gép tárolására egy új erőforráscsoport létrehozásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **Beállítások csoportban**szükség szerint módosítsa a szükséges módosításokat, és válassza **az OK gombot.** 
8. Az összefoglaló lapon a kép neve **privát képként**jelenik meg. Válassza **az Ok gombot** a virtuális gép üzembe helyezésének elindításához.


## <a name="use-powershell"></a>A PowerShell használata

A PowerShell segítségével virtuális gép létrehozása egy lemezképből az egyszerűsített paraméterkészlet a [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmag használatával. A lemezképnek ugyanabban az erőforráscsoportban kell lennie, ahol létre fogja hozni a virtuális gép.

 

A [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) egyszerűsített paraméterkészlete csak egy nevet, erőforráscsoportot és lemezképnevet igényel, hogy egy lemezképből virtuális gép jön létre. A New-AzVm a **-Name** paraméter értékét fogja használni az összes automatikusan létrehozott erőforrás neveként. Ebben a példában részletesebb neveket biztosítunk az egyes erőforrásokhoz, de a parancsmag automatikusan létrehozhatja őket. Előzetesen is létrehozhat erőforrásokat, például a virtuális hálózatot, és átadhatja az erőforrás nevét a parancsmagba. A New-AzVm a meglévő erőforrásokat használja, ha megtalálja őket a nevük alapján.

A következő példa létrehoz egy *myVMFromImage*nevű virtuális gép , a *myResourceGroup* erőforráscsoportban a *myImage*nevű lemezképből. 


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
[Windows-virtuális gépek létrehozása és kezelése az Azure PowerShell-modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

