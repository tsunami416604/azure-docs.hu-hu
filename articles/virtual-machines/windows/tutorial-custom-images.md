---
title: Oktatóanyag – Egyéni virtuálisgép-rendszerképek létrehozása az Azure PowerShell-lel | Microsoft Docs
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure PowerShell virtuálisgép-rendszerkép létrehozására az Azure-ban
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 74087a6d1ce00293c968837e72c636847081e39e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285980"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure PowerShell használatával

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Egyéni rendszerképek segítségével központi telepítések elindíthat és a konzisztencia érdekében több virtuális gép között. Ebben az oktatóanyagban létrehozhat egy egyéni rendszerképet egy Azure virtuális gép PowerShell-lel. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gépek rendszer-előkészítése (Sysprep) és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amely segítségével új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Amennyiben szükséges, [ezzel a mintaszkripttel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) létrehozhat egyet. Az oktatóanyag elvégzése során cserélje le az erőforráscsoportok és a virtuális gépek neveit, ahol szükséges.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="prepare-vm"></a>Virtuális gép előkészítése

Hozzon létre egy rendszerképet a virtuális gépek, készítse elő a forrásoldali virtuális gép általánosítása azt, felszabadítása és megőrzöttként való megjelölését, az Azure-ral általánosítottként kell.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).


1. Csatlakozzon a virtuális géphez.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a *%windir%\system32\sysprep*, majd futtassa a `sysprep.exe`.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **Leállítási beállítások** területen válassza a **Leállítás** lehetőséget, és kattintson az **OK** gombra.
5. A Sysprep a feladat befejezése után leállítja a virtuális gépet. **Ne indítsa újra a virtuális gépet**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Virtuális gép felszabadítása és megjelölése általánosként

A rendszerkép létrehozásához a virtuális gépet fel kell szabadítani, és az Azure-ban általánosként kell megjelölni.

Szabadítsa fel a virtuális Gépet az [Stop-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

A virtuális gép állapotának beállítása `-Generalized` használatával [Set-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>A rendszerkép létrehozása

Most már létrehozhatja a virtuális gép lemezképének használatával [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) és [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage). Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myVM* nevű virtuális gépből.

Töltse be a virtuális gépet. 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

Hozza létre a rendszerkép-konfigurációt.

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

Hozza létre a rendszerképet.

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Virtuális gépek létrehozása a rendszerképből

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. A virtuális gép egyéni rendszerképből való létrehozása hasonlít a virtuális gép Microsoft Azure Marketplace-rendszerképből való létrehozásához. Ha Marketplace-rendszerképet használ, akkor meg kell adnia a rendszerképre, a rendszerkép szolgáltatójára, az ajánlatra, a termékváltozatra és a verzióra vonatkozó adatokat. Az egyszerűsített paraméter beállítása a [New-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmagot, egyszerűen adja meg az egyéni rendszerkép nevét, mindaddig, amíg van ugyanabban az erőforráscsoportban. 

Ez a példa létrehoz egy virtuális gép nevű *myVMfromImage* származó a *myImage* kép a *myResourceGroup*.


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

## <a name="image-management"></a>Rendszerkép kezelése 

Az alábbiakban felsorolunk néhány gyakori, a felügyelt rendszerképekkel kapcsolatos feladatot, és ismertetjük, hogy miként lehet ezeket elvégezni a PowerShell használatával.

Listázza az összes rendszerképet név szerint.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Rendszerkép törlése. Ebben a példában a nevű lemezkép törlése *myImage* származó a *myResourceGroup*.

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális gépek rendszer-előkészítése (Sysprep) és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan hozhat létre magas rendelkezésre állású virtuális gépeket.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)



