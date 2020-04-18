---
title: Oktatóanyag – Egyéni virtuálisgép-lemezképek létrehozása az Azure PowerShell használatával
description: Ebből az oktatóanyagból elsajátíthatja, hogyan használható az Azure PowerShell virtuálisgép-rendszerkép létrehozására az Azure-ban
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 87347cfea0e45d3498c48f07578523a20d5a13e2
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641082"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Oktatóanyag: Egyéni rendszerkép létrehozása Azure virtuális gépről az Azure PowerShell-lel

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Egyéni lemezképek segítségével üzembe helyezési üzembe helyezéseket, és több virtuális gép konzisztenciáját biztosíthatja. Ebben az oktatóanyagban saját egyéni lemezképet hozhat létre egy Azure-beli virtuális gép powershell használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gépek rendszer-előkészítése (Sysprep) és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

Nyilvános előzetes verzióban rendelkezünk az [Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview) szolgáltatással. Egyszerűen írja le a testreszabásokat egy sablonban, és ez fogja kezelni a jelen cikkben ismertetett képlétrehozási lépéseket. [Próbálja ki az Azure Image Builder t (előzetes verzió) című programot.](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder)

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amely segítségével új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Szükség esetén ez a [parancsfájlminta](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) létrehozhat egyet az Ön számára. Az oktatóanyag elvégzése során cserélje le az erőforráscsoportok és a virtuális gépek neveit, ahol szükséges.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="prepare-vm"></a>Virtuális gép előkészítése

Virtuális gép lemezképének létrehozásához elő kell készítenie a forrás virtuális gépet általánosításával, az elosztással, majd az Azure-ral általánosítottként való megjelöléssel.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [A Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).


1. Csatlakozzon a virtuális géphez.
2. Nyissa meg a parancsablakot rendszergazdaként. Módosítsa a könyvtárat *%windir%\system32\sysprep* `sysprep.exe`értékre, majd futtassa a parancsot.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.
4. A **Leállítási beállítások** területen válassza a **Leállítás** lehetőséget, és kattintson az **OK** gombra.
5. A Sysprep a feladat befejezése után leállítja a virtuális gépet. **Ne indítsa újra a virtuális gépet**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Virtuális gép felszabadítása és megjelölése általánosként

A rendszerkép létrehozásához a virtuális gépet fel kell szabadítani, és az Azure-ban általánosként kell megjelölni.

A virtuális gép felszabadítása a [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)használatával.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

Állítsa a virtuális gép `-Generalized` állapotát [set-AzVm használatára.](https://docs.microsoft.com/powershell/module/az.compute/set-azvm) 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>A rendszerkép létrehozása

Most a [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) és a [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage)használatával hozhat létre egy lemezképet a virtuális gépről. Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myVM* nevű virtuális gépből.

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

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. A virtuális gép egyéni rendszerképből való létrehozása hasonlít a virtuális gép Microsoft Azure Marketplace-rendszerképből való létrehozásához. Ha Marketplace-rendszerképet használ, akkor meg kell adnia a rendszerképre, a rendszerkép szolgáltatójára, az ajánlatra, a termékváltozatra és a verzióra vonatkozó adatokat. A [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) parancsmag egyszerűsített paraméterkészletének használatával csak meg kell adnia az egyéni lemezkép nevét, feltéve, hogy ugyanabban az erőforráscsoportban van. Ha azt tervezi, hogy virtuális gép egy másik erőforráscsoportban, adja meg a rendszerkép erőforrás-azonosítóját a -ImageName paraméter.

Ez a példa létrehoz egy *myVMfromImage* nevű virtuális gép a *myImage-lemezképből* a *myResourceGroup*alkalmazásban.


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

Azt javasoljuk, hogy korlátozza az egyidejű központi telepítések számát 20 virtuális gépre egyetlen lemezképből. Ha azt tervezi, hogy több mint 20 virtuális gép több mint 20 virtuális gép ugyanazon egyéni lemezképből, használjon [megosztott képtár](shared-image-galleries.md) több lemezkép replikák. 


## <a name="image-management"></a>Rendszerkép kezelése 

Az alábbiakban felsorolunk néhány gyakori, a felügyelt rendszerképekkel kapcsolatos feladatot, és ismertetjük, hogy miként lehet ezeket elvégezni a PowerShell használatával.

Listázza az összes rendszerképet név szerint.

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

Rendszerkép törlése. Ez a példa törli a *myImage* nevű képet a *myResourceGroup*csoportból.

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

A következő oktatóanyagra a magas rendelkezésre állású virtuális gépek létrehozásának megismeréséhez jusson tovább.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)



