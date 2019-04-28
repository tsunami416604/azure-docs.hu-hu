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
ms.openlocfilehash: 7fd5b2051f81a5dc34270a608c1518e8a11678b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542469"
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