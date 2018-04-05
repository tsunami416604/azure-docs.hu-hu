---
title: Pillanatkép készítése a virtuális merevlemez létrehozása az Azure-ban |} Microsoft Docs
description: 'Útmutató: virtuális merevlemez másolatának létrehozása az Azure biztonsági másolatot, vagy a problémák elhárításához.'
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
ms.date: 03/20/2018
ms.author: cynthn
ms.openlocfilehash: e5882b2ddc708544a7715da13c1f0d18384ce4e3
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása 

Pillanatkép készítése az operációs rendszer vagy az adatok lemez a biztonsági mentéshez, vagy a virtuális gép problémák hibaelhárítását. Pillanatkép virtuális merevlemez teljes, csak olvasható másolatát. 

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával 

A következő példa az Azure parancssori felület telepítve, és az Azure-fiókjával bejelentkezik 2.0 szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

A következő lépések bemutatják, hogyan érvénybe a pillanatkép használatával a `az snapshot create` parancsot a `--source-disk` paraméter. Az alábbi példa feltételezi, hogy van-e a virtuális gépek nevű `myVM` a a `myResourceGroup` erőforráscsoportot.

Beolvasni a lemezen.
```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Nevű pillanatkép *osDisk-biztonsági mentés*.

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Ha azt szeretné, a pillanatkép zóna rugalmas tárolás tárolni, olyan régióhoz, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `--sku Standard_ZRS` paraméter.

## <a name="use-azure-portal"></a>Az Azure-portál használata 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal felső sarokban kezdődően **hozzon létre egy erőforrást** keresse meg a **pillanatkép**.
3. A pillanatkép paneljén kattintson **létrehozása**.
4. Adjon meg egy **neve** a pillanatkép.
5. Válasszon ki egy létező [Erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. 
6. Válasszon egy Azure-adatközpontban helyét.  
7. A **forráslemez**, válassza ki a felügyelt lemezt pillanatkép.
8. Válassza ki a **fiók típusa** használni a pillanatkép tárolására. Ajánlott **Standard_LRS** kivéve, ha esetleg szükség lenne rá egy nagy teljesítményű lemezen tárolja.
9. Kattintson a **Create** (Létrehozás) gombra.


## <a name="next-steps"></a>További lépések

 Hozzon létre egy virtuális gépet egy pillanatképből kezelt lemez létrehozásához a pillanatképből majd lemezcsatlakoztatás az új felügyelt az operációs rendszer lemezeként. További információkért lásd: a [hozzon létre egy virtuális Gépet egy pillanatképből](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) parancsfájl.

