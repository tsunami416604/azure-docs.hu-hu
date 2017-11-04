---
title: "Az Azure CLI parancsfájl minta - példány (áthelyezése) által kezelt lemezeken ugyanazon vagy másik előfizetésbe |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - példány (áthelyezése) által kezelt lemezeken ugyanazon vagy másik előfizetésbe"
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
ms.openlocfilehash: 8ff34f3d0b11c47f19205b92aebfc96e5cd5a014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Parancssori felület ugyanazon vagy másik előfizetés kezelt lemezek másolása

Ezt a parancsfájlt egy felügyelt lemezes másolja át az ugyanazon vagy másik előfizetésbe, de ugyanabban a régióban. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy új kezelt lemez létrehozása a célként megadott előfizetés azonosítóját felügyelt lemezt használja. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az lemez megjelenítése](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Lekérdezi a tulajdonságait az alábbi néven felügyelt lemezes és a kezelt lemez erőforráscsoport tulajdonságai. Azonosító tulajdonságot használja a felügyelt lemezes másolása másik előfizetést.  |
| [az lemez létrehozása](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Másolja a hozzon létre egy új felügyelt lemezes másik előfizetésben található azonosítójával lemez felügyelt, és elnevezése a szülő felügyelt lemezes.  |

## <a name="next-steps"></a>Következő lépések

[A felügyelt lemezes virtuális gép létrehozása](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További virtuális gép és a felügyelt lemezek CLI parancsfájl minták megtalálhatók a [Azure Linux virtuális dokumentációját](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
