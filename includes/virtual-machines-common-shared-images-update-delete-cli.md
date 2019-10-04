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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179131"
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

Frissítse a katalógus használatával leírását ([az aláírás-frissítési](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Frissítse a leírást egy kép definíció használatával [az sig kép – definíciófrissítést](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Frissítés használatával replikálni egy régió hozzáadása egy lemezkép verziója [az sig lemezkép-verzió frissítése](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Ez a változás eltarthat egy ideig, a lemezkép replikálja az új régióban.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Erőforrások törlése

A rendszerkép verziószámát először törlésével fordított sorrendben, törölheti az erőforrást rendelkezik. Összes lemezkép változatot törlését követően törölheti a rendszerkép definíciójában. Az összes rendszerkép-definíciók törlését követően törölheti a katalógusban. 

Egy rendszerkép verziója a Törlés [az sig lemezkép-verzió törlése](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Egy kép definíciója a törlése [az sig lemezkép-definíció törlése](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Egy katalógus segítségével törlése [az sig törlése](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
