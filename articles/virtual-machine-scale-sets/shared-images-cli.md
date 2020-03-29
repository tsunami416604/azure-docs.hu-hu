---
title: Méretezési csoport létrehozása az Azure-ban megosztott virtuálisgép-lemezképek használatával
description: Ismerje meg, hogyan használhatja az Azure CLI-t megosztott virtuálisgép-lemezképek létrehozásához a virtuálisgép-méretezési csoportok Azure-beli üzembe helyezéséhez.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276272"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Megosztott lemezképek létrehozása és használata virtuálisgép-méretezési készletekhez az Azure CLI 2.0-s készletekkel

Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. [A megosztott képgalériák](shared-image-galleries.md) jelentősen leegyszerűsítik az egyéni képmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. A megosztott képtár lehetővé teszi, hogy az egyéni virtuálisgép-képeket megoszthatja a szervezet másaival, a régiókon belül vagy régiók között, egy AAD-bérlőn belül. Válassza ki, hogy mely képeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és kivel szeretné megosztani őket. Több galériát is létrehozhat, így logikusan csoportosíthatja a megosztott képeket. A katalógus egy legfelső szintű erőforrás, amely teljes szerepköralapú hozzáférés-vezérlést (RBAC) biztosít. A rendszerképek verziószámba állíthatók, és kiválaszthatja, hogy az egyes lemezképek verzióit az Azure-régiók egy másik készletére replikálja.Images can be versioned, and you can choose to replicate each image version to a different set of Azure regions. A galéria csak felügyelt képekkel működik. 

>[!NOTE]
> Ez a cikk végigvezeti az általános felügyelt lemezkép használatának folyamatán. A méretezési csoportok speciális virtuálisgép-rendszerképekből való létrehozása nem támogatott.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Méretezési csoport létrehozása az egyéni virtuálisgép-rendszerképből
Hozzon létre [`az vmss create`](/cli/azure/vmss#az-vmss-create)egy méretezési készletet a segítségével. A platform rendszerképe helyett (pl. *UbuntuLTS* vagy *CentOS*) adja meg az egyéni virtuálisgép-rendszerkép nevét. Az alábbi példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely az előző lépésben szereplő, *myImage* nevű egyéni rendszerképet használja:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a további erőforrások eltávolításához törölje az erőforráscsoportot és annak összes erőforrását az [az csoport törlésével.](/cli/azure/group) A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>További lépések

A megosztott képtár erőforrást sablonok használatával is létrehozhatja. Számos Azure-gyorsindítási sablon áll rendelkezésre: 

- [Megosztott képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Képdefiníció létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Képverzió létrehozása megosztott képtárban](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása a rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Ha bármilyen problémába ütközik, [elháríthatja a megosztott képgalériákat.](troubleshooting-shared-images.md)
