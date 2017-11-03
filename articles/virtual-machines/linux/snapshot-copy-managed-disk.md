---
title: "Pillanatkép készítése a virtuális merevlemez létrehozása az Azure-ban |} Microsoft Docs"
description: "Útmutató: virtuális merevlemez másolatának létrehozása az Azure biztonsági másolatot, vagy a problémák elhárításához."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: da00c48f7da5a9be146f4fdb626c93db746c0f9b
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Pillanatkép létrehozása 

Pillanatkép készítése a biztonsági mentéshez vagy elhárítása a virtuális gép virtuális merevlemez állít egy operációs rendszer vagy az adatok lemezt. Pillanatkép virtuális merevlemez teljes, csak olvasható másolatát. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Készítsen pillanatképet az Azure CLI 2.0 segítségével

A következő példa az Azure parancssori felület telepítve, és az Azure-fiókjával bejelentkezik 2.0 szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

A következő lépések bemutatják, hogyan érvénybe a pillanatkép használatával a `az snapshot create` parancsot a `--source-disk` paraméter. Az alábbi példa feltételezi, hogy van-e a virtuális gépek nevű `myVM` a felügyelt operációsrendszer-lemezzel létrehozni a `myResourceGroup` erőforráscsoport.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

A kimeneti hasonlóan kell kinéznie:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Készítsen pillanatképet az Azure portál használatával 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal felső sarokban kezdődően **új** keresse meg a **pillanatkép**.
3. A pillanatkép paneljén kattintson **létrehozása**.
4. Adjon meg egy **neve** a pillanatkép.
5. Válasszon ki egy létező [Erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. 
6. Válasszon egy Azure-adatközpontban helyét.  
7. A **forráslemez**, válassza ki a felügyelt lemezt pillanatkép.
8. Válassza ki a **fiók típusa** használni a pillanatkép tárolására. Ajánlott **Standard_LRS** kivéve, ha esetleg szükség lenne rá egy nagy teljesítményű lemezen tárolja.
9. Kattintson a **Create** (Létrehozás) gombra.

Ha azt tervezi, a pillanatkép segítségével kezelt lemez létrehozása, és csatlakoztassa a virtuális gépek magas végrehajtása szükséges, használja a paraméterrel `--sku Premium_LRS` rendelkező a `az snapshot create` parancsot. Ez létrehoz a pillanatkép, prémium felügyelt lemezként tárolja. Prémium szintű felügyelt lemez jobb teljesítményt, mert SSD-meghajtót (SSD), de drágább, mint a Standard lemezek (HDD).


## <a name="next-steps"></a>Következő lépések

 Hozzon létre egy virtuális gépet egy pillanatképből kezelt lemez létrehozásához a pillanatképből majd lemezcsatlakoztatás az új felügyelt az operációs rendszer lemezeként. További információkért lásd: a [hozzon létre egy virtuális Gépet egy pillanatképből](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) parancsfájl.

