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
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: f69b1aff28165b9bf37c49fe62d1fb5aada91285
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236404"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Hozzon létre egy megosztott lemezkép-katalógus az Azure CLI-vel

A [megosztott Képkatalógus](shared-image-galleries.md) egyszerűbbé teszi az egyéni rendszerkép megosztása a szervezetben. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott lemezkép-katalógus lehetővé teszi a VM-rendszerképeit megosztja másokkal a szervezetében, vagy régióban, az AAD-bérlőn belül is. Győződjön meg arról, azokat az elérhető, és kikre meg szeretné osztani azokat a kívánt mely régiókban, melyik képek meg szeretné osztani. Több katalógusok hozhat létre, így logikusan csoportosíthatja a megosztott-lemezképeket. 

A katalógus által biztosított teljes szerepköralapú hozzáférés-vezérlés (RBAC) legfelsőbb szintű erőforráshoz. Lemezképek lehetnek rendszerverzióval ellátott, és dönthet úgy, hogy minden lemezkép-verzió replikálása számára egy másik Azure-régióban. A katalógus csak felügyelt képekkel működik.

A megosztott Képkatalógus funkció több erőforrástípusok rendelkezik. A Microsoft fog használatával vagy ezek ebben a cikkben létrehozásához:

| Resource | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Ez a alapvető-önmagában, vagy létrehozásához használt lemezkép egy **lemezkép verziója** egy rendszerkép-katalógusában. Felügyelt lemezképek általánosított virtuális gépek jönnek létre. Egy felügyelt rendszerképet egy speciális típusú virtuális Merevlemezt, amely használható, hogy több virtuális gép, és most már használható létrehozásához megosztott kép verzió. |
| **Lemezkép-katalógusában** | Az Azure piactéren, például egy **lemezkép-katalógusában** egy adattár a kezelése és megosztása a rendszerképeket, de Ön szabályozza, ki férhet. |
| **Rendszerkép definíciójában** | Képek ugyanazon a katalóguson belül határozza meg, és a lemezkép és a belső útmutatójához követelményeivel kapcsolatos információkat. Ez magában foglalja, hogy a kép Windows vagy Linux rendszerű, kibocsátási megjegyzések és minimális és maximális memóriára vonatkozó követelményeknek. Egy lemezkép definíciója. |
| **Lemezkép verziója** | Egy **lemezkép verziója** meg használni a virtuális gép létrehozása katalógus használata során. Kép különböző verzióinak rendelkezhet saját környezetéhez szükséges módon. Használata esetén, egy felügyelt rendszerképet, például egy **lemezkép verziója** hozhat létre virtuális Gépet, a rendszerkép verziószámát új lemezeket a virtuális gép létrehozásához használt. Lemezkép verziója többször is használható. |



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

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>További lépések
[Az Azure az Image Builder (előzetes verzió)](image-builder-overview.md) segítségével automatizálhatja a lemezkép-verzió létrehozását, még akkor is használhatja, frissíteni és [hozzon létre egy új lemezkép verziója egy meglévő kép verzióból](image-builder-gallery-update-image-version.md). 

Megosztott kép galéria-erőforrásai sablonok használatával is létrehozhat. Nincsenek elérhető számos Azure gyorsindítási sablonok: 

- [Hozzon létre egy megosztott lemezkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Kép definíció létrehozása egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Hozzon létre egy lemezkép verziója egy megosztott rendszerkép-katalógusában](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Lemezkép verziója egy virtuális gép létrehozása](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Megosztott kép katalógusokkal kapcsolatos további információkért lásd: a [áttekintése](shared-image-galleries.md). Ha problémákat tapasztal, tekintse meg [hibaelhárítás megosztott kép katalógusok](troubleshooting-shared-images.md).
