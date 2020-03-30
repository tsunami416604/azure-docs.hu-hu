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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179131"
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

A galéria leírásának frissítése a ([az sig frissítéssel](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


A képdefiníció leírásának frissítése az [sig image-definition frissítéssel.](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

A lemezkép verziójának frissítése az [az sig image-version frissítéssel](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update)replikálandó terület hozzáadásához. Ez a módosítás eltart egy ideig, amíg a rendszerkép replikálása az új régióba.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Erőforrások törlése

Az erőforrásokat fordított sorrendben kell törölnie, először a lemezkép-verzió törlésével. A kép összes verziójának törlése után törölheti a képdefiníciót. Az összes képdefiníció törlése után törölheti a gyűjteményt. 

A képverzió törlése [az sig image-version delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete)használatával.

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Képdefiníció törlése [az sig image-definition delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete)használatával.

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Képgaléria törlése [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete)használatával.

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
