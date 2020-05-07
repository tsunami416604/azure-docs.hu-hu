---
title: Rendszerkép létrehozása virtuális gépről (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre egy rendszerképet egy meglévő virtuális gépről az Azure-ban a Azure PowerShell használatával egy megosztott Képtárban.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 454ed810f950924d3dd790a2442fe29816bf940d
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838467"
---
# <a name="preview-create-an-image-from-a-vm"></a>Előzetes verzió: rendszerkép létrehozása virtuális gépről

Ha rendelkezik egy meglévő virtuális géppel, amelyet több, azonos virtuális gép létrehozásához kíván használni, akkor a virtuális gép használatával létrehozhat egy rendszerképet egy megosztott rendszerkép-katalógusban Azure PowerShell segítségével. Az [Azure CLI](image-version-vm-cli.md)használatával is létrehozhat egy rendszerképet egy virtuális gépről.

A rendszerképeket a Azure PowerShell használatával is rögzítheti a [speciális és az általánosított](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) virtuális gépekről. 

A képkatalógusban található lemezképek két összetevővel rendelkeznek, amelyeket a következő példában hozunk létre:
- A **rendszerkép definíciója** információt nyújt a rendszerképekről és az azok használatára vonatkozó követelményekről. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. 
- A **rendszerkép verziója** a virtuális gépek megosztott képtárat használó létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. A virtuális gép létrehozásakor a rendszer a rendszerkép verzióját használja új lemezek létrehozásához a virtuális géphez. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához rendelkeznie kell egy meglévő megosztott rendszerkép-katalógussal és egy Azure-beli meglévő virtuális géppel, amelyet forrásként kíván használni. 

Ha a virtuális gépnek van csatolt adatlemeze, az adatlemez mérete nem lehet nagyobb 1 TB-nál.

A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.


## <a name="get-the-gallery"></a>A katalógus beszerzése

Az összes gyűjteményt és képdefiníciót név szerint listázhatja. Az eredmények formátuma `gallery\image definition\image version`.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Ha megtalálta a megfelelő katalógus-és képdefiníciókat, hozzon létre változókat a későbbi használatra. Ez a példa a *MyGallery* nevű katalógust olvassa be a *myResourceGroup* erőforráscsoporthoz.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>A virtuális gép beszerzése

A [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)használatával megtekintheti az erőforráscsoporthoz elérhető virtuális gépek listáját. Miután megismerte a virtuális gép nevét és a hozzá tartozó erőforráscsoportot, `Get-AzVM` újra használhatja a virtuálisgép-objektum beolvasását és egy változóban történő tárolását. Ez a példa egy *sourceVM* nevű virtuális gépet kap a "myResourceGroup" erőforráscsoporthoz, és hozzárendeli azt a (z) *$sourceVm*változóhoz. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

Ajánlott eljárás a virtuális gép stop\deallocate, mielőtt a [stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)használatával létrehoz egy rendszerképet.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása 

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. A rendszer a rendszerképpel kapcsolatos információk kezelésére szolgál. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

A rendszerkép meghatározásakor győződjön meg arról, hogy a megfelelő információval rendelkezik. Ha általánosítja a virtuális gépet (a Windows Sysprep használatával vagy a Linux waagent), akkor létre kell hoznia egy rendszerkép-definíciót a `-OsState generalized`használatával. Ha nem általánosítta a virtuális gépet, hozzon létre egy rendszerkép `-OsState specialized`-definíciót a használatával.

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Hozza létre a rendszerkép definícióját a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)használatával. 

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy speciális, Windows rendszerű virtuális géphez van. A Linux rendszerű képek definíciójának létrehozásához használja `-OsType Linux`a következőt:. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Rendszerkép-verzió létrehozása

Hozzon létre egy rendszerkép [-verziót a New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)használatával. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és a rendszer replikálja az *USA nyugati középső* régiójában és az *USA déli középső* régiójában. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a *forrás* régiót is meg kell adnia a replikálás céljának.

A virtuális gépről származó rendszerkép-verzió létrehozásához használja `$vm.Id.ToString()` a következőt: `-Source`.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Eltarthat egy ideig, amíg replikálja a rendszerképet az összes megcélzott régióba, így létrehoztunk egy feladatot, hogy nyomon kövessük a folyamat előrehaladását. A feladatok előrehaladásának megtekintéséhez írja be a következőt `$job.State`:.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> A rendszerképet a prémium tárolóban is tárolhatja egy hozzáadási `-StorageAccountType Premium_LRS`vagy a [zóna redundáns tárterületével](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , `-StorageAccountType Standard_ZRS` ha létrehozza a rendszerkép verzióját.
>

## <a name="next-steps"></a>További lépések

Miután meggyőződött róla, hogy az új rendszerkép verziója megfelelően működik, létrehozhat egy virtuális gépet. Hozzon létre egy virtuális gépet egy [speciális rendszerkép-verzióból](vm-specialized-image-version-powershell.md) vagy egy [általánosított rendszerkép-verzióból](vm-generalized-image-version-powershell.md).
