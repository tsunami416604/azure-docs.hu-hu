---
title: Megosztott VM-rendszerképek létrehozása az Azure CLI-vel |} A Microsoft Docs
description: Ebből a cikkből elsajátíthatja az Azure CLI használatával hozzon létre egy megosztott rendszerképet a virtuális gépek az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 2f8ddae05b97b3fa6f1d3f65681defdd4e5a38b7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047690"
---
# <a name="preview-create-a-shared-image-gallery-with-the-azure-cli"></a>Előzetes verzió: Az Azure CLI használatával hozzon létre egy megosztott lemezkép-katalógusában

A megosztott képgyűjtemény jelentősen leegyszerűsíti a egyéni lemezkép megosztása a szervezetben. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. A megosztott lemezkép-katalógus lehetővé teszi a VM-rendszerképeit megosztja másokkal a szervezetében, vagy régióban, az AAD-bérlőn belül is. Győződjön meg arról, azokat az elérhető, és kikre meg szeretné osztani azokat a kívánt mely régiókban, melyik képek meg szeretné osztani. Több katalógusok hozhat létre, így logikusan csoportosíthatja a megosztott-lemezképeket. A katalógus által biztosított teljes szerepköralapú hozzáférés-vezérlés (RBAC) legfelsőbb szintű erőforráshoz. Lemezképek lehetnek rendszerverzióval ellátott, és dönthet úgy, hogy minden lemezkép-verzió replikálása számára egy másik Azure-régióban. A katalógus csak felügyelt képekkel működik.

Ez a cikk a saját katalógus és a egy Azure virtuális gép egyéni rendszerképek hoz létre. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy megosztott lemezkép-katalógusában
> * Megosztott kép definíciójának létrehozása
> * Hozzon létre egy megosztott lemezkép verziója
> * Virtuális gép létrehozása egy megosztott rendszerképből
> * Egy erőforrás törlése


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk van szükség, hogy futnak-e az Azure CLI verziója 2.0.46 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Virtuális gép létrehozása a lemezkép verzió használatával [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   -g myGalleryRG \
   -n myVM \
   --image "/subscriptions/<subscription-ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
   --generate-ssh-keys
```

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]



## <a name="next-steps"></a>További lépések

Megosztott kép katalógusokkal kapcsolatos további információkért lásd: a [áttekintése](shared-image-galleries.md).
