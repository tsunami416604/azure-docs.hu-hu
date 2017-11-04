---
title: "Az Azure CLI-parancsfájlt minták – hozzon létre egy felügyelt lemezes egy pillanatképből |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - pillanatképből kezelt lemez létrehozása"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ffdad7faa34fec09623a415664b5a260868e9dbc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>Hozzon létre egy felügyelt lemezes egy pillanatképből, a parancssori felület

Ez a parancsfájl egy felügyelt lemezes pillanatképet hoz létre. Segítségével szeretné visszaállítani a virtuális gép operációsrendszer- és lemezek pillanatkép-készítési. Hozzon létre az operációs rendszer és az adatok által kezelt lemezeken származó megfelelő, és hozzon létre egy új virtuális gép által kezelt lemezek csatolása. Visszaállíthatja a meglévő virtuális adatlemezek pillanatképeket készített adatlemezt csatolni is.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy pillanatképből kezelt lemez létrehozása. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az pillanatkép megjelenítése](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | Lekérdezi a az alábbi néven pillanatkép tulajdonságainak és a pillanatkép erőforráscsoport tulajdonságai. Felügyelt lemezes létrehozásához használt azonosító tulajdonsággal.  |
| [az lemez létrehozása](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Létrehoz egy kezelt lemez használatával pillanatkép felügyelt pillanatkép azonosítója |

## <a name="next-steps"></a>Következő lépések

[Virtuális gép létrehozása az operációs rendszer lemezeként felügyelt lemezes csatolásával](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép és a felügyelt lemezek CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
