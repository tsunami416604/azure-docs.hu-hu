---
title: Felügyelt lemez létrehozása lemezkép-verzióból
description: Felügyelt lemez létrehozása lemezkép-verzióból egy megosztott rendszerkép-gyűjteményben.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049749"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Felügyelt lemez létrehozása lemezkép-verzióból

Ha szükséges, a felügyelt lemezt egy megosztott képtárban tárolt lemezkép-verzióból is elvégezheti.


## <a name="cli"></a>parancssori felület

Állítsa a `source` változót a lemezkép verziójának azonosítójára, majd az [az Disk Create](/cli/azure/disk.md#az_disk_create) paranccsal hozza létre a felügyelt lemezt. 


Az [az SIG rendszerkép-Version List](/cli/azure/sig/image-version.md#az_sig_image_version_list)paranccsal megtekintheti a képfájlok listáját. Ebben a példában az összes olyan rendszerkép-verziót keresjük, amelyek a *myImageDefinition* rendszerkép-definíció részét képezik a *MyGallery* -rendszerkép galériájában.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


Ebben a példában egy *myManagedDisk*nevű felügyelt lemezt hozunk létre a *EastUS* régióban egy *myResourceGroup*nevű erőforráscsoporthoz. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Ha a lemez adatlemez, a Hozzáadás `--gallery-image-reference-lun` gombra kattintva adja meg a LUN-t.

## <a name="powershell"></a>PowerShell

A [Get-AzResource](/powershell/module/az.resources/get-azresource)használatával listázhatja az összes rendszerkép-verziót. 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



Ha már rendelkezik az összes szükséges információval, a [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) használatával lekérheti a használni kívánt forrás-rendszerkép verzióját, és hozzárendelheti azt egy változóhoz. Ebben a példában bemutatjuk a `1.0.0` definíció rendszerképének verzióját a forrás-katalógusban az `myImageDefinition` `myGallery` `myResourceGroup` erőforráscsoporthoz.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Állítson be néhány változót a lemez adataihoz.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Hozzon létre egy lemez-konfigurációt, majd a lemezt a forrás rendszerkép-verziójának AZONOSÍTÓjának használatával. A esetében a `-GalleryImageReference` LUN csak akkor szükséges, ha a forrás adatlemez.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Hozza létre a lemezt.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Következő lépések

A felügyelt lemezekről az [Azure CLI](image-version-managed-image-cli.md) vagy a [PowerShell](image-version-managed-image-powershell.md)használatával is létrehozhat lemezkép-verziót.


