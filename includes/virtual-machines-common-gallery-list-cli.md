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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66226019"
---
## <a name="using-rbac-to-share-images"></a>Képek megosztása az RBAC segítségével

A szerepköralapú hozzáférés-vezérlés (RBAC) használatával a képeket előfizetések között is megoszthatja. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik egy lemezkép-verzióhoz, még az előfizetések között is, a lemezkép-verzió használatával telepítheti a virtuális gépet.

Az erőforrások RBAC használatával történő megosztásáról a [Hozzáférés kezelése az RBAC és](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)az Azure CLI használatával című témakörben talál további információt.


## <a name="list-information"></a>Listainformáció

Az [az sig lista](/cli/azure/sig#az-sig-list)segítségével lepeltheti a rendelkezésre álló képgalériák helyét, állapotát és egyéb információit.

```azurecli-interactive 
az sig list -o table
```

Sorolja fel a képdefiníciókat egy gyűjteményben, beleértve az operációs rendszer típusára és állapotára vonatkozó információkat [az az sig image-definition lista használatával.](/cli/azure/sig/image-definition#az-sig-image-definition-list)

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

A megosztott képverziók felsorolása egy gyűjteményben az [sig image-version lista használatával.](/cli/azure/sig/image-version#az-sig-image-version-list)

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

A képverzió azonosítójának beszereznie az [sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show)használatával.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```