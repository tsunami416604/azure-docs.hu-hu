---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66226019"
---
## <a name="using-rbac-to-share-images"></a>Képek megosztása a RBAC használatával

A rendszerképeket a szerepköralapú Access Control (RBAC) használatával megoszthatja az előfizetések között. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik a lemezkép verziójához, még az előfizetések között is, telepítheti a virtuális gépet a lemezkép verziójának használatával.

Az erőforrások RBAC használatával történő megosztásával kapcsolatos további információkért lásd: [hozzáférés kezelése a RBAC és az Azure CLI használatával](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Információk listázása

A rendelkezésre álló képtárak helyének, állapotának és egyéb információinak lekérése az az [SIG List](/cli/azure/sig#az-sig-list)paranccsal.

```azurecli-interactive 
az sig list -o table
```

Egy katalógus képdefinícióinak listázása, beleértve az operációs rendszer típusával és állapotával kapcsolatos információkat az [az SIG rendszerkép-definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list)paranccsal.

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

A katalógusban szereplő megosztott rendszerkép-verziók listázása az [az SIG rendszerkép-Version List](/cli/azure/sig/image-version#az-sig-image-version-list)paranccsal.

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Szerezze be egy rendszerkép-verzió AZONOSÍTÓját az [az SIG rendszerkép-Version show](/cli/azure/sig/image-version#az-sig-image-version-show)paranccsal.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```