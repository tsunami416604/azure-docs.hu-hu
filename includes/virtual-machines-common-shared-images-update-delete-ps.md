---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179082"
---
## <a name="update-resources"></a>Erőforrások frissítése

Vannak bizonyos korlátozások, hogy mit lehet frissíteni. A következő elemek frissíthetők: 

Megosztott képgaléria:
- Leírás

Képdefiníció:
- Ajánlott vCPU-k
- Ajánlott memória
- Leírás
- Az életciklus vége dátuma

Kép verziószáma:
- Regionális replikaszám
- Célrégiók
- Kizárás a legújabb
- Az életciklus vége dátuma

Ha replikaterületek hozzáadását tervezi, ne törölje a forrás felügyelt lemezképet. A forrás felügyelt lemezkép szükséges a lemezkép verziójának további régiókba történő replikálásához. 

A gyűjtemény leírásának frissítéséhez használja az [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery)szolgáltatást.

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Ez a példa bemutatja, hogyan használhatja [az Update-AzGalleryImageDefinition segítségével](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) a lemezképdefiníció élettartama megszűnésének dátumának frissítéséhez.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Ez a példa bemutatja, hogyan használhatja [az Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) segítségével a lemezképverzió *kizárásához* a legújabb lemezképként való használatból.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erőforrások törlésekor meg kell kezdeni az utolsó elem a beágyazott erőforrások - a kép verzió. A verziók törlése után törölheti a képdefiníciót. A katalógus nem törölhető, amíg az alatta lévő összes erőforrást nem törölték.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

