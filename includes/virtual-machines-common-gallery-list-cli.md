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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226019"
---
## <a name="using-rbac-to-share-images"></a>Az RBAC használatával lemezképek megosztása

Képek megoszthatja a szerepköralapú hozzáférés-vezérlés (RBAC) segítségével előfizetések között. Azok a felhasználók, az olvasási engedéllyel rendelkezik az egy kép verzióra még több előfizetés, helyezhet üzembe egy virtuális gépet, a lemezkép-verzió lesz.

Az RBAC használatával erőforrások megosztása kapcsolatos további információkért lásd: [RBAC és az Azure CLI-hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Lista adatai

A hely, állapota és a rendelkezésre álló lemezkép gyűjtemények használatával kapcsolatos egyéb információkat [az sig lista](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Egy katalógusban, többek között az operációs rendszer típusát és állapotát, használja a kép definíciók listázása [az sig lemezkép-definíciós lista](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

A megosztott lemezkép verziója a katalógus, lista használatával [az sig lemezkép-verzió lista](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Egy rendszerkép verziója a Azonosítójának lekéréséhez [az sig lemezkép-verzió megjelenítése](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```