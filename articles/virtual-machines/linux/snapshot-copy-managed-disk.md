---
title: Pillanatkép létrehozása egy virtuális merevlemezről az Azure-ban
description: Megtudhatja, hogyan hozhat létre másolatot egy virtuális merevlemezről az Azure-ban biztonsági másolatként vagy hibaelhárítási problémákesetén.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f2d905d041198bd35c4657052bf18d981904dc44
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758392"
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása 

Készítsen pillanatképet egy operációs rendszerről vagy adatlemezről a biztonsági mentéshez, vagy hárítsa el a virtuális gépekkel kapcsolatos problémákat. A pillanatkép a virtuális merevlemez teljes, csak olvasható példánya. 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával 

A következő példa megköveteli, hogy [a Cloud Shell](https://shell.azure.com/bash) vagy az Azure CLI telepítve van.

A következő lépések bemutatják, hogyan készíthet pillanatképet az **az snapshot create** paranccsal a **--source-disk** paraméterrel. A következő példa feltételezi, hogy van egy virtuális gép nevű *myVM* a *myResourceGroup* erőforráscsoportban.

A lemezazonosító beszereznie az [vm show](/cli/azure/vm#az-vm-show)használatával.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Készítsen egy *osDisk-backup* nevű pillanatképet [az pillanatkép létrehozása segítségével.](/cli/azure/snapshot#az-snapshot-create)

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Ha a pillanatképet zóna-rugalmas tárolóban szeretné tárolni, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat,](../../availability-zones/az-overview.md) és tartalmazza a **--sku Standard_ZRS** paramétert.

A pillanatképek listáját az [az snapshot lista](/cli/azure/snapshot#az-snapshot-list)segítségével láthatja.

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal felső sarokban kezdődően kattintson az **Erőforrás létrehozása** elemre, és keressen **pillanatképet.** Válassza a **Pillanatkép** lehetőséget a keresési eredmények között.
3. A **Pillanatkép** panelen kattintson a **Létrehozás gombra.**
4. Adja meg a pillanatkép **nevét.**
5. Jelöljön ki egy meglévő erőforráscsoportot, vagy írja be egy új nevét. 
7. **Forráslemez**esetén jelölje ki a pillanatfelvételhez kívánt felügyelt lemezt.
8. Válassza ki a pillanatkép tárolásához használni kívánt **fióktípust.** Használja **a szabványos HDD-t,** kivéve, ha nagy teljesítményű SSD-n szeretné tárolni.
9. Kattintson **a Létrehozás gombra.**


## <a name="next-steps"></a>További lépések

 Hozzon létre egy virtuális gépet egy pillanatképből úgy, hogy létrehoz egy felügyelt lemezt a pillanatképből, majd csatolja az új felügyelt lemezt operációs rendszerlemezként. További információkért lásd: [Virtuális gép létrehozása pillanatkép-parancsfájlból.](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json)

