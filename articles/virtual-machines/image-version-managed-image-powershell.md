---
title: Felügyelt rendszerkép átirányítása megosztott képtárba
description: Ismerje meg, hogyan telepítheti át a felügyelt rendszerképeket a Azure PowerShell használatával egy megosztott képtárban lévő rendszerkép-verzióra.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 7e59ee029b1705f6f789812b870de96bbb74a6e5
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223550"
---
# <a name="migrate-from-a-managed-image-to-a-shared-image-gallery-image"></a>Migrálás felügyelt rendszerképből megosztott képgyűjteményi rendszerképre

Ha van egy meglévő felügyelt rendszerképe, amelyet át szeretne telepíteni egy megosztott képkatalógusba, akkor közvetlenül a felügyelt rendszerképből is létrehozhat egy közös rendszerkép-katalógust. Miután tesztelte az új rendszerképet, törölheti a forrás által felügyelt képet. Az [Azure CLI](image-version-managed-image-cli.md)használatával felügyelt rendszerképekről is áttelepítheti a megosztott képgyűjteményt.

A képkatalógusban található lemezképek két összetevővel rendelkeznek, amelyeket a következő példában hozunk létre:
- A **rendszerkép definíciója** információt nyújt a rendszerképekről és az azok használatára vonatkozó követelményekről. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. 
- A **rendszerkép verziója** a virtuális gépek megosztott képtárat használó létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. A virtuális gép létrehozásakor a rendszer a rendszerkép verzióját használja új lemezek létrehozásához a virtuális géphez. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához rendelkeznie kell egy meglévő felügyelt képpel. Ha a felügyelt lemezkép adatlemezt tartalmaz, az adatlemez mérete nem haladhatja meg az 1 TB-ot.

A cikkben végzett munka során szükség esetén cserélje le az erőforráscsoportot és a virtuális gépek nevét.

## <a name="get-the-gallery"></a>A katalógus beszerzése

Az összes gyűjteményt és képdefiníciót név szerint listázhatja. Az eredmények formátuma `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Ha megtalálta a megfelelő katalógust, hozzon létre egy változót, amelyet később szeretne használni. Ez a példa a *MyGallery* nevű katalógust olvassa be a *myResourceGroup* erőforráscsoporthoz.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása 

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. A rendszer a rendszerképpel kapcsolatos információk kezelésére szolgál. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

A rendszerkép meghatározásakor győződjön meg arról, hogy a megfelelő információval rendelkezik. Mivel a felügyelt lemezképek mindig általánosítva vannak, be kell állítani `-OsState generalized` . 

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Hozza létre a rendszerkép definícióját a [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)használatával. Ebben a példában a rendszerkép definíciójának neve *myImageDefinition*, és egy általánosított Windows operációs rendszerre van. A rendszerképek definíciójának létrehozásához használja a következőt: `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>A felügyelt rendszerkép beolvasása

A [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage)használatával megtekintheti az erőforráscsoporthoz elérhető rendszerképek listáját. Ha ismeri a rendszerkép nevét és a benne található erőforráscsoportot, akkor `Get-AzImage` ismét használhatja a rendszerkép-objektum beolvasásához és egy változóban való tárolásához. Ez a példa egy *myImage* nevű rendszerképet kap a "myResourceGroup" erőforráscsoporthoz, és hozzárendeli azt a (z) *$managedImage*változóhoz. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Rendszerképverzió létrehozása

Hozzon létre egy rendszerkép-verziót a felügyelt rendszerképből a [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion)használatával. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és a rendszer replikálja az *USA nyugati középső* régiójában és az *USA déli középső* régiójában. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a *forrás* régiót is meg kell adnia a replikálás céljának. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Eltarthat egy ideig, amíg replikálja a rendszerképet az összes megcélzott régióba, így létrehoztunk egy feladatot, hogy nyomon kövessük a folyamat előrehaladását. A folyamat megjelenítéséhez írja be a következőt: `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához. 
>
> A rendszerképet a prémium tárolóban is tárolhatja egy hozzáadási `-StorageAccountType Premium_LRS` vagy a [zóna redundáns tárterületével](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , `-StorageAccountType Standard_ZRS` Ha létrehozza a rendszerkép verzióját.
>

## <a name="delete-the-managed-image"></a>A felügyelt rendszerkép törlése

Miután meggyőződött róla, hogy az új rendszerkép verziója megfelelően működik, törölheti a felügyelt rendszerképet.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Miután meggyőződött arról, hogy a replikáció elkészült, létrehozhat egy virtuális gépet az [általánosított rendszerképből](vm-generalized-image-version-powershell.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).