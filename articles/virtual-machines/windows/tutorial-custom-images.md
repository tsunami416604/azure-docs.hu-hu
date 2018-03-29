---
title: Egyéni Virtuálisgép-lemezképek létrehozása az Azure PowerShell használatával |} Microsoft Docs
description: Útmutató – hozzon létre egy egyéni Virtuálisgép-lemezkép az Azure PowerShell használatával.
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
ms.date: 03/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 443f47b98ea063c6fe1f0b3517c00b6cf3692161
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Egy Azure-VM PowerShell-lel egyéni lemezkép létrehozása

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. Ebben az oktatóanyagban létrehoz egy egyéni rendszerképet egy Azure-beli virtuális gépről. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Sysprep és a virtuális gépek generalize
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése


## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amely segítségével új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Amennyiben szükséges, [ezzel a mintaszkripttel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) létrehozhat egyet. Az oktatóanyag elvégzése során cserélje le az erőforráscsoportok és a virtuális gépek neveit, ahol szükséges.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell telepítéséhez és használatához a helyi mellett dönt, ebben az oktatóanyagban igényel-e a AzureRM 5.6.0 verziója vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="prepare-vm"></a>Virtuális gép előkészítése

Hozzon létre egy virtuális gép lemezképét, meg kell készíteni a virtuális gép a virtuális gép normalizálása, felszabadítása, és majd a forrás virtuális gép általánosítva van az Azure-ban.

### <a name="generalize-the-windows-vm-using-sysprep"></a>A Windows virtuális gép Sysprep generalize

A Sysprep eltávolítja a személyes adatok, többek között, és előkészíti a számítógépet, hogy képként használni. A Sysprep kapcsolatos részletekért lásd: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx).


1. Csatlakozzon a virtuális géphez.
2. Nyissa meg a parancssort rendszergazdaként. Lépjen be *%windir%\system32\sysprep*, majd futtassa a *sysprep.exe*.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki *adja meg a rendszer Out-of-Box élmény (OOBE)*, és győződjön meg arról, hogy a *Generalize* jelölőnégyzet be van jelölve.
4. A **leállítási beállítások**, jelölje be *leállítási* majd **OK**.
5. A Sysprep befejezését követően a virtuális gép leáll. **Ne indítsa újra a virtuális gép**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Virtuális gép felszabadítása és megjelölése általánosként

A képfájl létrehozásához a virtuális gép kell felszabadítása. lehetséges, és az Azure-ban általánosítva van megjelölve.

A virtuális gép használatával felszabadítása [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

A virtuális gép állapotának beállítása `-Generalized` használatával [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>A rendszerkép létrehozása

Most a virtuális gép lemezképét segítségével létrehozható [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) és [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myVM* nevű virtuális gépből.

Helyezze a virtuális gépet. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

A lemezkép-konfiguráció létrehozása.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

A lemezkép létrehozása.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Virtuális gépek létrehozása a rendszerképből

Most, hogy egy lemezképet, létrehozhat egy vagy több új virtuális gépek a lemezképből. Virtuális gép létrehozása egy egyéni lemezképből nagyon hasonlít a Piactéri lemezképről virtuális gép létrehozása. A Piactéri lemezkép használata esetén meg kell adnia az információkat a kép, kép szolgáltató, ajánlat, SKU és verziót. Az egyszerűsített paraméter beállítása a [New-AzureRMVM]() parancsmag, csupán be kell, amíg ugyanabban az erőforráscsoportban van, adja meg az egyéni lemezkép nevét. 

Ez a példa létrehoz egy nevű virtuális gép *myVMfromImage* a a *myImage*, a a *myResourceGroup*.


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

## <a name="image-management"></a>Rendszerkép kezelése 

Néhány példa általános kezelési kép feladatok és hogyan hajthatja végre ezeket a PowerShell használatával.

Listázza az összes lemezkép neve.

```azurepowershell-interactive
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Rendszerkép törlése. Ez a példa törli a *myOldImage* nevű rendszerképet a *myResourceGroup* erőforráscsoportból.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Sysprep és a virtuális gépek generalize
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

Előzetes megtudhatja, hogyan magas rendelkezésre állású virtuális gépek kapcsolatos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)



