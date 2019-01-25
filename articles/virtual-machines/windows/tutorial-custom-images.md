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
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f8585023b01de55acb6c1b43b45e27af914a0a96
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884418"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Oktatóanyag: Hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek az Azure PowerShell használatával

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. Ebben az oktatóanyagban létrehoz egy egyéni rendszerképet egy Azure-beli virtuális gépről. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gépek rendszer-előkészítése (Sysprep) és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amely segítségével új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Amennyiben szükséges, [ezzel a mintaszkripttel](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) létrehozhat egyet. Az oktatóanyag elvégzése során cserélje le az erőforráscsoportok és a virtuális gépek neveit, ahol szükséges.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az AzureRM modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.

## <a name="prepare-vm"></a>Virtuális gép előkészítése

Egy virtuális gépről készült rendszerkép létrehozásához először elő kell készítenie a virtuális gépet a virtuális gép általánosításával, felszabadításával, majd a forrásként szolgáló virtuális gép általánosítottként való megjelölésével az Azure-ban.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Windows rendszerű virtuális gép általánosítása a Sysprep használatával

A Sysprep többek között minden személyes fiókadatot eltávolít, a gépet pedig előkészíti rendszerképként való használatra. További információ a Sysprepről: [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx).


1. Csatlakozzon a virtuális géphez.
2. Nyissa meg a parancsablakot rendszergazdaként. Váltson át a *%windir%\system32\sysprep* könyvtárra, majd futtassa a *sysprep.exe* fájlt.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza *A kezdőélmény indítása* lehetőséget, és győződjön meg róla, hogy be van-e jelölve az *Általánosítás* jelölőnégyzet.
4. A **Leállítási beállítások** területen válassza a *Leállítás* lehetőséget, és kattintson az **OK** gombra.
5. A Sysprep a feladat befejezése után leállítja a virtuális gépet. **Ne indítsa újra a virtuális gépet**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Virtuális gép felszabadítása és megjelölése általánosként

A rendszerkép létrehozásához a virtuális gépet fel kell szabadítani, és az Azure-ban általánosként kell megjelölni.

Felszabadította a virtuális gépet a [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) parancsmaggal.

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Adja meg a virtuális gép állapotaként a `-Generalized` értéket a [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) parancsmaggal. 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>A rendszerkép létrehozása

Most létrehozhatja a virtuális gép rendszerképét a [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) és a [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) parancsmagokkal. Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myVM* nevű virtuális gépből.

Töltse be a virtuális gépet. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Hozza létre a rendszerkép-konfigurációt.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Hozza létre a rendszerképet.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Virtuális gépek létrehozása a rendszerképből

Most, hogy már van egy rendszerképe, létrehozhat belőle egy vagy több új virtuális gépet. A virtuális gép egyéni rendszerképből való létrehozása hasonlít a virtuális gép Microsoft Azure Marketplace-rendszerképből való létrehozásához. Ha Marketplace-rendszerképet használ, akkor meg kell adnia a rendszerképre, a rendszerkép szolgáltatójára, az ajánlatra, a termékváltozatra és a verzióra vonatkozó adatokat. A [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmag egyszerűsített paraméterkészletét használva csak az egyéni rendszerkép nevét kell megadnia, amennyiben az ugyanabban az erőforráscsoportban van. 

Az alábbi példa létrehoz egy *myVMfromImage* nevű virtuális gépet a *myImage* rendszerkép alapján a *myResourceGroup* helyen.


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

Az alábbiakban felsorolunk néhány gyakori, a felügyelt rendszerképekkel kapcsolatos feladatot, és ismertetjük, hogy miként lehet ezeket elvégezni a PowerShell használatával.

Listázza az összes rendszerképet név szerint.

```azurepowershell-interactive
$images = Get-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Rendszerkép törlése. Ebben a példában a nevű lemezkép törlése *myImage* származó a *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
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

A következő oktatóanyagban a magas rendelkezésre állású virtuális gépeket ismerheti meg.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)



