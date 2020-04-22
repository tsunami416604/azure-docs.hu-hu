---
title: Megosztott képgalériák létrehozása az Azure CLI-vel
description: Ebben a cikkben megtudhatja, hogyan használhatja az Azure CLI egy virtuális gép megosztott lemezkép ének létrehozásához az Azure-ban.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c75d33f8310cfd143bf201cdac861954d07baf0b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758426"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Megosztott képgaléria létrehozása az Azure CLI-vel

A [megosztott képtár](shared-image-galleries.md) leegyszerűsíti az egyéni képmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képtár lehetővé teszi, hogy az egyéni virtuálisgép-képeket megoszthatja a szervezet másaival, a régiókon belül vagy régiók között, egy AAD-bérlőn belül. Válassza ki, hogy mely képeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és kivel szeretné megosztani őket. Több galériát is létrehozhat, így logikusan csoportosíthatja a megosztott képeket. 

A katalógus egy legfelső szintű erőforrás, amely teljes szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A rendszerképek verziószámba állíthatók, és kiválaszthatja, hogy az egyes lemezképek verzióit az Azure-régiók egy másik készletére replikálja.Images can be versioned, and you can choose to replicate each image version to a different set of Azure regions. A galéria csak felügyelt képekkel működik.

A Megosztott képtár szolgáltatás több erőforrástípussal rendelkezik. Mi lesz használ vagy épület ezek ebben a cikkben:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt kép** | Ez egy alapkép, amely önmagában használható, vagy **képverzió** létrehozására használható egy képgalériában. Felügyelt lemezképek általános virtuális gépekből jönnek létre. A felügyelt lemezkép egy speciális típusú virtuális merevlemez, amely több virtuális gép létrehozásához használható, és most már használható megosztott lemezkép-verziók létrehozásához. |
| **Képgaléria** | Az Azure Marketplace-hez hasonlóan a **képgaléria** is egy tárház a képek kezeléséhez és megosztásához, de ön szabályozza, hogy ki rendelkezik hozzáféréssel. |
| **Képdefiníció** | A képek egy galérián belül vannak definiálva, és a képre és a belső használatra vonatkozó követelményekre vonatkozó információkat tartalmaznak. Ez magában foglalja, hogy a lemezkép Windows vagy Linux, kiadási megjegyzések, valamint minimális és maximális memóriakövetelmények. Ez egy fajta kép definíciója. |
| **Kép verziószáma** | A **lemezkép-verzió** az, amit a virtuális gép létrehozásához használ katalógus használatakor. A környezetnek szüksége van egy lemezkép több verziójára is. A felügyelt lemezképhez hasonlóan, amikor egy **lemezkép-verziót** használ a virtuális gép létrehozásához, a rendszerkép-verzió a virtuális gép új lemezeinek létrehozásához használatos. A képverziótöbbször is használható. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép a legújabb lemezkép-verzió segítségével [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Egy adott verziót is használhat a `--image` paraméter lemezkép-verzióazonosítójának használatával. Például a kép *1.0.0-s* verziójának használatához írja be a következőt: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>További lépések
[Az Azure Image Builder (előzetes verzió)](image-builder-overview.md) segítségével automatizálhatja a lemezképek verziólétrehozását, akár egy meglévő lemezkép-verzió frissítésére és [új lemezkép-verzió létrehozására](image-builder-gallery-update-image-version.md)is használható. 

A megosztott képtár erőforrásait sablonok használatával is létrehozhatja. Számos Azure-gyorsindítási sablon áll rendelkezésre: 

- [Megosztott képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Képdefiníció létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Képverzió létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása a rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képgalériákról további információt az [Áttekintés című témakörben talál.](shared-image-galleries.md) Ha problémákba ütközik, olvassa el A [megosztott képgalériák hibaelhárítása című témakört.](troubleshooting-shared-images.md)
