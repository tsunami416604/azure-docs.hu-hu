---
title: Hozzon létre egy pillanatképet egy VHD-t az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy virtuális merevlemez másolatának készítése az Azure-beli biztonsági mentése vagy kapcsolatos hibaelhárítás során.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 9fbbbb4f73b5295b648008878c8145fe926fbaad
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974391"
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása 

Pillanatkép készítése egy rendszer- és lemez, a biztonsági mentéshez vagy a virtuális gép hibáinak elhárítása. Pillanatkép egy virtuális merevlemez teljes, a csak olvasható példányát. 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával 

Az alábbi példa használatát igényli [Cloud Shell](https://shell.azure.com/bash) vagy az Azure CLI telepítve van.

A következő lépések bemutatják, hogyan egy pillanatkép segítségével a **az pillanatkép létrehozása** parancsot a **--forráslemez** paraméter. Az alábbi példa azt feltételezi, hogy nincs-e nevű virtuális gép *myVM* a a *myResourceGroup* erőforráscsoportot.

GET, a lemez azonosító használatával [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Pillanatkép készítése nevű *osDisk biztonsági mentési* használatával [az pillanatkép létrehozása](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Ha szeretné tárolni a pillanatkép zóna rugalmas tárolás, egy régióban, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a **--sku Standard_ZRS** paraméter.

A pillanatképek használatával listája látható [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Az Azure Portal használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal felső sarokban kezdődően **erőforrás létrehozása** és keressen rá a **pillanatkép**. Válassza ki **pillanatkép** a keresési eredmények közül.
3. Az a **pillanatkép** panelen kattintson a **létrehozás**.
4. Adjon meg egy **neve** elkészíteni a pillanatképet.
5. Válasszon ki egy meglévő erőforráscsoportot, vagy írjon be egy új nevet. 
7. A **forráslemez**, válassza ki a felügyelt lemez, pillanatkép.
8. Válassza ki a **fiók típusa** tárolja a pillanatkép. Használat **Standard HDD** , kivéve, ha szüksége lesz rá egy nagy teljesítményű SSD tárolja.
9. Kattintson a **Create** (Létrehozás) gombra.


## <a name="next-steps"></a>További lépések

 Virtuális gép létrehozása felügyelt lemez létrehozása pillanatképből a, és ezután a az operációsrendszer-lemezként az új felügyelt lemez csatolása egy pillanatképből. További információkért lásd: a [virtuális gép létrehozása pillanatképből](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) parancsfájlt.

