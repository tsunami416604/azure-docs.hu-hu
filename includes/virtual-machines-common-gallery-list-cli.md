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
ms.openlocfilehash: f2e252a000c5633b2cc1ef34683d7d2c7c133c03
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047679"
---
## <a name="using-rbac-to-share-images"></a>Az RBAC használatával lemezképek megosztása

Képek megoszthatja a szerepkör alapú hozzáférés-vezérlés (RBAC) segítségével előfizetések között. Azok a felhasználók, az olvasási engedéllyel rendelkezik az egy kép verzióra még több előfizetés, helyezhet üzembe egy virtuális gépet, a lemezkép-verzió lesz.

Az RBAC használatával erőforrások megosztása kapcsolatos további információkért lásd: [RBAC és az Azure CLI-hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Lista adatai

A hely, állapota és a rendelkezésre álló lemezkép gyűjtemények használatával kapcsolatos egyéb információkat [az sig lista](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Egy katalógusban, többek között az operációs rendszer típusát és állapotát, használja a kép definíciók listázása [az sig lemezkép-definíciós lista](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

A megosztott lemezkép verziója a katalógus, lista használatával [az sig lemezkép-verzió lista](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

Egy rendszerkép verziója a Azonosítójának lekéréséhez [az sig lemezkép-verzió megjelenítése](/cli/azure/sig/image-version#az-sig-image-version-show).

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```