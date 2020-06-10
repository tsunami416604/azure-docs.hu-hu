---
title: Virtuális merevlemez pillanatképének létrehozása az Azure-ban
description: Megtudhatja, hogyan hozhat létre egy virtuális merevlemez másolatát az Azure-ban biztonsági mentés vagy hibaelhárítási problémák esetén.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7f4489b3073462a93319886ee5560ed5a6660111
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659963"
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása 

Készítsen pillanatképet az operációs rendszerről vagy az adatlemezről a biztonsági mentéshez vagy a virtuális gépekkel kapcsolatos hibák elhárításához. A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával 

Az alábbi példában a [Cloud Shell](https://shell.azure.com/bash) használatára van szükség, vagy az Azure CLI telepítve van.

A következő lépések bemutatják, hogyan készíthet pillanatképet az az **Snapshot Create** paranccsal a **--Source-Disk** paraméterrel. Az alábbi példa azt feltételezi, hogy létezik egy *myVM* nevű virtuális gép a *myResourceGroup* -erőforráscsoporthoz.

Töltse le a lemez AZONOSÍTÓját az [az VM show](/cli/azure/vm#az-vm-show)paranccsal.

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Hozzon létre egy *osDisk-Backup* nevű pillanatképet [az az Snapshot Create](/cli/azure/snapshot#az-snapshot-create)paranccsal.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Ha a pillanatképet a zóna rugalmas tárterületén szeretné tárolni, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat](../../availability-zones/az-overview.md) , és tartalmazza a **--SKU Standard_ZRS** paramétert.

A pillanatképek listáját az [az Snapshot List](/cli/azure/snapshot#az-snapshot-list)paranccsal tekintheti meg.

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal felső sarokban kattintson az **erőforrás létrehozása** elemre, és keresse meg a **pillanatképet**. Válassza a **Pillanatkép** lehetőséget a keresési eredmények közül.
3. A **Pillanatkép** panelen kattintson a **Létrehozás**gombra.
4. Adja meg a pillanatkép **nevét** .
5. Válasszon ki egy meglévő erőforráscsoportot, vagy írja be egy új nevet. 
7. A **forrásoldali lemez**lapon válassza ki a felügyelt lemezt a pillanatképhez.
8. Válassza ki a pillanatkép tárolására használni kívánt **fiókot** . A **standard HDD** csak akkor használja, ha nagy teljesítményű SSD-meghajtón tárolja.
9. Kattintson a **Létrehozás** lehetőségre.


## <a name="next-steps"></a>Következő lépések

 Hozzon létre egy virtuális gépet egy pillanatképből egy felügyelt lemez létrehozásával a pillanatképből, majd csatlakoztassa az új felügyelt lemezt az operációsrendszer-lemezként. További információ: [virtuális gép létrehozása pillanatkép-](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) parancsfájlból.

