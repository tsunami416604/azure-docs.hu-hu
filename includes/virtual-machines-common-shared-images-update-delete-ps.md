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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179082"
---
## <a name="update-resources"></a>Erőforrások frissítése

Vannak bizonyos korlátai, mit lehet frissíteni a. A következő elemek frissíthetők: 

Megosztott képgyűjtemény:
- Leírás

kép definíciója:
- Ajánlott vcpu-k
- Ajánlott memóriaméret
- Leírás
- Élettartam dátum vége

Lemezkép verziója:
- Regionális replikáinak száma
- Célrégiók
- Legújabb kizárása
- Élettartam dátum vége

Ha azt tervezi, a replika régiók felvétele, ne törölje a forrás felügyelt rendszerképet. A felügyelt forráslemezkép a rendszerkép verziószámát további régióban való replikálásához szükséges. 

A gyűjtemény leírását frissítéséhez használja [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Ez a példa bemutatja, hogyan használható [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) frissíteni a teljes életciklusa dátumot a rendszerkép definíciójában.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Ez a példa bemutatja, hogyan használható [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) használva, a lemezkép verziója kizárása a *legújabb* kép.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erőforrások törlésekor kell kezdődnie utolsó eleme a beágyazott erőforrások – a rendszerkép verziószámát. A törölt verziók is törölheti a rendszerkép definíciójában. A katalógus nem törölhető, amíg alatta összes erőforrás törölve lett.

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

