---
title: Oktatóanyag – egyéni virtuálisgép-rendszerképek létrehozása a Azure PowerShell
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure PowerShell virtuálisgép-rendszerkép létrehozására az Azure-ban
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 108ff8d89771217ed2833f2a47aa52ff05aa2f13
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100379"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Oktatóanyag: Egyéni rendszerkép létrehozása Azure virtuális gépről az Azure PowerShell-lel

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni lemezképek a központi telepítések indítására és a több virtuális gép közötti konzisztencia biztosítására használhatók. Ebben az oktatóanyagban egy Azure-beli virtuális gép saját egyéni rendszerképét hozza létre a PowerShell használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gépek rendszer-előkészítése (Sysprep) és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

A nyilvános előzetes verzióban elérhető az [Azure VM rendszerkép-készítő](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) szolgáltatás. Egyszerűen írja le a testreszabásokat egy sablonban, és kezelje a cikk rendszerkép-létrehozási lépéseit. [Próbálja ki az Azure rendszerkép-készítőt (előzetes verzió)](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder).

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amely segítségével új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Ha szükséges, a [parancsfájl-minta](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) létrehozhat egyet. Az oktatóanyag elvégzése során cserélje le az erőforráscsoportok és a virtuális gépek neveit, ahol szükséges.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell). A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="prepare-vm"></a>Virtuális gép előkészítése

Egy virtuális gép rendszerképének létrehozásához az általánosításával, a felszabadításával, majd az Azure-nal való általánosított megjelöléssel elő kell készítenie a forrás virtuális gépet.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [A Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).


1. Csatlakozzon a virtuális géphez.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat a *%WINDIR%\system32\sysprep*értékre, majd `sysprep.exe`futtassa a parancsot.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **Leállítási beállítások** területen válassza a **Leállítás** lehetőséget, és kattintson az **OK** gombra.
5. A Sysprep a feladat befejezése után leállítja a virtuális gépet. **Ne indítsa újra a virtuális gépet**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Virtuális gép felszabadítása és megjelölése általánosként

A rendszerkép létrehozásához a virtuális gépet fel kell szabadítani, és az Azure-ban általánosként kell megjelölni.

Szabadítsa fel a virtuális gépet a [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)használatával.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Állítsa be a virtuális gép állapotát a `-Generalized` [set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm)használatával. 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>A rendszerkép létrehozása

Most létrehozhat egy rendszerképet a virtuális gépről a [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) és a [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage)használatával. Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myVM* nevű virtuális gépből.

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

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. A virtuális gép egyéni rendszerképből való létrehozása hasonlít a virtuális gép Microsoft Azure Marketplace-rendszerképből való létrehozásához. Ha Marketplace-rendszerképet használ, akkor meg kell adnia a rendszerképre, a rendszerkép szolgáltatójára, az ajánlatra, a termékváltozatra és a verzióra vonatkozó adatokat. A [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmag egyszerűsített paraméterének használatával csak akkor kell megadnia az egyéni rendszerkép nevét, ha ugyanabban az erőforráscsoporthoz van. Ha egy másik erőforráscsoporthoz szeretné létrehozni a virtuális gépet, adja meg a-ImageName paraméter erőforrás-azonosítóját.

Ez a példa egy *myVMfromImage* nevű virtuális gépet hoz létre a *myImage* -rendszerképből a *myResourceGroup*-ben.


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

Azt javasoljuk, hogy egyetlen rendszerképből korlátozza az egyidejű központi telepítések számát 20 virtuális gépre. Ha több mint 20 virtuális gép nagy léptékű, egyidejű üzembe helyezését tervezi ugyanazon egyéni rendszerképből, több rendszerkép-replikával rendelkező [megosztott képtárat](shared-image-galleries.md) kell használnia. 


## <a name="image-management"></a>Rendszerkép kezelése 

Az alábbiakban felsorolunk néhány gyakori, a felügyelt rendszerképekkel kapcsolatos feladatot, és ismertetjük, hogy miként lehet ezeket elvégezni a PowerShell használatával.

Listázza az összes rendszerképet név szerint.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Rendszerkép törlése. Ez a példa törli a *myImage* nevű rendszerképet a *myResourceGroup*.

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

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre magasan elérhető virtuális gépeket.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)



