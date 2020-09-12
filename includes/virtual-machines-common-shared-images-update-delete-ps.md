---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 6e0612a017650f0c6e4c9f63d9a5fd097b0b92c4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304083"
---
## <a name="update-resources"></a>Erőforrások frissítése

Bizonyos korlátozások vonatkoznak a frissítésre. A következő elemek frissíthetők: 

Megosztott képgyűjtemény:
- Description

Rendszerkép definíciója:
- Ajánlott vCPU
- Ajánlott memória
- Description
- Élettartam vége

Rendszerkép verziója:
- Regionális replika száma
- Célcsoportok
- Legutóbbi kizárás
- Élettartam vége

Ha a replika-régiók hozzáadását tervezi, ne törölje a forrás által felügyelt képet. A forrás által felügyelt rendszerkép a rendszerkép verziójának további régiókba való replikálásához szükséges. 

A katalógus leírásának frissítéséhez használja az [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Ez a példa azt mutatja be, hogyan használható a [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) a rendszerkép definíciójának élettartamának frissítésére.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Ez a példa azt mutatja be, hogyan használható a [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) a lemezkép verziójának a *legújabb* rendszerképként való használatának kizárásához.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Ez a példa bemutatja, hogyan használható a [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) a lemezkép verziójának a *legújabb* rendszerképbe való felvételéhez.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erőforrások törlésekor először a beágyazott erőforrások utolsó elemét kell kezdenie – a rendszerkép verziószámát. A verziók törlése után törölheti a rendszerkép definícióját. A katalógust csak akkor törölheti, ha az összes alatta lévő erőforrás törölve lett.

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

