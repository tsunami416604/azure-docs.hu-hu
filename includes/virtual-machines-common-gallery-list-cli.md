---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0e8972b1b2bfaac12baee1ea823429749ed70461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82792751"
---
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